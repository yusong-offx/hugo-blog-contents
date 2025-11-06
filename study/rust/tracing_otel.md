---
title: "Rust: OpenTelemetry와 Tracing 통합 예제" 
date: "2025-11-06T13:45:00+09:00" 
draft: false 
description: "Rust에서 opentelemetry, tracing, tokio를 함께 사용하여 분산 추적을 설정하는 방법 예제입니다." 
tags: ["Rust", "OpenTelemetry", "Tracing", "Observability"]
---


# Rust에서 OpenTelemetry와 Tracing 통합하기

다음은 Rust에서 opentelemetry와 tracing 라이브러리를 tokio 런타임과 함께 사용하여 분산 추적(distributed tracing)을 설정하는 전체 예제 코드입니다.이 코드는 표준 출력(stdout)으로 트레이스 정보를 내보내는 간단한 파이프라인을 설정합니다.

```rust
use opentelemetry::global;
use opentelemetry::trace::TracerProvider as _;
use opentelemetry_sdk::propagation::TraceContextPropagator;

use opentelemetry_sdk::trace as sdktrace;
use std::time::Duration;
use tracing::{Instrument, span};
use tracing::{Level, info, instrument, warn};
use tracing_subscriber::{Registry, layer::SubscriberExt};

/// OTel 파이프라인을 설정하고 Tracing과 연결합니다.
fn init_tracing() -> sdktrace::SdkTracerProvider {
    // log 크레이트 로그를 tracing으로 브릿지(이미 설정되어 있으면 무시)
    let _ = tracing_log::LogTracer::builder()
        .with_max_level(log::LevelFilter::Info)
        .init();

    // 1) stdout(터미널) 트레이스 익스포터
    let exporter = opentelemetry_stdout::SpanExporter::default();

    // 2) TracerProvider + Batch Span Processor (Tokio 런타임 사용)
    let provider = sdktrace::SdkTracerProvider::builder()
        .with_batch_exporter(exporter)
        .build();

    // 3) 글로벌 등록 (TraceContext 전파자 포함)
    global::set_tracer_provider(provider.clone());
    global::set_text_map_propagator(TraceContextPropagator::new());

    // 4) tracing_subscriber에 OTel 레이어 연결
    let tracer = provider.tracer("ll-trace-server");
    let telemetry_layer = tracing_opentelemetry::layer().with_tracer(tracer);

    let subscriber = Registry::default()
        .with(telemetry_layer)
        .with(tracing_subscriber::fmt::layer());
    let _ = tracing::subscriber::set_global_default(subscriber);

    provider // main에서 flush/종료용으로 반환
}

#[instrument(fields(task_id = 42))] // 이 함수 자체가 스팬이 됨
async fn my_async_task() {
    span!(Level::DEBUG, "비동기 작업 시작...");
    tokio::time::sleep(Duration::from_millis(5)).await;

    // #[instrument] 덕분에 이 스팬은 my_async_task의 자식 스팬이 됨
    {
        let child_span = span!(Level::DEBUG, "child_task");
        // let _enter = child_span.enter();

        warn!("자식 작업 수행 중!");
        tokio::time::sleep(Duration::from_millis(10))
            .instrument(child_span.clone())
            .await;
        info!("자식 작업 완료.");
    }
}

#[tokio::main]
async fn main() {
    // (1) 트레이싱 설정 초기화
    let provider = init_tracing();

    // (2) 스팬과 이벤트 발생시키기
    info!(user = "alice", "메인 함수 시작");

    my_async_task().await;

    info!(user = "bob", "메인 함수 종료");

    // (3) OTel 데이터 전송 완료까지 대기 (flush)
    let result = provider.force_flush();
    println!("end : {result:?}");
}
```