---
type: 기술정리
created: 2024-11-26
tags: [SQL, ORDER BY, 정렬]
aliases: [정렬문, ORDER BY 사용법, SQL 정렬]
author: Joung Dong Hee
---

# ORDER BY (정렬)

SQL에서 데이터를 조회한 뒤 **오름차순**, **내림차순** 등의 정렬을 수행하고자 할 때는 `ORDER BY` 키워드를 사용합니다.

- **오름차순 (`ASC`)**: 값이 작은 것부터 큰 것 순서 (예: 1 → 2 → 3 → 4)
- **내림차순 (`DESC`)**: 값이 큰 것부터 작은 것 순서 (예: 4 → 3 → 2 → 1)

---

## 🔽 내림차순 정렬

```sql
SELECT * FROM employees ORDER BY salary DESC;
```

| id | name    | salary   | hire_date  |
|----|---------|----------|------------|
| 4  | Daisy   | 60000.00 | 2019-06-10 |
| 1  | Alice   | 55000.00 | 2021-05-15 |
| 3  | Charlie | 52000.00 | 2020-11-20 |
| 2  | Bob     | 48000.00 | 2022-03-12 |

---

## 🔼 오름차순 정렬

```sql
SELECT * FROM employees ORDER BY salary ASC;
```

| id | name    | salary   | hire_date  |
|----|---------|----------|------------|
| 2  | Bob     | 48000.00 | 2022-03-12 |
| 3  | Charlie | 52000.00 | 2020-11-20 |
| 1  | Alice   | 55000.00 | 2021-05-15 |
| 4  | Daisy   | 60000.00 | 2019-06-10 |

---

## 🔀 다중 정렬

여러 컬럼을 조합하여 정렬할 수 있습니다.  
예를 들어 `hire_date`는 오름차순, 같은 날짜 내에서는 `salary`를 내림차순으로 정렬합니다.

```sql
SELECT * FROM employees ORDER BY hire_date ASC, salary DESC;
```

| id | name    | salary   | hire_date  |
|----|---------|----------|------------|
| 4  | Daisy   | 60000.00 | 2019-06-10 |
| 3  | Charlie | 52000.00 | 2020-11-20 |
| 1  | Alice   | 55000.00 | 2021-05-15 |
| 2  | Bob     | 48000.00 | 2022-03-12 |

---

## ❓ `ORDER BY`가 없는 경우

정렬 기준이 지정되지 않은 경우, **DB 엔진이 임의의 순서로 결과를 반환**할 수 있으며 이 순서는 다음과 같은 요소에 영향을 받습니다:

```sql
SELECT * FROM employees;
```

[MySQL 공식 포럼](https://forums.mysql.com/read.php?21,239471,239688)에 따르면:

> - `ORDER BY`가 없을 경우 결과 순서를 신뢰해서는 안 된다.
> - 특정 순서가 필요하다면 항상 `ORDER BY`를 명시해야 한다.
> - 일부 상황에서 `GROUP BY`가 암묵적으로 정렬을 유도할 수 있으나 이는 표준 위반이며, `ORDER BY NULL`로 피할 수 있다.

MySQL은 일반적으로 **PRIMARY KEY 순서**나 **삽입 순서**를 기반으로 결과를 반환하지만,
데이터 수정, 삭제, 병합 등의 작업이 있을 경우 순서가 바뀔 수 있어 **일관성을 보장하기 어렵습니다**.

👉 따라서 **정렬이 필요한 경우 반드시 `ORDER BY`를 명시**해야 합니다.

---

## 🔗 참고

- [MySQL Forums - ORDER BY 정렬 기준](https://forums.mysql.com/read.php?21,239471,239471#msg-239471)
- [MySQL 공식 문서 - ORDER BY Optimization](https://dev.mysql.com/doc/refman/8.4/en/order-by-optimization.html)
