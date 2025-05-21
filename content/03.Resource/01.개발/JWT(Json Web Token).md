---
tags: [JWT, 인증, 보안, 토큰, Web Token]
aliases: [Json Web Token, JWT란, JWT 토큰]
date: 2024-12-23 16:12
author: Joung Dong Hee
---

# 📘JWT (Json Web Token)

**[[JWT(Json Web Token)|JWT]]** 는 비연결성(Connectionless)을 가진 [[HTTP|HTTP]] 환경에서 사용자를 인증하기 위한 대표적인 방식 중 하나입니다. 인증 방식에는 [[SESSION]] 기반과 토큰 기반 방식이 있으며, JWT는 토큰 기반 인증에서 널리 사용됩니다.

JWT는 **Header (헤더)**, **Payload (내용)**, **Signature (서명)** 으로 구성된 문자열이며, 각 부분은 `.`으로 구분됩니다.

```text
eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJKb2UifQ.1KP0SsvENi7Uz1oQc07aXTL7kpQG5jBNIybqr60AlD4
```

---

## HEADER

헤더에는 서명 알고리즘과 토큰의 타입을 명시합니다. 예시:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

JJWT 라이브러리가 지원하는 알고리즘은 다음과 같습니다:

| Identifier | Signature Algorithm |
|------------|---------------------|
| `HS256`    | HMAC using SHA-256  |
| `HS384`    | HMAC using SHA-384  |
| `HS512`    | HMAC using SHA-512  |
| `ES256`    | ECDSA using P-256 and SHA-256 |
| `ES384`    | ECDSA using P-384 and SHA-384 |
| `ES512`    | ECDSA using P-521 and SHA-512 |
| `RS256`    | RSASSA-PKCS-v1_5 using SHA-256 |
| `RS384`    | RSASSA-PKCS-v1_5 using SHA-384 |
| `RS512`    | RSASSA-PKCS-v1_5 using SHA-512 |
| `PS256`    | RSASSA-PSS using SHA-256 and MGF1 with SHA-256 |
| `PS384`    | RSASSA-PSS using SHA-384 and MGF1 with SHA-384 |
| `PS512`    | RSASSA-PSS using SHA-512 and MGF1 with SHA-512 |
| `EdDSA`    | Edwards-curve Digital Signature Algorithm |

공식 문서: [https://github.com/jwtk/jjwt](https://github.com/jwtk/jjwt)

---

## PAYLOAD

Payload에는 토큰에 포함할 데이터를 정의할 수 있으며, 이를 **클레임(claim)** 이라 부릅니다. 클레임은 `key: value` 형태의 쌍으로 구성됩니다.

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1516239022
}
```

클레임의 종류는 다음과 같습니다:

### 1. 등록된 (Registered) 클레임

미리 정의된 클레임으로, 예약어와 같은 성격을 가집니다:

- `iss`: 발급자 (issuer)
- `sub`: 제목 (subject)
- `aud`: 대상자 (audience)
- `exp`: 만료 시간 (expiration) — *NumericDate 형식 사용*
- `nbf`: Not Before — 토큰이 활성화될 시간
- `iat`: 발급 시간 (issued at)
- `jti`: 고유 식별자 (JWT ID) — 중복 처리 방지용

### 2. 공개 (Public) 클레임

이름 충돌을 방지하기 위해 URL 형식으로 작성해야 합니다.

```json
{
  "https://example.com/role": "admin"
}
```

### 3. 비공개 (Private) 클레임

서버-클라이언트 간 협의된 정보로 사용자 정의 데이터를 포함할 수 있습니다.

```json
{
  "username": "test"
}
```

---

## SIGNATURE

서명(Signature)은 `Header`와 `Payload`를 인코딩한 후, 비밀 키와 함께 지정된 알고리즘을 통해 생성됩니다.

```text
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret
)
```

이 서명 덕분에 토큰의 무결성을 검증할 수 있습니다.

---

## JWT의 장점

1. 서버가 세션 상태를 유지할 필요가 없으므로 리소스 소비가 적습니다.
2. 서버 간 인증 정보 공유가 필요할 때 유리합니다. (예: [[MSA|MSA 환경]])
3. 확장성 있는 인증 구조를 구축할 수 있습니다.

---

## JWT의 단점 및 주의사항

1. **보안 문제**  
   - Payload는 **Base64 인코딩**일 뿐, 암호화가 아닙니다. 민감한 정보(비밀번호 등)는 포함시키지 말아야 합니다.
2. **탈취 위험**  
   - 토큰이 탈취되면 만료 전까지 차단이 어렵습니다. 이를 보완하기 위해:
     - **블랙리스트** 관리
     - **짧은 만료 시간** 설정
     - **Refresh Token** 활용
3. **크기 문제**  
   - 세션 쿠키에 비해 토큰 크기가 크므로 네트워크 부담이 증가할 수 있습니다.

---

## 참고 자료

- [JWT 공식 사이트](https://jwt.io/)
- [JJWT 공식 GitHub](https://github.com/jwtk/jjwt)
- [벨로퍼트 블로그](https://velopert.com/2389)
