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

### 실습 예제

PostgreSQL WAL에 Pulsar CDC 커넥터를 연결하여 실시간 변경 이벤트를 추적하는 예제 프로젝트입니다.

- [cdc-tracer](https://github.com/yusong-offx/cdc-tracer): PostgreSQL WAL + Pulsar CDC 커넥터 테스트 레포지토리


커넥터는 Debezium 포맷을 유지하며 `key`에는 기본 키, `value`에는 `before/after` 필드가 포함된 JSON 이벤트를 기록합니다. 스키마 레지스트리를 사용하면 스키마 변경도 자동 추적할 수 있습니다.

## 3. ScyllaDB CDC 연동

[ScyllaDB CDC Source Connector](https://docs.scylladb.com/manual/stable/using-scylla/integrations/scylla-cdc-source-connector.html)




## 참고

- [debezium PostgreSQL Connector Documentation](https://debezium.io/documentation/reference/stable/connectors/postgresql.html)
- [ScyllaDB CDC Documentation](https://docs.scylladb.com/manual/stable/features/cdc/index.html)
- [Pulsar IO Connectors](https://pulsar.apache.org/docs/en/io-connectors/)
