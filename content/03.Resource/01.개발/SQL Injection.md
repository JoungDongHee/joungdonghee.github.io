---
tags:
  - 개발
  - 기술정리
  - 보안
  - SQLInjection
aliases:
  - SQL 인젝션
  - SQL 주입 공격
  - SQL 보안 취약점
type: 기술정리
created: 2025-05-21
---

# 📘 SQL Injection

`SQL Injection(주입)`은 사용자 입력의 유효성을 검사하지 못하는 웹 애플리케이션의 취약점을 악용하여, 공격자가 데이터베이스에 임의의 SQL 명령을 삽입 및 실행할 수 있게 하는 보안 취약점입니다.

이로 인해 공격자는 다음과 같은 악의적 행위를 수행할 수 있습니다:

- 인증 우회
- 데이터 조회, 변경, 삭제
- 데이터베이스 구조 확인 (스키마 탐색)
- 내부 시스템 접근

많은 **데이터 침해 사고**가 SQL Injection으로 인해 발생하며, 웹 보안에서 가장 주의해야 할 공격 유형 중 하나입니다.

---

## 🧪 예시

사용자가 로그인 입력폼을 통해 로그인 시 다음과 같은 쿼리가 실행될 수 있습니다.

```sql
"SELECT Count(*) FROM Users WHERE Username='" + txt.User.Text + "' AND Password='" + txt.Password.Text + "'";
```

사용자가 정상적으로 입력한 경우:

```sql
"SELECT Count(*) FROM Users WHERE Username='admin' AND Password='passwd123'";
```

정상적인 쿼리처럼 보이지만, **데이터 유효성 검증이 누락된 경우** 해커는 다음과 같이 입력할 수 있습니다:

- 사용자 이름: `admin`
- 비밀번호: `anything' or '1'='1`

이때 쿼리는 다음과 같이 변형됩니다:

```sql
"SELECT Count(*) FROM Users WHERE Username='admin' AND Password='anything' or '1'='1'";
```

- `'1'='1'`은 항상 `TRUE`이므로, 공격자는 비밀번호 없이 인증을 우회할 수 있습니다.

📌 참고 이미지:

![SQL Injection Example](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250521175144_image.png)

---

## 🔐 예방 방법

SQL Injection은 아래와 같은 방법으로 방지할 수 있습니다.

### 1. 사용자 입력 검증
- 숫자형 입력에 숫자 외의 문자가 포함되지 않도록 검증
- 정규 표현식 등을 통한 화이트리스트 기반 검증

### 2. PreparedStatement(= Parameterized Query) 사용
- [[JDBC]]의 `PreparedStatement`, MyBatis의 `#{} 구문`, JPA 등의 파라미터 바인딩을 통해 SQL 쿼리를 분리

```java
String query = "SELECT * FROM Users WHERE username = ? AND password = ?";
PreparedStatement pstmt = conn.prepareStatement(query);
pstmt.setString(1, username);
pstmt.setString(2, password);
```

### 3. ORM 또는 프레임워크의 바인딩 기능 활용
- MyBatis: `#{}` 사용
- JPA: JPQL의 바인딩 파라미터 사용

### 4. 에러 메시지 노출 금지
- 데이터베이스 관련 예외 메시지를 사용자에게 직접 노출하지 않도록 설정
- 에러 로그는 내부에서 처리하되, 외부에는 일반 메시지 제공

### 5. 최소 권한의 DB 계정 사용
- 읽기/쓰기 권한을 최소화하여 악용 가능성을 낮춤

---

## ⚠️ 주의사항

- `JDBC Template`에서 문자열을 직접 조합하는 방식은 SQL Injection에 매우 취약합니다.

  예를 들어 다음과 같은 코드:

  ```java
  String sql = "SELECT * FROM Users WHERE username = '" + username + "' AND password = '" + password + "'";
  jdbcTemplate.queryForList(sql);
  ```

  위처럼 **사용자 입력을 쿼리에 직접 문자열로 삽입**하면, 악의적인 SQL 문이 삽입되어 공격당할 수 있습니다. 반드시 바인딩 메커니즘을 사용해야 합니다.

- SQL 로그를 외부에 노출하거나 에러를 콘솔에 출력하지 않도록 설정하는 것도 중요합니다.

---

## 🔗 관련 문서
- [[MyBatis]]
- [[Spring Security]]
- [[입력값 검증]]
