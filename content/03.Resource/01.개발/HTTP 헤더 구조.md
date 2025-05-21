---
tags: [HTTP, Header, HTTP 헤더, 요청 헤더, 응답 헤더, RFC723x]
aliases: [HTTP Header, HTTP 헤더 구조, 요청 응답 헤더]
created: 2024-11-03 22:11
author: Joung Dong Hee
---

# HTTP Header

**HTTP 헤더(Header)** 는 클라이언트(**요청**)와 서버(**응답**) 간에 **부가적인 정보를 주고받기 위한 메타데이터**입니다.

- 대소문자를 구분하지 않습니다 (`Host`, `host`, `HOST`는 동일)
- 필요 시 임의의 헤더를 정의하여 사용할 수 있습니다
- 다양한 표준이 존재하며, [[RFC723x]]가 최신 기준입니다

![Http Header.png](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250516072061_image.png)

---

## 📥 Request Headers

클라이언트 → 서버 요청 시 포함되는 정보

### `Host`

- 요청 대상의 도메인 이름 또는 IP 주소
- 하나의 IP에 여러 도메인을 연결할 수 있는 가상 호스팅 환경에서 필수

### `User-Agent`

- 클라이언트의 소프트웨어 식별자
- 브라우저 종류, OS 버전 등의 정보가 포함되어 서버는 클라이언트 환경을 파악할 수 있음

### `Accept`

- 클라이언트가 원하는 응답 콘텐츠 유형 명시 (예: `application/json`, `text/html`)

### `Accept-Language`

- 클라이언트가 선호하는 언어 설정 (예: `ko-KR`, `en-US`)

---

## 📤 Response Headers

서버 → 클라이언트 응답 시 포함되는 정보

### `Content-Type`

- 응답 본문의 MIME 타입 지정 (예: `text/html`, `application/json`)

### `Content-Length`

- 응답 본문의 크기 (바이트 단위)

### `Location`

- [[리다이렉션(Redirection)]] 응답 시 새로운 리소스의 위치

### `Server`

- 서버 소프트웨어 정보 (예: `nginx/1.18.0`, `Apache/2.4.41`)

### `Set-Cookie`

- 클라이언트에게 [[쿠키(Cookie)]] 설정을 지시

---

## 📦 General Headers

요청/응답 공통으로 사용되는 헤더로, 메시지 자체의 속성을 설명

- `Date`: 메시지 생성 시각
- `Transfer-Encoding`: 메시지 전송 형식 (`chunked` 등)
- `Connection`, `Cache-Control` 등

---

## 참고

- [[HTTP]]
- [MDN - HTTP Headers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers)
