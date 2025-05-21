---
tags:
  - 개발
  - 기술정리
  - "#제어역전"
  - "#IoC컨테이너"
  - "#스프링빈"
created: 2025-05-09
type: 기술정리
aliases:
  - 제어 역전 원리
  - IoC 컨테이너
  - 스프링 프레임워크
  - 의존성 주입
  - Spring Bean 관리
---

# 📘IoC(Inversion of Control)


## IoC(제어 역전) 이란?

직역하면 "제어 의 역전" 이라는 의미 이다. 프로그래밍 관점으로 얘기한다면 **메소드,객체 생성 , 생명 주기 등 을 사용하는 개발자가 정하는 것 이 아니라 외부에서 결정되는 것을 말한다.**

자바에서는 다음과 같이 `new` 키워드를 사용하여 인스턴스를 생성 및 사용을 한다.  

```java
Book book = new Book()
```

그리고 이렇게 생성된 객체를 사용하고자 할때 다음과 같이 `Book 클래스` 에 있는 메소드를 직접 호출하고 사용한다.

```java
public static void main(String[] args) {  
	Book book = new Book("java",20000);  
	System.out.println(book.getTitle());  
	System.out.println(book.getPrice());  
}  
```

하지만 SpringFramwork 에서는 이러한 행위를 Spring 프레임 워크 내부에서 진행한다. 마치 DIP 에서 설명한 `AppConfig` 클래스 와 같이 말이다.

그리고 `AppConfig` 처럼 객체를 생성하고 관리하면서 의존 관계를 연결해주는 것 을 `IoC컨테이너` 혹은 `DI컨테이너` 라고 한다.

```java
public class AppConfig {  
    public OrderService orderService(){  
        return new OrderServiceImpl(discountPolicy());  
    }  
  
    private DiscountPolicy discountPolicy() {  
        return new RateDisCountPolicy();  
    }  
}
```

## IoC 컨테이너의 역할 및 Bean 등록

IoC 의 컨테이너는 객체의 생명주기 , 설정 관리 , Bean 의 범위 설정 등을 담당하며 이렇게 `IoC 컨테이너` 에서 관리 하는 객체를 Spring Bean 라고 한다. IoC 컨테이너는 Spring 의존성 주입 DI(Dependency Injection)를 통해 객체 간의 의존 관계를 설정한다.

Spring 에서는 다양하게 `IoC 컨테이너` 에 `Bean` 을 등록할수 있으며 `@Component` , `@Autowired`,`@Bean` 등과 같은 애노테이션 을 사용하여 Bean 객체로 등록이 가능하다.

```java
@Component
public class BookService {
    private final BookRepository bookRepository;

    // Constructor-based DI
    @Autowired
    public BookService(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }

    public void getBookInfo() {
        Book book = bookRepository.findBook();
        System.out.println(book.getTitle());
        System.out.println(book.getPrice());
    }
}

```
