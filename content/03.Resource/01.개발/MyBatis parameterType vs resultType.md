---
tags:
  - MyBatis
  - parameterType
  - resultType
  - 매핑
aliases:
  - parameterType vs resultType
  - MyBatis 파라미터 매핑
  - MyBatis 결과 매핑
created: 2025-05-19
---

# 📘 MyBatis의 `parameterType` vs `resultType` 차이와 매핑 방식

MyBatis에서는 SQL을 실행할 때 사용되는 **입력값(parameter)** 과 **결과값(result)** 을 Java 객체와 매핑하기 위해 `parameterType`과 `resultType` 속성을 사용합니다. 이 두 속성은 **POJO 기반 매핑**을 지원하며, 각각 다음과 같은 역할을 합니다.

---

## 📌 핵심 비교

| 속성 이름        | 역할                            | 바인딩 기준 메서드 | 매핑 방향        |
|------------------|----------------------------------|---------------------|------------------|
| `parameterType`  | SQL 실행 시 파라미터 전달          | `getter` 메서드 사용 | Java → SQL       |
| `resultType`     | SQL 실행 결과를 Java 객체로 매핑   | `setter` 메서드 사용 | SQL → Java 객체 |

---

## 🧩 parameterType – 입력 파라미터 바인딩

### 설명
MyBatis에서 SQL에 값을 전달할 때 사용하는 속성입니다. 이 속성에 지정된 Java 객체는 내부적으로 **getter 메서드**를 통해 값을 읽어 `#{}`에 바인딩됩니다.

### 예시

```xml
<select id="selectUser" parameterType="model.User" resultType="model.User">
    SELECT * FROM users WHERE id = #{id} AND name = #{name}
</select>
```

```java
public class User {
    private int id;
    private String name;

    public int getId() { return id; }
    public String getName() { return name; }
}
```

> ☑️ 위 예시에서 `#{id}`는 내부적으로 `getId()` 호출 결과를 SQL에 바인딩합니다.

> ⚠️ **주의:** `getter`가 정의되어 있지 않으면 해당 값은 SQL에 바인딩되지 않습니다.

---

## 🧾 resultType – 결과 매핑 처리

### 설명
쿼리 실행 후 반환된 `ResultSet` 데이터를 Java 객체로 매핑할 때 사용되는 속성입니다. 컬럼명을 기준으로 Java 객체의 **setter 메서드**를 호출하여 값을 주입합니다.

### 예시

```sql
SELECT id AS userId, name AS userName FROM users
```

```java
public class User {
    private int userId;
    private String userName;

    public void setUserId(int userId) { this.userId = userId; }
    public void setUserName(String userName) { this.userName = userName; }
}
```

> ☑️ 위 쿼리에서 조회된 `userId`, `userName` 컬럼은 `setUserId()`, `setUserName()`을 통해 객체에 주입됩니다.

> ⚠️ **주의:** `setter`가 정의되어 있지 않으면 해당 컬럼값은 객체에 주입되지 않습니다.

---

## ✅ 주의 사항 요약

> ⚠️ `parameterType`은 **getter**가 반드시 필요하며  
> ⚠️ `resultType`은 **setter**가 반드시 필요합니다.

- 만약 getter/setter가 누락되면 **정상적으로 바인딩되지 않으며**, 결과는 `null`이 되거나 실행 시 오류가 발생할 수 있습니다.
- 이는 Map 또는 기본형(Primitive type)으로 처리할 때는 예외일 수 있으나, **POJO를 사용하는 경우 반드시 확인**해야 합니다.

---

## 🔄 관련 개념과 비교

| 항목         | 설명 |
|--------------|------|
| `parameterType` | Mapper 메서드의 입력 인자를 SQL에 바인딩할 때 사용 |
| `resultType`    | SQL 결과를 객체로 매핑할 때 사용 |
| `resultMap`     | `resultType`보다 정밀한 매핑이 필요할 때 사용하는 사용자 정의 매핑 방식 |

---

## 🔗 관련 문서

- [[MyBatis]]
- [[ORM 개념]]
- [[resultMap]]
