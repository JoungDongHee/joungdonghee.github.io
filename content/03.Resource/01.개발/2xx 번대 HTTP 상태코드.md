---
tags: [HTTP, 상태코드, 2xx, 성공응답, API응답]
aliases: [2xx 상태 코드, HTTP 2xx, 성공 응답 코드]
created: 2024-10-06 22:10
---

# 📘2xx 상태 코드 (Successful Responses)

**2xx번대 상태 코드는 클라이언트의 요청이 성공적으로 처리되었음을 의미**합니다.  
주로 [[REST API]] 응답에서 사용되며, 아래와 같은 상황을 설명합니다:

---

## 200 OK

요청이 성공적으로 처리되었음을 나타냅니다.

```http
POST /subscribe HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded

name=Brian%20Smith&email=brian.smith%40example.com
```

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "message": "User subscription pending. A confirmation email has been sent.",
  "subscription": {
    "name": "Brian Smith",
    "email": "brian.smith@example.com",
    "id": 123,
    "feed": "default"
  }
}
```

---

## 201 Created

요청이 성공적으로 처리되어 **새로운 리소스가 생성**되었음을 의미합니다.  
서버는 `Location` 헤더를 통해 생성된 리소스의 URI를 제공할 수 있습니다.

```http
POST /users HTTP/1.1
Host: example.com
Content-Type: application/json

{
  "firstName": "Brian",
  "lastName": "Smith",
  "email": "brian.smith@example.com"
}
```

```http
HTTP/1.1 201 Created
Content-Type: application/json
Location: http://example.com/users/123

{
  "message": "New user created",
  "user": {
    "id": 123,
    "firstName": "Brian",
    "lastName": "Smith",
    "email": "brian.smith@example.com"
  }
}
```

---

## 202 Accepted

요청이 수신되었지만 아직 처리되지 않았음을 의미합니다.  
**비동기 처리**나 **배치 작업** 등에 사용됩니다. 성공이나 실패가 확정된 상태는 아닙니다.

```http
POST /tasks HTTP/1.1
Host: example.com
Content-Type: application/json

{
  "task": "emailDogOwners",
  "template": "pickup"
}
```

```http
HTTP/1.1 202 Accepted
Content-Type: application/json

{
  "message": "Request accepted. Starting to process task.",
  "taskId": "123",
  "monitorUrl": "http://example.com/tasks/123/status"
}
```

---

## 204 No Content

요청이 성공적으로 처리되었지만 반환할 콘텐츠가 없음을 의미합니다.  
자주 사용되는 예는 **DELETE 요청 후 응답**입니다.

```http
DELETE /image/123 HTTP/1.1
Host: example.com
Authorization: Bearer 1234abcd
```

```http
HTTP/1.1 204 No Content
Content-Length: 0
```

---

## 그 외

다양한 2xx 상태 코드가 존재하지만, 일반적으로는 위의 네 가지가 실무에서 가장 자주 사용됩니다.

---

## 참고

- [MDN - HTTP 2xx Status Codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/200)
- [인프런 - HTTP 웹 네트워크](https://www.inflearn.com/course/http-웹-네트워크)
