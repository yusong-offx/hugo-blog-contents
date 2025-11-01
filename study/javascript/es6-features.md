---
title: "JavaScript ES6+"
date: 2025-11-01
draft: false
description: "모던 JavaScript 기능들"
tags: ["javascript", "ES6", "웹개발"]
categories: ["Study"]
authors: ["송유진"]
---

# JavaScript ES6+

최신 JavaScript의 주요 기능들을 알아봅니다.

## 화살표 함수

```javascript
// 기존 함수
function add(a, b) {
    return a + b;
}

// 화살표 함수
const add = (a, b) => a + b;
```

## 구조 분해 할당

```javascript
// 배열
const [first, second] = [1, 2, 3];

// 객체
const { name, age } = { name: "김철수", age: 25 };
```

## Promise와 async/await

```javascript
// Promise
fetch('/api/data')
    .then(response => response.json())
    .then(data => console.log(data));

// async/await
async function fetchData() {
    const response = await fetch('/api/data');
    const data = await response.json();
    return data;
}
```
