---
tags: [HTTP, 리다이렉션, Redirection, 상태코드, 이동, Location 헤더]
aliases: [Redirection, HTTP 리다이렉션, URL 이동]
created: 2024-11-03 18:11
author: Joung Dong Hee
---

# 리다이렉션 (Redirection)

**리다이렉션(Redirection)** 은 클라이언트가 요청한 리소스가 더 이상 해당 URL에 없고, 다른 URL로 이동했을 경우, **자동으로 다른 위치로 이동시키는 기술**입니다.

이는[[HTTP 상태 코드 개요|HTTP 상태 코드]]중 **3xx** 계열 상태 코드(예: 301, 302, 307 등)와 함께 `Location` 헤더를 통해 동작합니다.

---

## 📌 리다이렉션 동작 예시

```plaintext
1. 클라이언트 → 서버 요청
   GET /doc HTTP/1.1

2. 서버 응답
   HTTP/1.1 301 Moved Permanently
   Location: /doc_new

3. 클라이언트는 응답의 Location을 기반으로 다시 요청
   GET /doc_new HTTP/1.1

4. 서버는 새로운 리소스에 대해 응답
   HTTP/1.1 200 OK
```

![리다이렉션 시퀀스](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250516072499_image.png)

---

## 📖 참고할 상태 코드

아래는 대표적인 3xx 상태 코드입니다. 각 코드의 상세 설명은 [[3xx 번대 http 상태코드]] 문서를 참고하세요.

| 상태 코드              | 설명                                |
|-----------------------|-------------------------------------|
| 301 Moved Permanently | 영구 이동, 이후 요청은 새로운 URL로 |
| 302 Found             | 임시 이동, 메서드가 GET으로 변경됨  |
| 307 Temporary Redirect| 임시 이동, 메서드와 본문 유지       |
| 308 Permanent Redirect| 영구 이동, 메서드와 본문 유지       |

---

## 🔗 참고 링크

- [MDN - Redirections](https://developer.mozilla.org/ko/docs/Web/HTTP/Redirections)
- [인프런 - HTTP 웹 네트워크 강의](https://www.inflearn.com/course/http-웹-네트워크)
