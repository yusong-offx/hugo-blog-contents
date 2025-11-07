---
title: Why gRPC-Web is Impossible
date: 2025-11-7T10:00:00+09:00
description: An exploration of the technical challenges that make gRPC-Web an impractical solution for modern web applications.
tags: ["gRPC-Web", "Web Development", "Technical Challenges"]
---

# Why gRPC-Web is Impossible

브라우저의 정책상 허용된 외부통신이외에는 허락하지않는다.   
브라우저 환경에서 사용할려면 gRPC-Web layer (proxy)를 두고 사용해야한다.  
WASM 환경또한 샌드박스이기 때문에 동일한 브라우저 정책을 적용받는다.
