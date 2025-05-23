---
tags: [HTTP, 상태코드, 응답코드, HTTP Status Code]
aliases: [HTTP 상태 코드, HTTP 응답 코드]
created: 2024-10-06 21:10
---

# HTTP의 상태 코드

[[HTTP|HTTP]]에서 중요한 속성 중 하나는 **상태 코드(Status Code)** 입니다.  

**HTTP 상태 코드는 클라이언트가 서버에 요청을 보낸 후, 그 요청이 어떻게 처리되었는지를 알려주는 숫자 코드**입니다.  
이를 통해 클라이언트는 요청이 성공했는지, 오류가 있었는지, 또는 추가 조치가 필요한지를 파악할 수 있습니다.

상태 코드는 다음과 같이 5개의 범주로 나뉩니다:

1. [[1xx 번대 http 상태코드]] – **정보 응답(Informational)**  
   요청을 받았으며 작업을 계속 진행 중임

2. [[2xx 번대 HTTP 상태코드]]– **성공(Success)**  
   요청이 정상적으로 처리됨

3. [[3xx 번대 http 상태코드]] – **리다이렉션(Redirection)**  
   클라이언트가 요청을 완료하려면 추가 조치가 필요함

4. [[4xx 번대 http 상태코드]] – **클라이언트 오류(Client Error)**  
   잘못된 요청 또는 인증/권한 문제 등

5. [[5xx 번대 http 상태코드]] – **서버 오류(Server Error)**  
   유효한 요청을 처리하지 못한 서버의 문제

각 범주의 상세한 상태 코드는 연결된 문서를 참고하세요.
