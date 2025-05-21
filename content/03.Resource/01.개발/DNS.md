---
tags: [DNS, 네트워크, IP, 도메인, hosts 파일]
aliases: [DNS란, DNS 설명, 도메인 이름 시스템]
created: 2024-10-05 20:10
---

# 📘DNS(Domain Name System)란?

우리가 사용하는 휴대폰, 인터넷, TV, 서버 등 인터넷에 연결된 모든 것은 ***디지털 신호(0과 1)***, 즉 숫자를 통해 통신합니다. 이 숫자들이 바로 [[IP 주소]]입니다.

예를 들어, 우리가 자주 사용하는 `naver.com`, `google.com` 같은 사이트들도 각기 고유한 IP 주소를 가지고 있습니다. 우리가 크롬이나 엣지 같은 브라우저에서 `naver.com`에 접속하는 것은 결국 네이버 서버의 IP 주소에 접속하는 것입니다.

하지만 모든 통신은 숫자 기반으로 이루어지므로, 사람이 기억하기 쉬운 **도메인 이름**과 실제 IP 주소 간의 매핑이 필요합니다.  
이때 등장하는 것이 바로 **DNS**입니다.

> DNS는 쉽게 말해 인터넷의 전화번호부입니다. 사람이 기억하기 어려운 IP 주소를 사람이 이해할 수 있는 [[도메인]] 이름으로 변환해주는 시스템입니다.

![DNS 설명 이미지](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250516072126_image.png)

---

## DNS Server 구조

DNS 서버는 **계층적(hierarchical)** 구조로 이루어져 있습니다.

![DNS 계층 구조](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250516072175_image.png)

`www.example.com`을 입력하면 가장 먼저 **인터넷 공급업체(ISP)** 의 **DNS Resolver**가 요청을 받습니다. Resolver에 해당 IP 정보가 없다면 다음과 같은 순서로 질의가 이루어집니다:

1. **Root DNS 서버** → `.com`에 대한 네임서버 정보 반환  
2. **`.com` TLD 서버** → `example.com`에 대한 네임서버 정보 반환  
3. **Authoritative DNS 서버** → `www.example.com`의 최종 IP 주소(예: 192.0.2.44) 반환

---

## Recursive DNS vs Authoritative DNS

- **Recursive DNS**: 사용자의 요청을 받아 필요한 정보를 찾아 여러 DNS 서버에 질의  
- **Authoritative DNS**: 특정 도메인의 **최종 IP 주소** 정보를 제공  

이 두 역할이 협력하여 도메인 이름과 IP 주소 간 매핑을 효율적으로 수행합니다.

---

## [[hosts 파일]]

DNS 질의가 수행되기 전에, 먼저 로컬 시스템에 존재하는 **hosts 파일**을 확인합니다. 이 파일에 특정 도메인에 대한 IP가 지정되어 있다면, DNS 서버에 질의하지 않고 해당 정보를 우선 사용합니다.

![hosts 파일 확인 과정](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250516072298_image.png)

> 📌 따라서 `hosts` 파일이 악성코드 등에 의해 변경될 경우, 의도하지 않은 서버에 접속할 위험이 있습니다.

### hosts 파일 예시

```plaintext
111.111.111.111 www.naver.com
```

이와 같이 설정하면 `www.naver.com` 접속 시 원래 IP(예: 23.44.52.223)가 아닌 `111.111.111.111`로 접속됩니다.

![ping 전](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250516072279_image.png)  


![ping 후](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250516072397_image.png)

> 📎 [[https://en.wikipedia.org/wiki/Hosts_(file)|운영체제 별 hosts 파일 위치 (위키백과)]]

---

## DNS 캐시

DNS 캐시는 운영체제 또는 브라우저가 최근에 조회한 도메인의 IP 주소를 일시적으로 저장하는 메커니즘입니다.

- ✅ 장점: 응답 속도 개선, 네트워크 트래픽 감소  
- ⚠️ 단점: 캐시된 정보가 오래된 경우 잘못된 주소로 연결될 수 있음

> 이 캐시에는 **TTL(Time to Live)** 값이 있으며, 이 시간이 지나면 다시 DNS 질의를 수행합니다.

---

## 📚 참조

- [AWS: What is DNS?](https://aws.amazon.com/ko/route53/what-is-dns/)  
- [Wikipedia: Hosts file](https://en.wikipedia.org/wiki/Hosts_(file))
