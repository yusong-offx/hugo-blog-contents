---
title: "Keycloak Basic"
description: "Keycloak 기본 개념"
date: 2025-11-01
tags: ["keycloak", "identity", "authentication"]
---

# Keycloak Basic

## 개요

Keycloak은 오픈 소스 아이덴티티 및 액세스 관리 솔루션으로, 애플리케이션과 서비스에 대한 싱글 사인온(SSO), 사용자 관리, 소셜 로그인 등을 제공합니다. Keycloak은 다양한 프로토콜(OAuth2, OpenID Connect, SAML 등)을 지원하며, 확장성과 유연성을 갖추고 있어 다양한 환경에서 활용할 수 있습니다.


## 주요 API

```
{
    "issuer": "http://localhost:18080/realms/ll-trace",
    "authorization_endpoint": "http://localhost:18080/realms/ll-trace/protocol/openid-connect/auth",
    "token_endpoint": "http://localhost:18080/realms/ll-trace/protocol/openid-connect/token",
    "introspection_endpoint": "http://localhost:18080/realms/ll-trace/protocol/openid-connect/token/introspect",
    "userinfo_endpoint": "http://localhost:18080/realms/ll-trace/protocol/openid-connect/userinfo",
    "end_session_endpoint": "http://localhost:18080/realms/ll-trace/protocol/openid-connect/logout",
    "frontchannel_logout_session_supported": true,
    "frontchannel_logout_supported": true,
    "jwks_uri": "http://localhost:18080/realms/ll-trace/protocol/openid-connect/certs",
    "check_session_iframe": "http://localhost:18080/realms/ll-trace/protocol/openid-connect/login-status-iframe.html",
    "grant_types_supported": [
        "authorization_code",
        "implicit",
        "refresh_token",
        "password",
        "client_credentials",
        "urn:openid:params:grant-type:ciba",
        "urn:ietf:params:oauth:grant-type:device_code"
    ],
    "acr_values_supported": [
        "0",
        "1"
    ],
    "response_types_supported": [
        "code",
        "none",
        "id_token",
        "token",
        "id_token token",
        "code id_token",
        "code token",
        "code id_token token"
    ],
    "subject_types_supported": [
        "public",
        "pairwise"
    ],
    "id_token_signing_alg_values_supported": [
        "PS384",
        "RS384",
        "EdDSA",
        "ES384",
        "HS256",
        "HS512",
        "ES256",
        "RS256",
        "HS384",
        "ES512",
        "PS256",
        "PS512",
        "RS512"
    ],
    "id_token_encryption_alg_values_supported": [
        "RSA-OAEP",
        "RSA-OAEP-256",
        "RSA1_5"
    ],
    "id_token_encryption_enc_values_supported": [
        "A256GCM",
        "A192GCM",
        "A128GCM",
        "A128CBC-HS256",
        "A192CBC-HS384",
        "A256CBC-HS512"
    ],
    "userinfo_signing_alg_values_supported": [
        "PS384",
        "RS384",
        "EdDSA",
        "ES384",
        "HS256",
        "HS512",
        "ES256",
        "RS256",
        "HS384",
        "ES512",
        "PS256",
        "PS512",
        "RS512",
        "none"
    ],
    "userinfo_encryption_alg_values_supported": [
        "RSA-OAEP",
        "RSA-OAEP-256",
        "RSA1_5"
    ],
    "userinfo_encryption_enc_values_supported": [
        "A256GCM",
        "A192GCM",
        "A128GCM",
        "A128CBC-HS256",
        "A192CBC-HS384",
        "A256CBC-HS512"
    ],
    "request_object_signing_alg_values_supported": [
        "PS384",
        "RS384",
        "EdDSA",
        "ES384",
        "HS256",
        "HS512",
        "ES256",
        "RS256",
        "HS384",
        "ES512",
        "PS256",
        "PS512",
        "RS512",
        "none"
    ],
    "request_object_encryption_alg_values_supported": [
        "RSA-OAEP",
        "RSA-OAEP-256",
        "RSA1_5"
    ],
    "request_object_encryption_enc_values_supported": [
        "A256GCM",
        "A192GCM",
        "A128GCM",
        "A128CBC-HS256",
        "A192CBC-HS384",
        "A256CBC-HS512"
    ],
    "response_modes_supported": [
        "query",
        "fragment",
        "form_post",
        "query.jwt",
        "fragment.jwt",
        "form_post.jwt",
        "jwt"
    ],
    "registration_endpoint": "http://localhost:18080/realms/ll-trace/clients-registrations/openid-connect",
    "token_endpoint_auth_methods_supported": [
        "private_key_jwt",
        "client_secret_basic",
        "client_secret_post",
        "tls_client_auth",
        "client_secret_jwt"
    ],
    "token_endpoint_auth_signing_alg_values_supported": [
        "PS384",
        "RS384",
        "EdDSA",
        "ES384",
        "HS256",
        "HS512",
        "ES256",
        "RS256",
        "HS384",
        "ES512",
        "PS256",
        "PS512",
        "RS512"
    ],
    "introspection_endpoint_auth_methods_supported": [
        "private_key_jwt",
        "client_secret_basic",
        "client_secret_post",
        "tls_client_auth",
        "client_secret_jwt"
    ],
    "introspection_endpoint_auth_signing_alg_values_supported": [
        "PS384",
        "RS384",
        "EdDSA",
        "ES384",
        "HS256",
        "HS512",
        "ES256",
        "RS256",
        "HS384",
        "ES512",
        "PS256",
        "PS512",
        "RS512"
    ],
    "authorization_signing_alg_values_supported": [
        "PS384",
        "RS384",
        "EdDSA",
        "ES384",
        "HS256",
        "HS512",
        "ES256",
        "RS256",
        "HS384",
        "ES512",
        "PS256",
        "PS512",
        "RS512"
    ],
    "authorization_encryption_alg_values_supported": [
        "RSA-OAEP",
        "RSA-OAEP-256",
        "RSA1_5"
    ],
    "authorization_encryption_enc_values_supported": [
        "A256GCM",
        "A192GCM",
        "A128GCM",
        "A128CBC-HS256",
        "A192CBC-HS384",
        "A256CBC-HS512"
    ],
    "claims_supported": [
        "aud",
        "sub",
        "iss",
        "auth_time",
        "name",
        "given_name",
        "family_name",
        "preferred_username",
        "email",
        "acr"
    ],
    "claim_types_supported": [
        "normal"
    ],
    "claims_parameter_supported": true,
    "scopes_supported": [
        "openid",
        "roles",
        "profile",
        "acr",
        "email",
        "basic",
        "offline_access",
        "phone",
        "web-origins",
        "address",
        "microprofile-jwt"
    ],
    "request_parameter_supported": true,
    "request_uri_parameter_supported": true,
    "require_request_uri_registration": true,
    "code_challenge_methods_supported": [
        "plain",
        "S256"
    ],
    "tls_client_certificate_bound_access_tokens": true,
    "revocation_endpoint": "http://localhost:18080/realms/ll-trace/protocol/openid-connect/revoke",
    "revocation_endpoint_auth_methods_supported": [
        "private_key_jwt",
        "client_secret_basic",
        "client_secret_post",
        "tls_client_auth",
        "client_secret_jwt"
    ],
    "revocation_endpoint_auth_signing_alg_values_supported": [
        "PS384",
        "RS384",
        "EdDSA",
        "ES384",
        "HS256",
        "HS512",
        "ES256",
        "RS256",
        "HS384",
        "ES512",
        "PS256",
        "PS512",
        "RS512"
    ],
    "backchannel_logout_supported": true,
    "backchannel_logout_session_supported": true,
    "device_authorization_endpoint": "http://localhost:18080/realms/ll-trace/protocol/openid-connect/auth/device",
    "backchannel_token_delivery_modes_supported": [
        "poll",
        "ping"
    ],
    "backchannel_authentication_endpoint": "http://localhost:18080/realms/ll-trace/protocol/openid-connect/ext/ciba/auth",
    "backchannel_authentication_request_signing_alg_values_supported": [
        "PS384",
        "RS384",
        "EdDSA",
        "ES384",
        "ES256",
        "RS256",
        "ES512",
        "PS256",
        "PS512",
        "RS512"
    ],
    "require_pushed_authorization_requests": false,
    "pushed_authorization_request_endpoint": "http://localhost:18080/realms/ll-trace/protocol/openid-connect/ext/par/request",
    "mtls_endpoint_aliases": {
        "token_endpoint": "http://localhost:18080/realms/ll-trace/protocol/openid-connect/token",
        "revocation_endpoint": "http://localhost:18080/realms/ll-trace/protocol/openid-connect/revoke",
        "introspection_endpoint": "http://localhost:18080/realms/ll-trace/protocol/openid-connect/token/introspect",
        "device_authorization_endpoint": "http://localhost:18080/realms/ll-trace/protocol/openid-connect/auth/device",
        "registration_endpoint": "http://localhost:18080/realms/ll-trace/clients-registrations/openid-connect",
        "userinfo_endpoint": "http://localhost:18080/realms/ll-trace/protocol/openid-connect/userinfo",
        "pushed_authorization_request_endpoint": "http://localhost:18080/realms/ll-trace/protocol/openid-connect/ext/par/request",
        "backchannel_authentication_endpoint": "http://localhost:18080/realms/ll-trace/protocol/openid-connect/ext/ciba/auth"
    },
    "authorization_response_iss_parameter_supported": true
}
```

