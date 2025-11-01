---
title: "Rust 시작하기"
date: 2025-11-01
draft: false
description: "Rust 프로그래밍 입문"
tags: ["rust", "시스템프로그래밍"]
categories: ["Study"]
authors: ["송유진"]
---

# Rust 시작하기

안전하고 빠른 시스템 프로그래밍 언어 Rust를 학습합니다.

## Hello World

```rust
fn main() {
    println!("Hello, World!");
}
```

## 변수와 불변성

```rust
fn main() {
    let x = 5;  // 불변 변수
    let mut y = 10;  // 가변 변수
    
    y = 15;  // OK
    // x = 10;  // 에러!
}
```

## 소유권 (Ownership)

Rust의 가장 독특한 특징인 소유권 시스템에 대해 알아봅니다.
