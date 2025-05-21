---
tags: [WebServer, HTTP, 정적웹페이지, Nginx, Apache]
aliases: [WS, 웹 서버, Web Server]
created: 2024-11-12 23:11
---

# 📘WS (Web Server)

흔히 웹 서버와 혼동되는 개념이 [[WAS (Web Application Server)]]이지만, 둘은 명확히 구분되는 역할을 수행합니다.

**웹 서버 (Web Server)** 는 클라이언트로부터 [[HTTP]] 요청을 받아들이고, 요청에 따라 HTML 문서, 이미지, CSS, JavaScript 등의 [[정적 웹 페이지 (Static Web Page)]]를 전달하는 역할을 합니다.

대표적인 웹 서버 제품으로는 **Nginx**, **Apache**, **IIS** 등이 있습니다.

![image.png](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250516071613_image.png)


---

## 웹 서버와 WAS의 주요 차이점

| 항목         | Web Server                              | WAS (Web Application Server)                        |
|--------------|------------------------------------------|-----------------------------------------------------|
| **처리 대상** | 정적 콘텐츠 (HTML, 이미지 등)              | 동적 콘텐츠 (JSP, Servlet 등)                        |
| **주요 기능** | 요청 라우팅, 캐싱, 로드 밸런싱             | 비즈니스 로직 처리, DB 연동, 세션 관리 등           |
| **응답 속도** | 정적 콘텐츠에 최적화되어 있어 빠름          | 동적 처리에 따라 상대적으로 느릴 수 있음             |

---

## 참고 자료

- [gmlwjd 블로그 - Web Server vs WAS](https://gmlwjd9405.github.io/2018/10/27/webserver-vs-was)

