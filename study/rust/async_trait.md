---
title : Understanding Async Traits in Rust
date : 2025-11-07T12:00:00+09:00
description : A comprehensive guide to using async traits in Rust, including practical examples and best practices.
tags : ["Rust", "Async", "Traits", "Programming"]
---

🎯 `async_trait`이 필요한 이유: Rust의 '아직 안 되는' 그것

정확히 짚으셨습니다. `async` 문법 자체는 언어에 포함되어 있죠. (Rust 1.39부터\!)

핵심적인 이유는, 2025년 11월 현재 **안정화된(stable) Rust 컴파일러가 트레잇(trait) 내부에서 `async fn`을 직접 지원하지 않기 때문**입니다.

`async_trait`은 바로 그 구멍을 메워주는 '필수 유틸리티'입니다.

### 🧐 뭐가 문제인데요?

말씀하신 대로, 일반 함수는 `async`가 아주 잘 됩니다.

```rust
// 이건 아주 잘 됩니다 (OK)
async fn regular_function() {
    // ... 비동기 작업 ...
}
```

하지만 이 `async fn`을 트레잇(trait) 정의 안에 넣으려고 하면, 컴파일러가 화를 냅니다.

```rust
trait MyService {
    // 이건 컴파일 에러! (stable 버전 기준)
    // "async functions are not currently supported in traits"
    async fn do_something(&self) -> u32;
}
```

### 🪄 `async_trait`이 하는 일

`async_trait`은 영리한 \*\*프로시저럴 매크로(procedural macro)\*\*입니다. 우리가 `#[async_trait]` 어트리뷰트를 붙이면, 컴파일 타임에 이 코드를 컴파일러가 이해할 수 있는 형태로 '번역'해줍니다.

**[우리가 작성하는 코드]**

```rust
use async_trait::async_trait;

#[async_trait]
trait MyService {
    async fn do_something(&self) -> u32;
}
```

**[컴파일 시 `async_trait`이 변환하는 코드 (개념적)]**
`async_trait`은 `async fn`을 `Box`에 담긴 `Future`를 반환하는 일반 함수로 바꿔치기합니다.

```rust
trait MyService {
    // 실제로는 더 복잡하지만, 핵심 아이디어는 이렇습니다.
    fn do_something(&self) -> std::pin::Pin<Box<dyn std::future::Future<Output = u32> + Send + '_>>;
}
```

`async fn` 자체가 `Future`를 반환하는 함수의 '문법 설탕(syntax sugar)'인데, `async_trait`은 이 '설탕'을 트레잇에서도 쓸 수 있도록, 조금 더 복잡한 `Box<dyn Future>`(동적 디스패치) 형태로 풀어주는 역할을 하는 셈이죠.

### 요약

1.  **맞습니다:** Rust는 `async` 문법을 지원합니다. (런타임은 `tokio`, `async-std` 등이 제공)
2.  **하지만:** 이 `async` 문법을 `trait` 정의 안에서는 (아직) 쓸 수 없습니다.
3.  **그래서:** `async_trait` 매크로가 이 '설탕'을 컴파일러가 알아듣는 `Box<dyn Future>` 코드로 변환해주는 '번역기' 역할을 합니다.

이 기능(`async fn in traits`)은 Rust 언어 차원에서 활발히 개발 중이라, 언젠가는 `async_trait` 크레이트가 필요 없어질 날이 올 겁니다.
