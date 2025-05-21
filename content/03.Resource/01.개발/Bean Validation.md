---
tags: [Spring, Bean Validation, 검증, Hibernate Validator, DTO 검증]
aliases: [Spring Bean Validation, Hibernate Validator, Java Bean Validation]
created: 2025-04-10 15:04
---

# 📘Bean Validation

**Bean Validation**은 기존에 사용하던 Spring의 [[Validator]] 인터페이스를 이용한 검증 방식에서 발전한 **표준화된 검증 기술**로, Java 표준 스펙인 **JSR-380 (Bean Validation 2.0)** 을 의미한다.

## ✅ 개요 및 구현체

Bean Validation은 특정 구현체가 아닌 **기술 표준(Specification)** 이다. 대표적인 구현체는 다음과 같다:

- **Hibernate Validator** → Spring Boot 기본 탑재
- Apache BVal
- Open Liberty Validation 등

Maven 의존성이 필요한 경우, 다음 저장소를 참고한다:  
🔗 [Hibernate Validator Maven Repository](https://mvnrepository.com/artifact/org.hibernate/hibernate-validator)

---

## ✅ 사용 방법

간단하게 필드 위에 애너테이션만 추가하면 자동 검증이 적용된다.

```java
@Data
public class ItemSaveForm {

    @NotBlank
    private String itemName;

    @NotNull
    @Range(min = 1000, max = 1000000)
    private Integer price;

    @NotNull
    @Max(value = 9999)
    private Integer quantity;
}
```

---

## ✅ 대표 애너테이션 목록

| 애너테이션 | 설명 |
|------------|------|
| `@NotNull` | 값이 `null`이면 안 됨 |
| `@NotEmpty` | null 아님 + 비어있지 않음 (문자열, 배열, 컬렉션) |
| `@NotBlank` | 문자열이 공백이 아닌 값 포함 |
| `@Size(min, max)` | 문자열/배열/컬렉션의 크기 범위 제한 |
| `@Min`, `@Max` | 최소/최대 숫자 값 제한 |
| `@Positive`, `@Negative` | 양수/음수 값 검사 |
| `@Email` | 이메일 형식 확인 |
| `@Pattern(regexp)` | 정규 표현식 매칭 |
| `@Past`, `@Future` | 날짜가 과거/미래인지 확인 |

자세한 내용은 🔗 [Hibernate 공식 문서](https://docs.jboss.org/hibernate/validator/6.2/reference/en-US/html_single/#validator-defineconstraints-spec) 참조

---

## ✅ Spring Controller에서의 사용 예

```java
@PostMapping("/add")
public String addItem(
    @Validated @ModelAttribute ItemSaveForm form,
    BindingResult bindingResult,
    RedirectAttributes redirectAttributes) {

    // 복합 룰: 총합 가격 검증
    if (form.getPrice() != null && form.getQuantity() != null) {
        int totalPrice = form.getPrice() * form.getQuantity();
        if (totalPrice < 10000) {
            bindingResult.reject("totalPriceMin", "가격 * 수량의 합은 10,000 이상이어야 합니다.");
        }
    }

    if (bindingResult.hasErrors()) {
        return "validation/form";
    }

    Item savedItem = itemRepository.save(form.toItem());
    redirectAttributes.addAttribute("id", savedItem.getId());

    return "redirect:/items/{id}";
}
```

> 💡 사용자 메시지는 `messages.properties`에 정의해 다국어 및 사용자 맞춤 메시지를 지원하도록 한다.

---

## ✅ REST API에서의 사용

`@RequestBody`와 함께 사용할 경우 JSON 데이터를 자동으로 바인딩하고 검증한다.

```java
@PostMapping("/api/items")
public ResponseEntity<?> addItem(@Validated @RequestBody ItemSaveForm form) {
    Item savedItem = itemService.save(form);
    return ResponseEntity.ok(savedItem);
}
```

### 주의 사항

- **타입 불일치 시**: JSON → 객체 변환 자체 실패 → 400 에러
- **값 유효성 실패 시**: Hibernate Validator에 의해 검증 오류 발생 → 400 에러 응답
- **에러 메시지 커스터마이징** 필요 시 → 별도 DTO + [[@ControllerAdvice]] 구성 추천

---

## ✅ @ModelAttribute vs @RequestBody

| 구분              | `@ModelAttribute`                           | `@RequestBody`                               |
|-------------------|---------------------------------------------|-----------------------------------------------|
| 데이터 전달 방식  | 폼 파라미터 (query/form-data)                | JSON 요청 본문                                |
| 바인딩 방식       | 스프링의 파라미터 바인딩                    | `HttpMessageConverter` 사용                   |
| 검증 실패 처리    | [[BindingResult]]로 직접 제어 가능           | 자동 400 반환, [[@ExceptionHandler]] 필요     |
| 주요 사용처       | 템플릿 기반 MVC 폼 처리                      | REST API JSON 처리                            |

---

## ✅ 실무에서 자주 하는 실수와 팁

- `@Validated`가 없으면 애너테이션이 있어도 검증이 **실행되지 않음**
- `@Valid`도 사용 가능하지만, `@Validated`는 **groups 기능을 지원**하므로 실무에 더 유용
- **커스텀 제약조건**을 만들 때는 `ConstraintValidator` 인터페이스 구현
- 검증 실패 응답은 클라이언트 UX에 직접 영향을 미치므로 **에러 응답 객체 설계**가 중요

---

## 🔗 관련 문서

- [[Validator]]
- [[BindingResult]]
- [[Bean Validation vs Validator]]
- [Hibernate Validator 공식 문서](https://hibernate.org/validator/)
