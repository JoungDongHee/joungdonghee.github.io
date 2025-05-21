---
tags: [WAS, WebServer, SpringBoot, Tomcat, 아키텍처, 웹서버]
aliases: [WAS, 웹 애플리케이션 서버, Web Application Server]
created: 2024-11-05 23:11
---

# 📘WAS (Web Application Server)

**[[WAS (Web Application Server)]]** 는 [[WS (Web Server)]]와 동일하게 [[HTTP]]를 기반으로 동작합니다.  
웹 서버가 담당하던 기능 중 일부를 수행하며, 클라이언트에게 [[동적 웹 페이지 (Dynamic Web Page)]]를 전달할 수 있습니다.

대표적인 WAS 제품으로는 **Tomcat**, **JBoss**, **Jetty**, **WebSphere** 등이 있습니다.

![image.png](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250516071510_image.png)

---

## WAS와 WS를 함께 사용하는 이유

많은 웹 아키텍처에서는 가장 앞단에 **Nginx**와 같은 [[WS (Web Server)]]를 두고, 그 뒤에 **Tomcat**과 같은 [[WAS (Web Application Server)]]를 두는 구조를 사용합니다.  
이러한 구조를 사용하는 이유는 다음과 같습니다:

1. **역할 분리**  
   웹 서버는 정적 파일(이미지, HTML, CSS, JS 등)을 효율적으로 처리하고, WAS는 동적 요청을 처리합니다. 이를 통해 각각의 역할을 최적화할 수 있습니다.

2. **로드 밸런싱 및 프록시 기능 활용**  
   Nginx 같은 웹 서버는 로드 밸런싱과 리버스 프록시 기능을 통해 트래픽 분산 및 보안 향상에 기여합니다.

3. **확장성**  
   웹 서버와 WAS를 분리함으로써 각 컴포넌트를 독립적으로 수평 확장할 수 있어 시스템의 유연성과 확장성이 향상됩니다.

---

## 추가 설명: Spring Boot의 WAS 내장

[[Spring Boot]]는 자체적으로 WAS 기능(Tomcat 등)을 내장한 프레임워크입니다.  
따라서 별도의 WAS 설치 없이도 HTTP 요청을 처리할 수 있으며, 설정을 통해 Jetty나 Undertow로 변경할 수도 있습니다.

---

## 참고 자료

- [Wishket 기술 매거진 - 웹 서버와 WAS의 차이](https://yozm.wishket.com/magazine/detail/1780/)
- [gmlwjd 블로그 - Web Server vs WAS](https://gmlwjd9405.github.io/2018/10/27/webserver-vs-was)

