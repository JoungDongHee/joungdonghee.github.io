---
tags: [Spring, Bean Validation, Validator, 검증 비교]
aliases: [Validator와 Bean Validation 차이, Bean Validation vs Validator]
created: 2025-05-16
---

# 📘Bean Validation vs Validator

Spring에서의 검증 로직을 구현하는 두 가지 주요 방식인 **Bean Validation(JSR-380)** 과 **Spring의 Validator 인터페이스**를 비교하여 각각의 특징과 차이점을 정리한다.

---

## ✅ 목적 및 정의

| 항목 | Bean Validation | Validator 인터페이스 |
|------|-----------------|-----------------------|
| 정의 | Java EE 표준 기반의 검증 방식 (JSR-380) | Spring 프레임워크 자체의 검증 추상화 |
| 표준 여부 | ✅ 표준 (javax.validation) | ❌ 비표준 (Spring 고유) |
| 기본 구현체 | Hibernate Validator | 직접 구현 필요 (ex. `ItemValidator`) |

---

## ✅ 사용 방식

| 항목 | Bean Validation | Validator 인터페이스 |
|------|------------------|------------------------|
| 선언 위치 | 필드 위에 애너테이션 부착 | 별도 클래스에서 `validate()` 메서드 구현 |
| 동작 시점 | `@Valid` 또는 `@Validated` 어노테이션 통해 자동 실행 | 컨트롤러 또는 수동 호출로 실행 |
| 주 사용 대상 | DTO/Form 객체 검증 | 복잡한 검증 로직, 커스텀 제약 조건 등 |

---

## ✅ 예시 비교

### Bean Validation

```java
public class ItemForm {
    @NotBlank
    private String name;

    @Min(1000)
    private int price;
}
```

```java
@PostMapping("/add")
public String add(@Validated @ModelAttribute ItemForm form, BindingResult result) { ... }
```

---

### Spring Validator

```java
@Component
public class ItemValidator implements Validator {
    public boolean supports(Class<?> clazz) {
        return Item.class.isAssignableFrom(clazz);
    }

    public void validate(Object target, Errors errors) {
        Item item = (Item) target;
        if (item.getPrice() < 1000) {
            errors.rejectValue("price", "min", "최소 가격은 1000원입니다.");
        }
    }
}
```

---

## ✅ 장단점 비교

| 구분  | Bean Validation                               | Validator 인터페이스                  |
| --- | --------------------------------------------- | -------------------------------- |
| 장점  | - 코드 간결함<br>- 표준 기반<br>- [[Thymeleaf]]와 연동 쉬움 | - 복잡한 로직 처리 가능<br>- 객체 간 상호검증 용이 |
| 단점  | - 복합 조건 검증 어려움<br>- 커스텀 구현 복잡                 | - 코드량 많고 반복적<br>- 재사용 어려움        |

---

## ✅ 실무 추천 사용 전략

- **간단한 필드 검증**: Bean Validation
- **복합 조건, 객체 간 비교 등 복잡한 검증**: Validator
- **둘을 함께 조합**하여 실무에서 사용되는 경우가 많음 (ex. Bean Validation + Validator 호출)

---

## 🔗 관련 문서

- [[Validator]]
- [[BindingResult]]
- [[Bean Validation]]