이 문서는 OpenID Connect(OIDC) Discovery Document (또는 .well-known/openid-configuration)입니다.

간단히 말해, 이 JSON 파일은 **Keycloak Realm(영역)이 제공하는 OIDC/OAuth 2.0 서비스의 '설정 메뉴판' 또는 '서비스 맵(지도)'**입니다.

애플리케이션(클라이언트)이 이 Realm과 통신(로그인, 토큰 요청 등)을 하려고 할 때, 필요한 모든 URL 엔드포인트와 **서버가 지원하는 기능(암호화 방식, 스코프 등)**을 이 파일 하나로 파악할 수 있습니다.

주요 항목들을 그룹별로 나누어 설명해 드리겠습니다.

📍 1. 핵심 엔드포인트 (가장 중요한 주소들)

애플리케이션이 실제로 통신해야 하는 핵심 URL 주소들입니다.

    issuer: "발급자". 이 Realm의 고유 식별자입니다. 토큰(JWT)의 iss 필드에 이 값이 들어갑니다.

    authorization_endpoint: (가장 중요) 사용자를 로그인시키기 위해 리디렉션해야 하는 바로 그 주소입니다. (지난 질문에 대한 답입니다.)

    token_endpoint: 로그인 성공 후 받은 '인증 코드(code)'를 '액세스 토큰(access token)'과 교환하기 위해, 애플리케이션 서버가 Keycloak 서버에 요청하는 주소입니다. (서버 간 통신)

    userinfo_endpoint: 애플리케이션이 액세스 토큰을 사용해 사용자의 상세 정보(이메일, 이름 등)를 요청할 수 있는 주소입니다.

    end_session_endpoint: 사용자를 로그아웃시키기 위해 리디렉션하는 주소입니다.

    jwks_uri: 토큰이 정말 이 Keycloak 서버에서 발급된 것이 맞는지 서명을 검증할 때 필요한 공개 키(Public Key) 목록을 제공하는 주소입니다.

