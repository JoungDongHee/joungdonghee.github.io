---
tags:
  - 개발
  - 기술정리
  - JSON
  - 데이터포맷
  - API
  - 보안
created: 2025-05-21
type: 기술정리
aliases:
  - JSON이란
  - JSON 개념
  - JSON 정의
  - 자바스크립트 객체 표기법
  - JSON
  - json 
---

# 📘 [[JSON]] (JavaScript Object Notation)

**JSON(JavaScript Object Notation)** 은 자바스크립트의 객체 표기법을 기반으로 만들어진 **텍스트 기반의 데이터 포맷** 입니다.  

0과 1로 이루어진 컴퓨터의 이진 데이터 처리 환경 속에서, 사람이 데이터를 이해하고 읽기 쉽도록 설계된 **구조화된 표현 방식**입니다. 주로 시스템 간 **데이터 교환**에 사용됩니다.

JSON은 **key-value 쌍**으로 데이터를 표현하며, 특히 **RESTful API 통신**에서 널리 활용됩니다.

### ✅ JSON의 기본 구조

```json
{
  "key1": "value1"
}
```

### ✅ 다양한 데이터 타입 예시

- **숫자 (Number)**

```json
{
  "number": 1
}
```

- **문자열 (String)**

```json
{
  "string": "Hello"
}
```

- **불리언 (Boolean)**

```json
{
  "bool": true
}
```

- **객체 (Object)**

```json
{
  "object": {
    "innerKey": "innerValue"
  }
}
```

- **배열 (Array)**

```json
{
  "array": ["item1", "item2"]
}
```

### 📦 JSON과 API 통신

API 통신 시 JSON 데이터는 **문자열 형태로 메시지 바디(Message Body)** 에 포함되어 전송됩니다.  
문자열이 길어질 경우 네트워크 대역폭을 많이 차지하게 되어 **전송 속도 저하**가 발생할 수 있습니다.  

이를 해결하기 위해 **데이터 압축(gzip 등)** 을 적용하기도 합니다.

---

### 🔐 JSON 사용 시 보안 고려사항

JSON은 API 통신에 많이 사용되기 때문에, 다음과 같은 보안 이슈에 주의해야 합니다:

- **JSON Injection**
  - 사용자 입력값이 JSON 구조에 삽입되어 악의적인 데이터를 포함하거나 구조를 변경할 수 있습니다.
  - 해결책: 입력값을 반드시 검증하고, JSON을 직접 문자열로 구성하지 말고 안전한 직렬화 방식을 사용하세요.

- **Cross-Site Scripting (XSS)**
  - 클라이언트에서 받은 JSON 데이터를 HTML에 직접 출력할 경우, 스크립트가 실행될 위험이 있습니다.
  - 해결책: 클라이언트 렌더링 시 데이터를 적절히 이스케이프 처리하고, 신뢰할 수 없는 JSON은 DOM에 직접 삽입하지 마세요.

- **CORS(Cross-Origin Resource Sharing) 정책**
  - JSON은 종종 도메인 간 요청에 사용되는데, 이때 서버가 명시적으로 허용하지 않으면 요청이 차단됩니다.
  - 해결책: 서버에서 올바른 `Access-Control-Allow-Origin` 헤더를 설정해야 합니다.

- **대용량 데이터 유출 위험**
  - JSON은 사람이 읽기 쉬운 형태이므로, 인증되지 않은 사용자에게 응답이 노출될 경우 민감 정보가 쉽게 유출될 수 있습니다.
  - 해결책: 민감 데이터는 포함하지 않도록 주의하고, 인증 및 권한 검사를 철저히 해야 합니다.

---

> 💡 JSON은 대부분의 프로그래밍 언어에서 쉽게 파싱(parse)하고 생성(generate)할 수 있는 범용 형식입니다.
