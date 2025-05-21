---
tags:
  - 개발
  - 기술정리
  - "#Autowired"
  - "#의존성주입"
  - "#생성자주입"
  - "#Spring프레임워크"
created: 2025-05-09
type: 기술정리
aliases:
  - Spring 의존성 주입
  - "@Autowired 사용법"
  - Spring 자동 주입
  - 생성자 주입 권장
  - Spring DI
---

# 📘 @Autowired 어노테이션


## @Autowired 어노테이션이란?

`@Autowired`는 Spring Framework에서 가장 많이 사용되는 어노테이션 중 하나로, 의존성 자동 주입(Dependency Injection)을 위해 사용됩니다. Spring의 IoC 컨테이너는 이 어노테이션이 붙은 필드, 생성자, 메서드 등에 자동으로 의존 객체를 주입해줍니다.


## @Autowired 사용 위치

`@Autowired` 어노테이션은 다음과 같은 위치에 사용할 수 있습니다:

1. **필드(Field)**: 클래스의 멤버 변수에 직접 사용
2. **생성자(Constructor)**: 클래스의 멤버 변수에 직접 사용
3. **setter 메서드**: 속성 설정을 위한 메서드에 사용

## 주입 방식별 예제

### 1. 필드 주입 (Field Injection)

```java
@Component
public class UserService {
    @Autowired
    private UserRepository userRepository;
    
    // 메서드...
}
```

**특징**:
- 가장 간단한 방식이지만 테스트하기 어려움
- Spring 컨테이너 없이는 의존성 주입이 불가능
- 불변성이 보장되지 않음

### 2. 생성자 주입 (Constructor Injection) - 권장 방식 ✅

```java
@Component
public class UserService {
    private final UserRepository userRepository;
    
    @Autowired // Spring 4.3+ 에서는 생략 가능
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    // 메서드...
}
```

**특징**:
- 불변성 보장 (final 키워드 사용 가능)
- 필수 의존성을 명확히 표현
- 테스트하기 용이함
- 순환 의존성 감지 가능

### 3. 수정자 주입 (Setter Injection)

```java
@Component
public class UserService {
    private UserRepository userRepository;
    
    @Autowired
    public void setUserRepository(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    // 메서드...
}
```

**특징**:
- 선택적 의존성에 유용
- 런타임에 의존성 변경 가능 (하지만 일반적으로 권장되지 않음)

## @Autowired의 동작 원리

1. Spring IoC 컨테이너는 애플리케이션 컨텍스트를 로드하면서 Bean들을 등록합니다.
2. `@Autowired`가 붙은 의존성을 확인합니다.
3. 해당 타입에 맞는 Bean을 찾아 자동으로 연결합니다.
4. 여러 개의 후보 Bean이 있을 경우:
   - `@Primary` 어노테이션이 있는 Bean 우선
   - `@Qualifier` 어노테이션으로 지정된 Bean 사용
   - 이름이 변수명과 일치하는 Bean 사용

## @Autowired 사용 시 주의사항

- **순환 참조(Circular Dependency)**: 두 Bean이 서로를 의존하면 문제가 발생할 수 있습니다.
- **단일 Bean 조건**: 같은 타입의 Bean이 여러 개 있을 경우 `@Qualifier`나 `@Primary`를 사용해야 합니다.
- **주입 시점**: 객체 생성 후 의존성이 주입되므로, 생성자에서는 주입된 객체를 사용할 수 없습니다. (필드, 수정자 주입의 경우)

## @Autowired의 required 속성

기본적으로 `@Autowired`는 필수 의존성(required=true)으로 간주되어, 일치하는 Bean을 찾지 못하면 예외가 발생합니다.

```java
@Autowired(required = false)
private OptionalService optionalService; // 해당 Bean이 없어도 예외 발생하지 않음
```

## 최신 Spring에서의 변화

Spring 4.3부터는 클래스에 생성자가 하나만 있을 경우 자동으로 의존성 주입이 수행되어 `@Autowired` 어노테이션을 생략할 수 있습니다.
