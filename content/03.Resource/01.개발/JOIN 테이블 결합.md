---
type: 기술정리
created: 2024-12-12
date: 2024 년 12 월 12 일 10 시 12 분
tags: [SQL, JOIN, 테이블조인]
aliases: [SQL JOIN, 테이블 JOIN, 조인문, JOIN 종류]
---

# JOIN

JOIN 은 여러개의 테이블을 하나의 테이블로 만드는 문법으로 하나의 테이블에 원하는 결과가 있으면 좋겠지만 A 와 B 테이블로 결과 값이 분리 되어 있을 경우 활용할수 있다.

여러개의 테이블 을 JOIN 할 경우 [[데이터의 무결성]] 을 위해 [[기본키-PK(Primary Key)]] 와 [[외래키-FK(Foreign Key)]] 관계로 맺어야 하며 이를 [[외래키-FK(Foreign Key)#^414bc3 |일대 다 관계]] 라고 한다.

JOIN 의 종류에는 **INNER JOIN** , **OUTER JOIN** , **CROSS JOIN** , **SELF JOIN** 이 존재 하며 원하는 결과에 맞게 사용해야 한다.


## INNER JOIN 내부 조인

두 테이블 간 `교집합` 을 찾아 결과를 도출 하기 위해 사용합니다. JOIN 조건 은 `ON` 절 에 명시 하며 일반적으로 PK 와 FK 와 같은 KEY 혹은 일치하는 컬럼을 기준으로 합니다.

![image.png](https://file-api.ksq9511.synology.me:5353/obsidian-image/20241212141450.png)



### 예시 테이블
#### 테이블 1: `employees`


| employee_id | name    | department_id |
| ----------- | ------- | ------------- |
| 1           | Alice   | 101           |
| 2           | Bob     | 102           |
| 3           | Charlie | 103           |
| 4           | David   | 104           |


#### 테이블 2: `departments`

|department_id|department_name|
|---|---|
|101|HR|
|102|IT|
|103|Marketing|
|105|Finance|

```sql
SELECT 
    e.employee_id,
    e.name,
    d.department_name
FROM 
    employees e
INNER JOIN 
    departments d
ON 
    e.department_id = d.department_id;

```


### 결과

|employee_id|name|department_name|
|---|---|---|
|1|Alice|HR|
|2|Bob|IT|
|3|Charlie|Marketing|



---

## 🔎 예시 테이블 요약

각 JOIN 예제에서 반복적으로 사용된 테이블은 다음과 같습니다:

### employees 테이블

| employee_id | name    | department_id |
| ----------- | ------- | ------------- |
| 1           | Alice   | 101           |
| 2           | Bob     | 102           |
| 3           | Charlie | 103           |
| 4           | David   | 104           |

### departments 테이블

| department_id | department_name |
| ------------- | ---------------- |
| 101           | HR               |
| 102           | IT               |
| 103           | Marketing        |
| 104           | Finance          |

---

### students 테이블

| student_id | name    | course_id |
| ---------- | ------- | --------- |
| 1          | Alice   | 101       |
| 2          | Bob     | 102       |
| 3          | Charlie | 103       |
| 4          | David   | 104       |

### courses 테이블

| course_id | course_name  |
| --------- | ------------ |
| 101       | Mathematics  |
| 102       | Science      |
| 103       | Literature   |

---

### orders 테이블

| order_id | customer_id | product_id |
| -------- | ----------- | ---------- |
| 1        | 101         | A1         |
| 2        | 102         | B2         |
| 3        | 103         | C3         |

### products 테이블

| product_id | product_name |
| ---------- | ------------ |
| A1         | Laptop       |
| B2         | Smartphone   |
| D4         | Tablet       |

---

### colors 테이블

| color_id | color_name |
| -------- | ---------- |
| A        | Red        |
| B        | Blue       |
| C        | Green      |

---

## OUTER JOIN 외부 조인 


두 테이블 중 하나의 데이터만 존재할 경우 기준 으로 삼는 테이블의 데이터를 표출 하는 방법으로 **LEFT** OUTER JOIN , **RIGHT** OUTER JOIN , **FULL** OUTER JOIN 이 존재한다,

이때 기준으로 삼는 테이블의 데이터는 모두 표출 하며  매칭 되는 테이블 의 데이터가 없는 경우 `NULL` 로 표출 한다,

![image.png](https://file-api.ksq9511.synology.me:5353/obsidian-image/20241212141447.png)



### **LEFT** OUTER JOIN

**왼쪽 테이블의 모든 행**과 오른쪽 테이블에서 **조인 조건을 만족하는 행**을 결합하는 문법 으로 오른쪽 테이블에 일치하는 결과가 없을 경우 `NULL`  이 나온다


#### 예시 테이블

##### 테이블 1: `students`

|student_id|name|course_id|
|---|---|---|
|1|Alice|101|
|2|Bob|102|
|3|Charlie|103|
|4|David|104|

##### 테이블 2: `courses`

|course_id|course_name|
|---|---|
|101|Mathematics|
|102|Science|
|103|Literature|

```sql
SELECT 
    s.student_id,
    s.name,
    c.course_name
FROM 
    students s
LEFT OUTER JOIN 
    courses c
ON 
    s.course_id = c.course_id;

```


#### 결과

|student_id|name|course_name|
|---|---|---|
|1|Alice|Mathematics|
|2|Bob|Science|
|3|Charlie|Literature|
|4|David|NULL|

### RIGHT OUTER JOIN

**우측 테이블의 모든 행**과 좌측 테이블에서 **조인 조건을 만족하는 행**을 결합하는 문법 으로 좌측 테이블에 일치하는 결과가 없을 경우 `NULL`  이 나온다


#### 예시 테이블

##### 테이블 1: `orders`

| order_id | customer_id | product_id |
| -------- | ----------- | ---------- |
| 1        | 101         | A1         |
| 2        | 102         | B2         |
| 3        | 103         | C3         |

##### 테이블 2: `products`

| product_id | product_name |
| ---------- | ------------ |
| A1         | Laptop       |
| B2         | Smartphone   |
| D4         | Tablet       |

```sql
SELECT 
    o.order_id,
    o.customer_id,
    p.product_name
FROM 
    orders o
RIGHT OUTER JOIN 
    products p
ON 
    o.product_id = p.product_id;
```


#### 결과

| order_id | customer_id | product_name |
| -------- | ----------- | ------------ |
| 1        | 101         | Laptop       |
| 2        | 102         | Smartphone   |
| NULL     | NULL        | Tablet       |

### **FULL OUTER JOIN**

 **왼쪽 테이블**과 **오른쪽 테이블의 모든 행**을 포함하며, **조인 조건을 만족하는 행**을 결합하는 방법으로 왼쪽 과 오른족에 일치하는 데이터가 없는 경우 `NULL` 로 표시 한다.


#### 예시 테이블

##### 테이블 1: `employees`

| employee_id | name    | department_id |
| ----------- | ------- | ------------- |
| 1           | Alice   | 101           |
| 2           | Bob     | 102           |
| 3           | Charlie | 103           |

##### 테이블 2: `departments`

| department_id | department_name |
| ------------- | --------------- |
| 101           | HR              |
| 102           | IT              |
| 104           | Finance         |

```sql
SELECT 
    e.employee_id,
    e.name,
    d.department_name
FROM 
    employees e
FULL OUTER JOIN 
    departments d
ON 
    e.department_id = d.department_id;

```


#### 결과

| employee_id | name    | department_name |
| ----------- | ------- | --------------- |
| 1           | Alice   | HR              |
| 2           | Bob     | IT              |
| 3           | Charlie | NULL            |
| NULL        | NULL    | Finance         |

## CROSS JOIN

두개의 테이블을 조합하여 만들수 있는 최대의 조합을 만들어 내는 방법입니다. 일반적인 JOIN 과 다르게 
`ON` 을 사용하지 않고 첫번째 행과 두번째 테이블 의 모든 행과 결합 됩니다.

여기서 만들어지는 테이블의 행 의 수는 `(결과 행 수 = 테이블1 행 수 × 테이블2 행 수)` 이다.

CROSS JOIN 은 모든 테이블 의 조합을 반환 하기 때문에 결과 테이블의 행의 수가 배수로 늘어나게 된며 성능 저하에 원인이 된다. 

![image.png](https://file-api.ksq9511.synology.me:5353/obsidian-image/20241212141320.png)



### 예시 테이블

#### 테이블 1: `products`

|product_id|product_name|
|---|---|
|1|Laptop|
|2|Smartphone|

#### 테이블 2: `colors`

|color_id|color_name|
|---|---|
|A|Red|
|B|Blue|
|C|Green|

```sql
SELECT 
    p.product_name,
    c.color_name
FROM 
    products p
CROSS JOIN 
    colors c;

```


### 결과

|product_name|color_name|
|---|---|
|Laptop|Red|
|Laptop|Blue|
|Laptop|Green|
|Smartphone|Red|
|Smartphone|Blue|
|Smartphone|Green|

## SELF JOIN

동일한 테이블은 두번 JOIN 하여 결과를 만들어내는 방식으로 보통 계층 구조(예: 직원-관리자 관계)나 데이터 비교를 구현할 때 사용됩니다.


![image.png](https://file-api.ksq9511.synology.me:5353/obsidian-image/20241212142468.png)



### 예시 테이블

#### 테이블: `employees`

|employee_id|name|manager_id|
|---|---|---|
|1|Alice|NULL|
|2|Bob|1|
|3|Charlie|1|
|4|David|2|

```sql
SELECT 
    e1.name AS employee_name,
    e2.name AS manager_name
FROM 
    employees e1
LEFT JOIN 
    employees e2
ON 
    e1.manager_id = e2.employee_id;

```


### 결과

|employee_name|manager_name|
|---|---|
|Alice|NULL|
|Bob|Alice|
|Charlie|Alice|
|David|Bob|

---

# 참고 자료

- [한빛 SQL 기초 문법 - JOIN 정리](https://hongong.hanbit.co.kr/sql-%EA%B8%B0%EB%B3%B8-%EB%AC%B8%EB%B2%95-joininner-outer-cross-self-join/)