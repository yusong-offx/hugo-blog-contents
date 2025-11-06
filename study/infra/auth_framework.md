---
title: "Keycloak 대체재 탐방기 (Feat. Go, OpenFGA)"
date: 2025-11-06T10:44:00+09:00
draft: false
tags: ["Auth", "DevOps", "Go", "Keycloak", "ZITADEL", "OpenFGA", "Casdoor"]
summary: "Java 기반 Keycloak의 무거움에 지쳐 Go 기반의 가벼운 AuthN/AuthZ 솔루션을 찾아 나선 여정. ZITADEL, Casdoor, 그리고 OpenFGA를 비교합니다."
---

# Keycloak이 너무 무거워서 갈아타기 (AuthN/AuthZ 프레임워크 탐색기)

## Keycloak, 고마웠지만 우리 헤어지자

Keycloak은 훌륭한 오픈소스 IAM(Identity and Access Management)입니다. 기능만 보면 '올인원(All-in-One)' 끝판왕이죠. OIDC, OAuth 2.0, SAML, LDAP 연동, 소셜 로그인... 없는 게 없습니다.

**하지만, Java(정확히는 Quarkus/WildFly) 기반입니다.**

> keycloak이 java로 작성되어 있어 리소스 대비 성능이 좋지않은 편인듯 하여 대체재를 찾아보았습니다.

이 한 문장으로 모든 게 설명됩니다. 특히 리소스가 제한적인 개발 환경이나 쿠버네티스(K8s) 환경에서 Keycloak을 띄울 때의 느낌은...

* **부팅 속도:** "커피 한 잔 내리고 오겠습니다." (Pod 재시작이 두렵습니다)
* **메모리 점유율:** "내가 이 구역의 힙스터(Heap-ster)다."

그래서 'Go로 짜인 가볍고 빠른 녀석'을 찾아보기로 했습니다.

## 1차 후보: Go 기반의 '올인원' 솔루션

Keycloak처럼 관리자 UI까지 다 갖춰진 올인원 솔루션으로 두 가지가 물망에 올랐습니다. 둘 다 백엔드는 Go로 작성되었습니다.

1.  **ZITADEL:** 클라우드 네이티브 환경에 맞게 설계되었고, 아키텍처가 독특합니다.
2.  **Casdoor:** Go + React 기반. UI가 깔끔하고 Keycloak의 핵심 기능을 잘 구현해 뒀습니다.

## 2. 진짜 요구사항: Authorization은 OpenFGA로!

그런데 여기서 중요한 전제 조건이 하나 생겼습니다. 단순히 "Keycloak 대체"가 아니라 아키텍처를 분리하고 싶었습니다.

> **"권한(Authorization) 관리는 OpenFGA를 쓰고 싶다."**

OpenFGA는 CNCF 프로젝트로, Google의 Zanzibar 논문을 기반으로 한 강력한 ReBAC(관계 기반 접근 제어) 시스템입니다. "객체 하나하나" 세밀하게 권한을 제어하는 데 특화되어 있죠.

* `user:anne`은 `document:123`의 `viewer`이다.
* `document:123`의 `editor`는 `viewer`의 권한을 포함한다.

이런 "객체와 관계" 중심의 제어(ReBAC)가 필요했습니다.

이 결정 하나로 두 후보(ZITADEL, Casdoor)를 바라보는 시각이 완전히 달라졌습니다. 왜냐하면...

* **Casdoor**는 내장 권한 모델로 **Casbin** (RBAC/ABAC)을 씁니다.
* **ZITADEL**은 내장 권한 모델로 **Zanzibar (ReBAC)**를 씁니다.

## 3. ZITADEL vs Casdoor: OpenFGA 파트너로 누가 더 적합한가?

OpenFGA를 쓴다는 것은, 두 솔루션의 '내장 권한 관리' 기능은 쓰지 않겠다는 의미입니다. 즉, 이 대결은 **"누가 더 훌륭한 인증(Authentication) 서버 및 사용자 관리 도구인가?"**로 좁혀집니다.

| 비교 항목 | 🏛️ ZITADEL |  pragmatic  pragmatic Casdoor |
| :--- | :--- | :--- |
| **권한 철학** | **Zanzibar (ReBAC)** | **Casbin (RBAC/ABAC)** |
| **아키텍처** | **Event Sourcing (ES) / CQRS** | 전통적인 CRUD |
| **OpenFGA와의 궁합** | **최상.** (같은 Zanzibar 사상 공유) | **보통.** (서로 다른 철학) |
| **안정성 (감사)** | **극강.** (모든 변경 이력은 '이벤트') | **양호.** (전통적인 CRUD 로그) |

### ZITADEL이 끌리는 이유: 철학의 통일과 아키텍처

#### 1. 철학의 통일 (Zanzibar)

이게 결정적이었습니다.

* 내가 쓸 **OpenFGA** (Zanzibar 기반)
* 내가 쓸 **ZITADEL** (Zanzibar 기반)

비록 내장 권한 모델을 안 쓰더라도, 인증 서버(ZITADEL)와 권한 서버(OpenFGA)가 '관계(Relation)', '객체(Object)', '사용자(User)'를 바라보는 **데이터 모델의 사상이 동일**합니다. 이건 연동 시 개념적 마찰이 없다는 뜻입니다.

반면 Casdoor는 Casbin(정책, 주체, 액션) 기반이라 생각의 흐름이 다릅니다.

#### 2. 아키텍처 (Event Sourcing)

"성능"과 "안정성"에서 ZITADEL이 앞서는 지점입니다.

* **Casdoor**는 전통적인 **CRUD** 방식입니다. DB의 `user` 테이블을 `UPDATE`하죠. 직관적이지만 과거 이력을 잃기 쉽습니다.
* **ZITADEL**은 '은행 장부' 같은 **이벤트 소싱(Event Sourcing)** 방식입니다.
    * 사용자가 비번을 바꾸면 `UPDATE`를 하는 게 아니라, `"user.password.changed"`라는 **이벤트(사건)를 그냥 기록(Log)**합니다. 데이터가 수정되는 게 아니라 *추가*될 뿐입니다.
    * **장점:** 완벽한 감사 추적(Audit Trail)이 가능합니다. "3주 전 그 사용자의 상태"를 100% 재현할 수 있습니다. 이는 "성능"만큼이나 "안정성"을 중요하게 생각하는 제게 큰 매력이었습니다.

## 4. 결론: ZITADEL (AuthN) + OpenFGA (AuthZ)

Casdoor는 "가벼운 Keycloak이 필요한데, Casbin도 좋아!"라는 시나리오에선 최고의 선택일 수 있습니다.

하지만 저의 요구사항은 달랐습니다.

* **인증(AuthN):** 가볍고(Go), 안정적이며(Event Sourcing), 감사 추적이 완벽한 것.
* **권한(AuthZ):** 객체 레벨의 세밀한 제어가 가능한 것 (ReBAC).

이 두 가지를 만족시키는 조합은 **ZITADEL (인증) + OpenFGA (권한)**였습니다.

Keycloak의 무거움에서 벗어나, Go 기반의 가벼움과 클라우드 네이티브 아키텍처의 이점을 모두 챙기는 구성을 찾은 것 같아 만족스럽습니다. 이제... 설치하러 가봐야겠네요.