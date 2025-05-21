---
tags: [HTTP, 상태코드, 5xx, 서버 오류, Internal Server Error, Service Unavailable]
aliases: [5xx 상태 코드, HTTP 5xx, 서버 에러 코드]
created: 2024-10-07 23:10
---

# 📘5xx 상태 코드 (Server Error Responses)

**5xx번대 HTTP 상태 코드는 클라이언트의 요청이 유효함에도 불구하고, 서버의 문제로 인해 요청을 처리하지 못했음을 나타냅니다.**  
이 오류는 서버 내부의 예외, 과부하, 점검 등의 이유로 발생하며, 사용자에게는 일반적인 오류 메시지만 제공하는 것이 보안상 안전합니다.

---

## 500 Internal Server Error

- 서버 내부에서 예기치 못한 오류가 발생했을 때 사용
- 구체적인 오류는 응답에 포함하지 않으며, **서버 로그**에 기록되어야 함

```http
GET /highlights HTTP/1.1
Host: example.com
User-Agent: curl/8.6.0
Accept: */*
```

```http
HTTP/1.1 500 Internal Server Error
Content-Type: text/html
Content-Length: 123

<!doctype html>
<html>
<head><title>500 Internal Server Error</title></head>
<body>
  <h1>Internal Server Error</h1>
  <p>The server was unable to complete your request. Please try again later.</p>
  <p>If this problem persists, please <a href="https://example.com/support">contact support</a>.</p>
</body>
</html>
```

---

## 503 Service Unavailable

- 서버가 일시적으로 요청을 처리할 수 없는 상태
- 주로 **과부하**, **점검 중**, **리소스 부족** 등의 상황에서 발생
- `Retry-After` 헤더를 통해 재시도 가능한 시간 명시 가능

```http
GET /highlights HTTP/1.1
Host: example.com
User-Agent: curl/8.6.0
Accept: */*
```

```http
HTTP/1.1 503 Service Unavailable
Content-Type: text/html
Retry-After: 120

<!doctype html>
<html>
<head><title>503 Service Unavailable</title></head>
<body>
  <h1>503 Service Unavailable</h1>
  <p>The server was unable to complete your request. Please try again later.</p>
  <p>If this problem persists, please <a href="https://example.com/support">contact support</a>.</p>
</body>
</html>
```

---

## 기타 상태 코드 (예시만 나열)

| 코드                  | 설명                               |
| ------------------- | -------------------------------- |
| 501 Not Implemented | 서버가 요청을 처리할 수 있는 기능을 지원하지 않음     |
| 502 Bad Gateway     | 게이트웨이나 프록시 서버가 잘못된 응답을 수신        |
| 504 Gateway Timeout | 게이트웨이 또는 프록시 서버가 시간 내에 응답을 받지 못함 |

---

## 참고 자료

- [[HTTP 상태 코드]]
- [인프런 - HTTP 웹 네트워크](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC)
