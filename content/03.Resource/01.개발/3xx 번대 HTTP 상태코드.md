---
tags: [HTTP, 상태코드, 3xx, 리다이렉션, Redirection, URL 이동]
aliases: [3xx 상태 코드, HTTP 3xx, 리다이렉트 상태 코드]
created: 2024-10-06 22:35
---

# 📘3xx 상태 코드 (Redirection)

**3xx번대 HTTP 상태 코드는 요청이 성공적으로 수신되었으나, 클라이언트가 요청을 완료하기 위해 추가적인 작업(주로 리다이렉션)을 해야 함을 의미**합니다.

대표적으로 `Location` 헤더를 포함한 응답을 통해 클라이언트가 **다른 URL로 이동**해야 하는 경우 사용됩니다.

---

## 📌 영구 리다이렉션 (Permanent Redirect)

특정 리소스가 **영구적으로 새로운 URL로 이동**되었음을 의미합니다.

### 301 Moved Permanently

^e78e5d

- 리소스가 완전히 다른 URL로 이동
- 클라이언트는 **GET 방식으로 재요청**하며 본문은 제거됨

```http
POST /old-event HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded

name=hello&age=20
```

```http
HTTP/1.1 301 Moved Permanently
Location: /new-event
```

→ 이후 클라이언트는 `GET /new-event`로 자동 전환됨  

![301 예시](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250516072574_image.png)

---

### 308 Permanent Redirect

^5e2972

- 301과 동일한 의미지만, **요청 메서드 및 본문을 유지**함
- 클라이언트는 **POST → POST**, **PUT → PUT** 등으로 유지

![308 예시](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250516072689_image.png)

---

## 📌 일시 리다이렉션 (Temporary Redirect)

리소스가 **일시적으로 다른 URL로 이동**되었을 때 사용됩니다.

### 302 Found

^ef7060

- 요청된 리소스가 일시적으로 다른 URI에 존재
- **GET 방식으로 전환**, 본문 제거됨
- 301과 유사하지만 임시적 의미

---

### 303 See Other

- 302와 동일하게 동작
- 주로 **POST 후 리다이렉션 시 GET으로 전환하여 다른 리소스를 보이기 위해 사용**

---

### 307 Temporary Redirect

^555c5f

- **요청 메서드와 본문을 유지한 채** 리다이렉션
- 예: POST 요청 시 307 응답을 받으면 클라이언트는 그대로 POST 방식으로 새로운 URI에 요청

---

## 📌 특수 리다이렉션

- 캐시된 결과를 사용하거나, 리소스 이동 이외의 목적을 가질 수 있습니다 (예: 프로토콜 변경 등)

---

## 참고 자료

- [인프런 - HTTP 웹 네트워크 강의](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC)
- [[HTTP 상태 코드]]
