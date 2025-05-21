---
tags: [URI, URL, URN, 웹 주소, 리소스 식별자]
aliases: [URI란, URL이란, URN이란]
created: 2024-11-03 19:11
author: Joung Dong Hee
---

# URI (Uniform Resource Identifier)

**URI**는 **URL**과 **URN**을 포함하는 상위 개념으로,  
인터넷상의 **리소스(Resource)** 를 식별하는 문자열입니다.

> 📌 URI는 특정 리소스가 어디 있는지(위치) 또는 무엇인지(이름)를 식별할 수 있습니다.

![URI 개념 이미지](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250516071464_image.png)

---

# URL (Uniform Resource Locator)

**URL**은 인터넷에서 웹페이지, 이미지, 비디오 등의 **위치(Location)** 를 지정하는 문자열입니다.  
예: `https://developer.mozilla.org`
브라우저 주소창에 입력하는 대부분의 주소가 URL에 해당합니다.

---

# URN (Uniform Resource Name)

**URN**은 리소스의 **이름(Name)** 만을 식별하며, 위치나 접근 방법은 지정하지 않습니다.  
예: `urn:isbn:0451450523` (책의 ISBN)

URN은 특정 리소스가 "어디"에 있는지는 몰라도 "무엇"인지를 고유하게 식별할 수 있게 해줍니다.

![URN 예시 이미지](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250516071426_image.png)

---

## URL의 기본 구조

![URL 구조](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250516071573_image.png)

| 구성 요소              | 설명 |
|----------------------|------|
| `http`               | **프로토콜(Protocol)** - 자원에 어떻게 접근할지를 정의 (예: `https`, `ftp`, `ssh` 등) |
| `www.example.com`    | **도메인 이름** - [[DNS]]를 통해 IP 주소로 변환됨 |
| `:80`                | **[[포트]] 번호** - 생략 가능 (기본값: `http`: 80, `https`: 443, `ssh`: 22 등) |
| `/path/to/myfile.html` | **경로(Path)** - 요청하는 자원의 위치 |
| `?key1=value1&key2=value2` | **쿼리 파라미터(Query Parameter)** - 서버에 전달할 데이터 |
| `#SomewhereInTheDocument` | **Fragment** - 문서 내 특정 위치로 이동, 서버에는 전송되지 않음 |

---

## URI, URL, URN 관계 요약

| 항목 | 목적 | 예시 | URI 포함 여부 |
|------|------|------|----------------|
| **URI** | 자원을 유일하게 식별 | `https://example.com` 또는 `urn:isbn:0451450523` | ✅ 전체 |
| **URL** | 자원의 위치를 지정 | `https://example.com/page` | ✅ 일부 |
| **URN** | 자원의 이름을 지정 | `urn:isbn:0451450523` | ✅ 일부 |

---

## 📚 관련 문서

- [[DNS]]
- [[포트]]
