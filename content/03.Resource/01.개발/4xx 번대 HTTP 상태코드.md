---
tags: [HTTP, 상태코드, 4xx, 클라이언트 오류, 인증, 권한]
aliases: [4xx 상태 코드, HTTP 4xx, 클라이언트 에러 코드]
created: 2024-10-06 22:10
---

# 📘4xx 상태 코드 (Client Error Responses)

**4xx번대 HTTP 상태 코드는 클라이언트 측 오류로 인해 요청이 실패했음을 의미**합니다.  
잘못된 문법, 인증 실패, 존재하지 않는 리소스 요청 등으로 인해 발생하며, **재시도해도 성공하지 않습니다.**

---

## 400 Bad Request

- 잘못된 문법 또는 파라미터로 인해 요청 자체가 유효하지 않음

```http
POST /users HTTP/1.1
Host: example.com
Content-Type: application/json

{
  "email": "b@example.com
",
  "username": "b.smith"
}
```

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "error": "Bad request",
  "message": "Request body could not be read properly."
}
```

---

## 401 Unauthorized

- 인증이 필요한 리소스에 대해 인증 정보가 없거나 잘못되었을 때 발생
- 응답에는 `WWW-Authenticate` 헤더 포함

```http
GET /admin HTTP/1.1
Host: example.com
```

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer
```

---

## 403 Forbidden

- 인증은 되었지만, 해당 리소스에 대한 **권한이 없음**
- 401 Unauthorized 와의 차이: 인증 유무가 아닌 **권한의 문제**

```http
DELETE /users/123 HTTP/1.1
Host: example.com
Authorization: Bearer abcd123
```

```http
HTTP/1.1 403 Forbidden
Content-Type: application/json

{
  "error": "InsufficientPermissions",
  "message": "Deleting users requires the 'admin' role."
}
```

---

## 404 Not Found

- 요청한 리소스를 서버에서 찾을 수 없음
- 오타, 삭제된 URL, 잘못된 리다이렉션 등에서 자주 발생

```http
GET /my-deleted-blog-post HTTP/1.1
Host: example.com
```

```http
HTTP/1.1 404 Not Found
Content-Type: text/html
```

---

## 405 Method Not Allowed

- 요청한 URL은 존재하지만, 허용되지 않은 HTTP Method를 사용한 경우 발생
- 응답에 `Allow` 헤더로 허용된 메서드 명시

```http
TRACE / HTTP/1.1
Host: example.com
```

```http
HTTP/1.1 405 Method Not Allowed
Allow: GET, POST, HEAD
```

---

## 참고

- [[HTTP 상태 코드]]
- [인프런 - HTTP 웹 네트워크](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC)
