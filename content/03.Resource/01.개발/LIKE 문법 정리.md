---
type: 기술정리
created: 2024-12-26
tags: [SQL, LIKE, 문자열검색]
aliases: [LIKE 문법, LIKE 사용법, SQL LIKE]
author: Joung Dong Hee
---

# LIKE (일치)

`LIKE` 연산자는 [[MySQL]]에서 문자열 패턴을 검색할 때 사용됩니다. 주로 `WHERE` 절에서 특정 문자열 패턴과 일치하는 데이터를 찾기 위해 활용합니다.

---

## 🔹 와일드카드 설명

| 와일드카드 | 설명 | 예시 | 일치하는 값 |
|------------|------|------|--------------|
| `%`        | 0개 이상의 문자와 일치 | `'ab%'` | `"ab"`, `"abc"`, `"abcd"` |
| `_`        | 정확히 1개의 문자와 일치 | `'a_d'` | `"abd"`, `"a1d"`, `"a#d"` (단 `"ad"` 또는 `"abcd"`는 일치 X) |

---

## 예시 테이블: `users`

| id  | username  | email                  |
|-----|-----------|------------------------|
| 1   | alice     | alice@example.com      |
| 2   | bob       | bob123@example.com     |
| 3   | carol     | carol_test@example.com |
| 4   | david     | david123@example.com   |
| 5   | john_doe  | john@example.com       |

---

## ✅ `%` 사용 예시

"a"로 시작하는 모든 username을 찾기 위한 쿼리:

```sql
SELECT * FROM users WHERE username LIKE 'a%';
```

**결과:**

| id | username | email              |
|----|----------|--------------------|
| 1  | alice    | alice@example.com  |

---

## ✅ `_` 사용 예시

첫 글자가 "b"이고 세 번째 글자가 "b"인 3글자 username을 찾기 위한 쿼리:

```sql
SELECT * FROM users WHERE username LIKE 'b_b';
```

**결과:**

| id | username | email              |
|----|----------|--------------------|
| 2  | bob      | bob123@example.com |

---

## ✅ `%`와 `_` 조합 예시

두 번째 이후에 "d"가 포함된 username을 찾는 쿼리:

```sql
SELECT * FROM users WHERE username LIKE '%_d%';
```

**결과:**

| id | username | email              |
|----|----------|--------------------|
| 4  | david    | david123@example.com |
| 5  | john_doe | john@example.com     |

---

## ⚠️ 주의사항

1. **대소문자 구분**
    - MySQL은 기본적으로 대소문자를 구분하지 않음 (`utf8_general_ci` 등).
    - 대소문자 구분이 필요한 경우 `BINARY` 키워드 사용:

    ```sql
    SELECT * FROM users WHERE username LIKE BINARY 'A%';
    ```

2. **특수문자 포함 검색**
    - `%`, `_`를 패턴으로 그대로 사용하려면 `ESCAPE` 필요:
```sql
SELECT * FROM users WHERE username LIKE '%\_%' ESCAPE '\';
```

3. **LIKE + IN은 불가능**
    - 아래 쿼리는 **잘못된 구문**:

    ```sql
    SELECT * FROM users WHERE username LIKE IN ('%alice%', '%bob%');
    ```

    - **올바른 방식:**

    ```sql
    SELECT * FROM users WHERE username LIKE '%alice%' OR username LIKE '%bob%';
    ```

4. **성능 문제**
    - `%` 또는 `_`가 포함된 `LIKE`는 인덱스 사용 불가.
    - 특히 `%abc`처럼 **좌측 와일드카드**는 인덱스를 전혀 활용하지 못해 **Full Table Scan**이 발생함.

---

## 🔗 관련 문서

- [[MySQL 문자열 함수]]
- [[WHERE 조건절]]
- [[SQL 성능 최적화]]

---

## 참고

- [W3Resource - MySQL LIKE](https://www.w3resource.com/mysql/string-functions/mysql-like-function.php)
