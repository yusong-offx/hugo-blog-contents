---
title: "Apache Pulsar CDC로 ScyllaDB와 PostgreSQL 동기화하기"
date: 2025-11-02T00:00:00+09:00
draft: false
categories:
		- study
		- infra
tags:
		- pulsar
		- cdc
		- scylladb
		- postgresql
		- wal
		- data-pipeline
description: "Apache Pulsar CDC 커넥터를 활용해 ScyllaDB CDC 로그와 PostgreSQL WAL을 스트리밍하고, 다중 시스템 간 데이터를 실시간으로 동기화하는 방법"
---

## 1. Pulsar CDC 개요

Apache Pulsar는 **Storage + Compute 분리 구조**와 **다중 테넌시**를 지원하는 스트리밍 플랫폼으로, Pulsar IO 커넥터를 통해 다양한 CDC(Change Data Capture) 소스의 로그를 실시간으로 수집할 수 있습니다. 특히 PostgreSQL의 WAL(Write-Ahead Log)과 ScyllaDB의 CDC 로그를 직접 읽어 표준 Pulsar 토픽으로 내보낼 수 있어, 서로 다른 데이터베이스 간 동기화 파이프라인을 간결하게 구축할 수 있습니다.

Pulsar CDC 흐름은 아래와 같이 구성됩니다.

1. **Source 커넥터**가 데이터베이스의 변경 로그(WAL, CDC Log)를 구독
2. 변경 이벤트를 JSON/Avro 스키마로 정규화한 뒤 Pulsar 토픽에 전송
3. **Sink 커넥터** 또는 커스텀 소비자가 이벤트를 받아 타 시스템에 반영
4. Pulsar의 구독 모델(Shared, KeyShared 등)을 활용해 부하 분산과 순서 보장 전략을 선택

## 2. PostgreSQL WAL 수집

Pulsar는 Debezium 기반의 `pulsar-io-debezium-postgres` 소스 커넥터를 제공합니다. 이 커넥터는 PostgreSQL의 WAL을 logical decoding 슬롯을 통해 스트리밍하고, 테이블 단위로 변경 이벤트를 발행합니다.

### 준비 사항

- PostgreSQL 10 이상, logical replication 활성화
- `wal_level = logical`, `max_replication_slots` 및 `max_wal_senders` 적절히 설정
- Pulsar 클러스터 및 `pulsar-admin` CLI 접근 권한

### 커넥터 설정 예시

```bash
pulsar-admin sources create \
	--archive connectors/pulsar-io-debezium-postgres-3.1.1.nar \
	--tenant study \
	--namespace infra \
	--name postgres-cdc \
	--destination-topic-name persistent://study/infra/postgres-wal \
	--parallelism 1 \
	--source-config '{
		"database.hostname": "postgres.staging.svc",
		"database.port": "5432",
		"database.user": "cdc_user",
		"database.password": "***",
		"database.dbname": "app",
		"database.server.name": "postgres-app",
		"slot.name": "pulsar_slot",
		"plugin.name": "pgoutput",
		"schema.include.list": "public",
		"table.include.list": "public.orders,public.order_items",
		"snapshot.mode": "initial",
		"pulsar.service.url": "pulsar://broker-0.pulsar.svc:6650"
	}'
```

커넥터는 Debezium 포맷을 유지하며 `key`에는 기본 키, `value`에는 `before/after` 필드가 포함된 JSON 이벤트를 기록합니다. 스키마 레지스트리를 사용하면 스키마 변경도 자동 추적할 수 있습니다.

## 3. ScyllaDB CDC 연동

ScyllaDB는 4.0 이상에서 CDC를 제공하여 테이블 별 변경 로그를 스트리밍할 수 있습니다. Pulsar에서는 두 가지 접근이 일반적입니다.

- **Scylla CDC Pulsar Source**: `scylla-cdc-java` 기반 오픈소스 커넥터를 사용하여 CDC 로그 테이블을 읽고 Pulsar 토픽으로 전송
- **Kafka 호환 모드 활용**: Scylla CDC → Kafka Connect → Pulsar Kafka-on-Pulsar(GoP) 브릿지를 사용하는 방식 (운영 간소화를 위해 전자를 추천)

### 커넥터 설정 예시 (scylla-cdc-source)

```bash
pulsar-admin sources create \
	--archive connectors/pulsar-io-scylla-cdc-1.0.0.nar \
	--tenant study \
	--namespace infra \
	--name scylla-orders-cdc \
	--destination-topic-name persistent://study/infra/scylla-orders \
	--parallelism 2 \
	--source-config '{
		"scylla.contact.points": "scylla-0.scylla.svc:9042,scylla-1.scylla.svc:9042",
		"scylla.keyspace": "app",
		"scylla.table": "orders",
		"scylla.username": "cdc_user",
		"scylla.password": "***",
		"pulsar.service.url": "pulsar://broker-0.pulsar.svc:6650",
		"cursor.initial": "latest"
	}'
```

Scylla CDC 이벤트는 파티션 키 기준으로 정렬된 스트림이므로 Pulsar 토픽을 KeyShared 모드로 구독하면 순서를 유지하며 확장이 가능합니다.

## 4. 타겟 시스템 동기화 전략

- sink 단에서 멱등 처리를 위해 이벤트 헤더의 `eventType`(create/update/delete)와 `op_ts`(operation timestamp)를 활용하거나, Pulsar 메시지 ID를 별도 테이블에 저장해 재처리를 방지합니다.

- 해당 schema를 읽거나 해서 원하는 방향으로 데이터 처리를 할 수 있습니다.

## 참고

- [debezium PostgreSQL Connector Documentation](https://debezium.io/documentation/reference/stable/connectors/postgresql.html)
- [ScyllaDB CDC Documentation](https://docs.scylladb.com/manual/stable/features/cdc/index.html)
- [Pulsar IO Connectors](https://pulsar.apache.org/docs/en/io-connectors/)
