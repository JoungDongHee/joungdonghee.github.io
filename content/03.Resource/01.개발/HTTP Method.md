---
tags: [HTTP, Method, REST API, 웹 통신, HTTP 메서드, 멱등성, 안전성]
aliases: [HTTP Method, HTTP 메서드]
created: 2024-10-14 23:10
---

# 📘HTTP Method

HTTP에서 Method는 클라이언트가 서버에 요청을 보낼 때 **요청의 목적을 정의하는 수단**입니다.  
이를 통해 [[REST API]]를 설계하고, API의 기능(조회, 생성, 수정, 삭제 등)을 명확하게 나타낼 수 있습니다.

HTTP Method에는 GET, POST, PUT, PATCH, DELETE, HEAD, OPTIONS, CONNECT, TRACE 등이 있으며,  
이 중 실무에서 가장 자주 사용되는 메서드는 다음 다섯 가지입니다:

- **GET**
- **POST**
- **PUT**
- **PATCH**
- **DELETE**

---

## 개념 요약

- **멱등성 (Idempotency)**:  
  동일한 요청을 여러 번 수행해도 서버의 상태가 동일하게 유지되는 성질.  
  → GET, PUT, DELETE는 멱등성을 가짐

- **안전성 (Safety)**:  
  서버의 상태를 변경하지 않는 성질.  
  → GET, HEAD는 안전함

- **CORS (Cross-Origin Resource Sharing)**:  
  일부 메서드는 CORS 정책에 따라 사전 요청(Preflight)이 필요할 수 있음

---

## 1. GET

- 리소스를 조회하는 데 사용
- URL의 Query 문자열로 데이터 전달
- **멱등성**, **안전성** 있음
- 캐싱 가능, 브라우저 히스토리에 남음
- Body에 데이터를 실을 수 있으나 권장되지 않음

```http
GET https://m.entertain.naver.com/article/213/0001312836
```

---

## 2. POST

- 서버에 데이터를 보내어 처리하도록 요청
- 주로 **새 리소스 생성** 혹은 **복잡한 로직 처리** 시 사용
- Body에 JSON, XML, Form 등 다양한 포맷으로 데이터 전송
- 멱등성 없음

```http
POST / HTTP/1.1
Host: foo.com
Content-Type: application/json

{
  "username": "jdh",
  "age": 20
}
```

```http
HTTP/1.1 201 Created
Content-Type: application/json

{
  "username": "jdh",
  "age": 20,
  "memberNumber": 10
}
```

---

## 3. PUT

- 리소스를 **전체 대체(덮어쓰기)** 하는 요청
- 리소스가 존재하지 않으면 새로 생성할 수 있음
- 멱등성 있음

> [!danger] 데이터 유실 주의
> 일부 필드를 생략하고 PUT 요청 시, 생략된 필드는 삭제될 수 있습니다.

```python
# 기존 데이터
{
  "id": 1,
  "name": "홍길동",
  "age": 30
}

# PUT 데이터
{
  "name": "김철수"
}

# 결과
{
  "name": "김철수"
  # 'age' 정보 유실
}
```

---

## 4. PATCH

- 리소스의 **일부 필드만 수정**
- 데이터 효율성이 뛰어나며, 멱등성은 구현 방식에 따라 다름

```python
# 기존 사용자
{
  "id": 1,
  "name": "홍길동",
  "email": "hong@example.com"
}

# PATCH 데이터
{
  "name": "김철수"
}

# 결과
{
  "id": 1,
  "name": "김철수",
  "email": "hong@example.com"
}
```

---

## 5. DELETE

- 리소스를 삭제
- 멱등성 있음 (같은 요청을 반복해도 결과는 같음)

```http
DELETE /file.html HTTP/1.1
```

```http
HTTP/1.1 204 No Content
```

---

## 6. 기타 HTTP 메서드

- **HEAD**: GET과 동일하지만 응답 본문 없이 Header만 반환
- **OPTIONS**: 서버가 허용하는 메서드 및 옵션을 반환 (CORS Preflight 요청에 사용)
- **CONNECT, TRACE**: 프록시 터널링, 디버깅 등 특수 목적

---

## 참고 링크

- [MDN - HTTP Methods](https://developer.mozilla.org/ko/docs/Web/HTTP/Methods)
- 각 브라우저 및 서버 공식 문서
