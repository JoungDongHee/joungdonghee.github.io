---
tags: [Spring, BindingResult, 검증, 폼처리]
aliases: [Spring BindingResult, 바인딩 결과, 바인딩 에러 처리]
created: 2025-04-04 12:04
---

# 📘BindingResult

[[Spring MVC]]에서 사용자의 입력값을 서버 측 도메인 객체에 바인딩하거나 검증할 때 발생하는 오류를 수집하고 처리하기 위한 객체가 `BindingResult`이다.

`BindingResult`는 컨트롤러 메서드에서 `@ModelAttribute`로 바인딩된 객체에 대한 바인딩 및 검증 에러 정보를 담고 있으며, 사용자가 이를 활용해 적절하게 오류를 처리할 수 있도록 도와준다.

## ❓ 왜 BindingResult가 필요한가?

컨트롤러에서 `@ModelAttribute`를 사용해 폼 데이터를 객체로 바인딩할 때 유효성 검증이 실패하면 예외가 발생할 수 있다. 이때 `BindingResult`를 사용하지 않으면 [[Spring]]은 예외를 던지고, 사용자는 `Whitelabel Error Page`와 같은 기본 에러 페이지를 보게 된다.

이는 사용자 경험에 부정적인 영향을 준다.

```java
public String addItem(@ModelAttribute Item item, Model model) {
    // BindingResult가 없으면 바인딩/검증 오류 시 여기까지 도달하지 못함
}
```

하지만 `BindingResult`를 `@ModelAttribute` 바로 다음 파라미터로 선언하면, 스프링은 예외를 던지지 않고 검증 오류를 `BindingResult`에 담아 컨트롤러 메서드 내부에서 해당 로직을 계속 실행할 수 있도록 해준다.

```java
public String addItem(@ModelAttribute Item item, BindingResult bindingResult, Model model) {
    if (bindingResult.hasErrors()) {
        return "form"; // 다시 입력 폼으로 이동
    }
    // 정상 처리
}
```

## ✅ 사용 방법

1. `BindingResult`는 반드시 `@ModelAttribute` 파라미터 **바로 뒤**에 위치해야 한다.
2. 검증 오류가 있을 경우, `FieldError`나 `ObjectError`를 사용해 에러 정보를 수동으로 추가할 수 있다.

```java
public String addItemV1(@ModelAttribute Item item, BindingResult bindingResult, Model model) {
    if (!StringUtils.hasText(item.getItemName())) {
        bindingResult.addError(new FieldError(
            "item", "itemName", item.getItemName(), false,
            new String[]{"required.item.itemName"}, null, "상품 이름은 필수입니다."
        ));
    }

    if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 1000000) {  
        bindingResult.addError(new FieldError(
            "item", "price", item.getPrice(), false, null, null, 
            "가격은 1,000 ~ 1,000,000 까지 허용합니다."));  
    }

    if (bindingResult.hasErrors()) {
        return "form"; // 다시 입력 폼으로 이동
    }

    return "redirect:/items";
}
```

> [!note]  
> `FieldError`는 `ObjectError`의 하위 클래스이며, 각각 필드 단위/객체 전체에 대한 오류를 나타낸다.

## 🧩 Thymeleaf에서 오류 메시지 출력하기

`BindingResult`에 담긴 에러 메시지는 [[Thymeleaf]] 템플릿에서 다음과 같이 출력할 수 있다.

### 1. 단일 필드 오류 출력

```html
<div>
    <label>상품명</label>
    <input type="text" th:field="*{itemName}" />
    <div th:if="${#fields.hasErrors('itemName')}" th:errors="*{itemName}">상품명 오류</div>
</div>
```

### 2. 글로벌 오류 출력

```html
<div th:if="${#fields.hasGlobalErrors()}">
    <p th:each="err : ${#fields.globalErrors()}" th:text="${err}">글로벌 오류</p>
</div>
```

### 3. 전체 오류 출력

```html
<ul>
    <li th:each="err : ${#fields.allErrors()}" th:text="${err}"></li>
</ul>
```

## ⚠️ BindingResult의 단점

- 검증할 필드가 많을수록 코드가 복잡하고 중복된다.
- 유지보수가 어렵고, 컨트롤러 코드의 가독성이 떨어진다.
- 실무에서는 [[@Validated]] 와 [[Bean Validation]]을 조합하는 방식이 선호된다.

---

## 📌 추가로 알아두면 좋은 점

### 1. 검증 실패를 예외 대신 처리할 수 있게 해준다
- `BindingResult`가 없으면 `@Valid`나 `@Validated` 사용 시 검증 실패 시 예외가 발생한다.
- `BindingResult`를 함께 선언하면 예외 없이 오류 정보를 수집할 수 있다.

### 2. 필드 오류 vs 글로벌 오류
- `rejectValue()` → 특정 필드 오류
- `reject()` → 객체 전체 오류

### 3. 메시지 국제화 연동 가능
- `messages.properties`와 연동하여 다양한 언어의 에러 메시지를 출력할 수 있다.

```properties
required.item.itemName=상품 이름은 필수입니다.
range.item.price=가격은 1,000 ~ 1,000,000 까지 허용합니다.
```

### 4. 커스텀 Validator와 연동
- `Errors` 인터페이스는 내부적으로 `BindingResult`를 구현한 객체이며, 커스텀 Validator와도 호환된다..ㅡㅇ

### 5. 파라미터 위치 주의
- `BindingResult`는 반드시 `@ModelAttribute` 파라미터 **바로 뒤**에 위치해야 한다.

### 6. 비즈니스 로직 오류에도 사용 가능
- 예: 중복된 이름, 재고 부족 등의 오류도 `rejectValue()`를 통해 처리 가능

### 7. REST API에서는 다른 방식 사용
- REST 방식에서는 `@ExceptionHandler` 또는 `@ControllerAdvice`와 함께 예외 기반 검증 처리 필요

---

## 🔗 관련 문서

- [[Bean Validation vs Validator]]
- [[Validator]]
- [Spring 공식 문서 - BindingResult](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods.html#webmvc-ann-methods-arguments-bindingresult)
