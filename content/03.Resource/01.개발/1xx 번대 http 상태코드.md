---
tags:
  - HTTP
  - 상태코드
  - 1xx
  - 정보응답
  - 프로토콜
aliases:
  - 1xx 상태 코드
  - HTTP 1xx
  - Informational Response
created: 2024-10-06 22:10
share: true
---

# 📘1xx 상태 코드 (Informational Response)

**1xx 번대 상태 코드는 요청이 수신되어 처리 중임을 나타내는 정보 응답 코드**입니다.  
주로 클라이언트와 서버 간의 통신 초기 과정에서 사용되며, 최종 응답이 아닙니다.  
실제로는 거의 사용되지 않거나 클라이언트(브라우저)가 이를 자동 처리하기 때문에 사용자에게는 잘 노출되지 않습니다.

---

## 주요 코드

### 100 Continue

- 클라이언트가 요청의 일부를 서버에 전송했으며, 나머지를 계속 보내도 된다는 응답
- 대용량 데이터를 전송하기 전, 헤더만 보내고 서버의 승인을 기다릴 때 사용됨

### 101 Switching Protocols

- 클라이언트가 요청한 프로토콜로 변경 중임을 나타냄
- 예: HTTP에서 WebSocket으로 업그레이드 요청

### 102 Processing (WebDAV)

- 서버가 요청을 수신했으며, 아직 응답을 생성하지 않았음을 의미 (RFC 2518)

---

## 요약

| 상태 코드 | 의미                                | 설명 |
|-----------|-------------------------------------|------|
| 100       | Continue                            | 요청의 나머지를 계속 보내도 됨 |
| 101       | Switching Protocols                 | 프로토콜 전환 중 |
| 102       | Processing                          | 서버가 요청을 처리 중 (WebDAV용) |

---

## 참고

- [[HTTP 상태 코드]]
- [MDN Web Docs - HTTP 1xx](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status#information_responses)