🛠️ 2. 지원 기능 (Realm의 능력치)

이 Keycloak Realm이 어떤 표준 기능들을 지원하는지 알려줍니다.

    grant_types_supported: 토큰을 발급받을 수 있는 방법들입니다.

        authorization_code: (가장 표준적인) 로그인 페이지를 통한 인증 코드 방식

        refresh_token: 만료된 액세스 토큰을 새로 고침하는 방식

        password: (권장되지 않음) 사용자의 아이디/비밀번호로 직접 토큰을 받는 방식

        client_credentials: (M2M) 기계(서버) 간 인증에 사용되는 방식

    response_types_supported: authorization_endpoint에 요청 시 어떤 응답을 받을 수 있는지 명시합니다. (code가 표준입니다.)

    scopes_supported: 클라이언트가 사용자에게 요청할 수 있는 정보의 범위(Scope) 목록입니다.

        openid: (필수) OIDC 인증을 사용하겠다는 의미입니다.

        profile: 이름, 프로필 사진 등 기본 프로필 정보를 요청합니다.

        email: 이메일 정보를 요청합니다.

    claims_supported: userinfo 엔드포인트나 토큰에서 제공받을 수 있는 사용자 정보의 종류(예: name, email 등)입니다.

🔐 3. 보안 및 암호화 설정 (어떻게 통신을 보호하는가?)

통신 과정에서 사용되는 다양한 암호화 및 서명 알고리즘 목록입니다.

    id_token_signing_alg_values_supported: ID 토큰을 서명할 때 사용하는 알고리즘 목록입니다. (예: RS256)

    token_endpoint_auth_methods_supported: token_endpoint에 요청할 때, 클라이언트(애플리케이션) 자신을 인증하는 방법입니다. (예: client_secret_basic - ID/Secret 사용)

    code_challenge_methods_supported: PKCE 지원 여부입니다. S256이 지원되므로, 모바일 앱이나 SPA(싱글 페이지 앱)에서 보안성을 높일 수 있습니다.

    (기타 ..._alg_..., ..._enc_... 값들): 토큰, 사용자 정보, 요청 객체 등을 암호화하거나 서명할 때 지원하는 다양한 알고리즘 목록을 의미합니다.

🔄 4. 기타 고급 기능

표준 기능 외에 Keycloak이 제공하는 추가적인 엔드포인트와 기능들입니다.

    introspection_endpoint: 토큰을 Keycloak 서버에 보내 이 토큰이 아직 유효한지(active) 검사할 수 있는 주소입니다.

    revocation_endpoint: 액세스 토큰이나 리프레시 토큰을 **강제로 만료(폐기)**시킬 때 사용하는 주소입니다.

    backchannel_logout_...: "백채널 로그아웃" 관련 설정입니다. (예: Keycloak에서 로그아웃 시, 해당 사용자가 로그인했던 모든 앱에 서버 간 통신으로 로그아웃을 알림)

    pushed_authorization_request_endpoint (PAR): authorization_endpoint로 긴 요청 파라미터를 보내기 전에, 서버 간 통신으로 미리 요청을 "푸시"하여 보안성을 높이는 고급 기능입니다.

요약

이 파일은 **"Keycloak Realm 'll-trace' 사용 설명서"**이며, OIDC/OAuth 2.0 라이브러리(예: spring-boot-starter-oauth2-client, keycloak.js 등)는 이 파일의 주소만 알면 필요한 모든 설정을 자동으로 완료합니다.

개발자 입장에서는 이 파일에서 **authorization_endpoint**와 token_endpoint 같은 핵심 주소들을 확인하고, 내 애플리케이션이 scopes_supported에 있는 스코프를 올바르게 요청하고 있는지 확인하는 용도로 주로 사용합니다.