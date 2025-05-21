---
tags: [쿠키, 인증, 클라이언트 저장소, 웹 스토리지, HTTP]
aliases: [Cookie, 쿠키란]
created: 2025-05-16
type: 기술정리
---

# 📘 쿠키 (Cookie)

**[[HTTP]]** 는 본래 [[무상태 프로토콜 (Stateless)]]이기 때문에 사용자의 상태 정보를 저장하지 않습니다. 이를 극복하기 위한 대표적인 방법 중 하나가 **쿠키(Cookie)** 입니다.

쿠키는 주로 클라이언트(브라우저) 측에 데이터를 저장하는 용도로 사용되며, 다음과 같은 3가지 목적에 주로 활용됩니다:

1. **세션 관리**  
   - 서버에 저장된 세션 ID, 장바구니 정보, 게임 스코어 등 상태 정보를 유지하기 위한 용도
2. **개인 설정 관리**  
   - 사용자가 설정한 테마, 로그인 ID, 최근 방문 페이지 등의 사용자 맞춤 정보를 저장
3. **트래킹 (Tracking)**  
   - 사용자의 행동을 추적하고 이를 분석하기 위한 용도 (예: 광고 클릭 이력 등)

---

## ✅ 쿠키와 최신 저장소 API

과거에는 클라이언트 측에 정보를 저장할 때 **쿠키**가 유일한 수단이었지만, 현재는 **Modern Storage API**(예: `localStorage`, `sessionStorage`, [[IndexedDB]])가 권장됩니다.

> 쿠키는 모든 HTTP 요청마다 자동으로 서버에 전송되므로, 특히 모바일 환경에서는 **네트워크 성능 저하**의 원인이 될 수 있습니다.

클라이언트 측 데이터 저장을 원한다면 아래 API를 사용하는 것이 더 안전하고 효율적입니다:

- [웹 스토리지 API (localStorage / sessionStorage)](https://developer.mozilla.org/ko/docs/Web/API/Web_Storage_API)
- [IndexedDB API](https://developer.mozilla.org/ko/docs/Web/API/IndexedDB_API)

---

## 🍪 쿠키 생성 방법

쿠키는 **[[HTTP|HTTP]] 응답의 헤더**에 `Set-Cookie` 항목을 포함시켜 클라이언트에게 전달됩니다.

```http
Set-Cookie: sessionId=abc123; Expires=Wed, 01 Jan 2025 00:00:00 GMT; HttpOnly; Secure
```

- 클라이언트는 이 정보를 기반으로 브라우저 내부에 쿠키를 저장합니다.
- 쿠키는 만료일(`Expires`) 또는 지속 시간(`Max-Age`)이 설정되며, 해당 시간이 지나면 자동으로 삭제됩니다.
- **보안을 위해**, 다음과 같은 속성을 함께 설정할 수 있습니다:
  - `HttpOnly`: JavaScript를 통한 접근을 차단
  - `Secure`: HTTPS 연결에서만 전송
  - `SameSite`: 교차 사이트 요청 시 쿠키 전송 제한

---

## ⏳ 쿠키의 라이프 사이클

쿠키는 설정 방식에 따라 생명 주기가 달라집니다:

### 1. 세션 쿠키 (Session Cookie)
- `Expires`나 `Max-Age` 속성이 **없는** 쿠키
- 브라우저를 종료하면 삭제됨
- 일부 브라우저는 세션 복원을 지원하여 쿠키가 복원될 수도 있음

### 2. 지속 쿠키 (Persistent Cookie)
- `Expires` 또는 `Max-Age` 속성이 명시된 쿠키
- 명시된 만료 시간까지 브라우저에 유지됨
- 사용자 기기 재시작 후에도 유지될 수 있음

```http
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2025 07:28:00 GMT;
```

> 쿠키의 라이프 사이클은 사용자 브라우저의 구현 방식에도 영향을 받으므로 테스트가 중요합니다.

---

## 🔐 쿠키와 보안 고려 사항

- 쿠키에는 **민감한 정보를 직접 저장하지 말아야 합니다.**
- HTTPS와 `HttpOnly`, `Secure`, `SameSite` 속성을 적극 활용해야 합니다.
- [[SESSION]] 또는 [[JWT(Json Web Token)|JWT]] 방식과 함께 사용될 경우 인증 시스템의 일부분으로 작동할 수 있습니다.

---

## 📌 쿠키 속성 상세 정리

| 속성 | 설명 |
|------|------|
| `Domain` | 쿠키가 전송될 도메인을 지정. 기본은 현재 도메인 |
| `Path` | 쿠키가 전송될 경로 범위 지정 (`/admin` 등) |
| `Expires` / `Max-Age` | 쿠키의 만료 시간 지정 |
| `HttpOnly` | JavaScript 접근 제한. XSS 공격 방어 |
| `Secure` | HTTPS에서만 쿠키 전송 허용 |
| `SameSite` | CSRF 방어용 속성: `Strict`, `Lax`, `None` 지원 |

---

## 🛡️ CSRF 방어와 SameSite 쿠키

`SameSite` 속성은 **CSRF(Cross-Site Request Forgery)** 공격을 방지하기 위해 사용됩니다.

- `Strict`: 외부 사이트 요청 시 쿠키를 절대 전송하지 않음
- `Lax`: GET 요청 등 일부 안전한 요청에만 쿠키 전송 허용
- `None`: 항상 쿠키 전송. 단 `Secure` 속성이 함께 있어야 함

> CSRF 방어를 위해 대부분의 인증 쿠키는 `SameSite=Lax` 또는 `Strict`를 사용해야 합니다.

---

## 🔁 쿠키 크기 제한

- **개별 쿠키는 4KB 이하**여야 하며,
- **도메인당 약 20개까지** 쿠키를 저장할 수 있습니다 (브라우저마다 다름)

> 너무 많은 정보를 쿠키에 저장하면 요청 헤더 크기가 증가하고, 성능 저하의 원인이 됩니다.

---

## 참고 문서

- [MDN - Web Storage API](https://developer.mozilla.org/ko/docs/Web/API/Web_Storage_API)  
- [MDN - IndexedDB API](https://developer.mozilla.org/ko/docs/Web/API/IndexedDB_API)
