---
tags: [Spring, Validator, 검증, SpringMVC]
aliases: [Spring Validator, 스프링 Validator, Validator 인터페이스]
created: 2025-04-10 14:04
---

# 📘 Validator (Spring Framework)

[[Spring Framework]]에서는 입력 데이터 검증(Validation)을 편리하게 수행하고 코드의 재사용성을 높이기 위해 `Validator`라는 인터페이스를 제공한다.

## Validator 인터페이스

`Validator`는 입력 데이터의 검증 로직을 별도의 클래스로 분리할 때 사용된다. 검증 로직을 Controller 내부에 작성하면 코드가 복잡해지고 유지보수가 어려워지기 때문이다.

Validator의 기본 인터페이스는 다음과 같다:

```java
public interface Validator {  
    boolean supports(Class<?> clazz);  
    void validate(Object target, Errors errors);  
}
```

- `supports(Class<?> clazz)`: 이 Validator가 특정 클래스의 객체 검증을 지원하는지 여부를 판단한다.
- `validate(Object target, Errors errors)`: 실제 검증 로직을 작성한다. 검증 실패 시 `Errors` 객체에 오류 정보를 추가한다.

## 사용 방법

### ❌ Controller 내 직접 검증 (권장되지 않음)

다음 예시는 Controller에서 직접 검증 로직을 구현한 방식으로, 검증 항목이 많아질수록 코드가 복잡해지고 중복이 증가한다.

```java
public String addItem(@ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes, Model model) {  
    if (!StringUtils.hasText(item.getItemName())) {  
        bindingResult.rejectValue("itemName", "required");  
    }  
    if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 1000000) {  
        bindingResult.rejectValue("price", "range", new Object[]{1000, 1000000}, null);  
    }  
    if (item.getQuantity() == null || item.getQuantity() >= 9999) {  
        bindingResult.rejectValue("quantity", "max", new Object[]{9999}, null);  
    }

    if (item.getPrice() != null && item.getQuantity() != null && item.getPrice() * item.getQuantity() < 10000) {  
        bindingResult.reject("totalPriceMin", new Object[]{10000}, null);  
    }  

    if (bindingResult.hasErrors()) {  
        return "validation/addForm";  
    }  

    Item savedItem = itemRepository.save(item);  
    redirectAttributes.addAttribute("itemId", savedItem.getId());  
    return "redirect:/items/{itemId}";  
}
```

### ✅ Validator 인터페이스 구현 (권장 방식)

별도의 Validator 클래스를 만들어 검증 로직을 분리하면 유지보수성과 재사용성이 좋아진다.

```java
@Component
public class ItemValidator implements Validator {  

    @Override  
    public boolean supports(Class<?> clazz) {  
        return Item.class.isAssignableFrom(clazz);  
    }  

    @Override  
    public void validate(Object target, Errors errors) {  
        Item item = (Item) target;

        if (!StringUtils.hasText(item.getItemName())) {  
            errors.rejectValue("itemName", "required");  
        }  
        if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 1000000) {  
            errors.rejectValue("price", "range", new Object[]{1000, 1000000}, null);  
        }  
        if (item.getQuantity() == null || item.getQuantity() >= 9999) {  
            errors.rejectValue("quantity", "max", new Object[]{9999}, null);  
        }

        if (item.getPrice() != null && item.getQuantity() != null && item.getPrice() * item.getQuantity() < 10000) {  
            errors.reject("totalPriceMin", new Object[]{10000}, null);  
        }  
    }  
}
```

## Validator 등록

Validator를 Controller에 등록하려면 `@InitBinder` 메서드를 사용한다. 이는 해당 Controller의 메서드가 호출되기 전에 `WebDataBinder`에 Validator를 등록해준다.

```java
@InitBinder  
public void init(WebDataBinder dataBinder) {  
    dataBinder.addValidators(itemValidator);  
}
```

> [!note]  
> `@InitBinder`는 해당 컨트롤러에만 적용된다. 전역 Validator 설정이 필요한 경우 WebMvcConfigurer를 활용한다.

## Validator 활용

```java
public String addItemV5(@ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes, Model model) {  
    itemValidator.validate(item, bindingResult);  

    if (bindingResult.hasErrors()) {  
        log.info("errors={} ", bindingResult);  
        return "validation/v2/addForm";  
    }  

    Item savedItem = itemRepository.save(item);  
    redirectAttributes.addAttribute("itemId", savedItem.getId());  
    redirectAttributes.addAttribute("status", true);  
    return "redirect:/validation/v2/items/{itemId}";  
}
```

### `@Validated`를 활용한 간결한 방식

Validator를 직접 호출하지 않고, `@Validated`를 활용하면 더 간결하게 구현할 수 있다.

```java
public String addItem(@Validated @ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes, Model model) {  
    if (bindingResult.hasErrors()) {  
        return "validation/addForm";  
    }  
    Item savedItem = itemRepository.save(item);  
    redirectAttributes.addAttribute("itemId", savedItem.getId());  
    return "redirect:/items/{itemId}";  
}
```

> [!tip]  
> `@Validated`는 `javax.validation.Valid`와 유사하지만, `groups` 기능 등 [[Bean Validation]]의 고급 기능을 사용할 때 유리하다.

---

## 🔗 관련 문서

- [[Bean Validation vs Validator]]
- [[BindingResult]]
- [Spring 공식 문서 - Validator](https://docs.spring.io/spring-framework/reference/core/validation/validator.html)
