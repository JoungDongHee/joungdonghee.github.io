---
tags:
  - 개발
  - 기술정리
  - "#개발"
  - "#기술정리"
  - "#자바"
  - "#객체지향프로그래밍"
  - "#this"
created: 2025-05-07
type: 기술정리
aliases:
  - 자바 this
  - this 키워드
  - 자바 생성자 호출
  - this() 사용법
  - 객체지향 this
---

# 📘 자바 this 키워드 완벽 정리

## this 키워드란?

다음 코드 에서 볼 수 있듯이, `this.name`, `this.age`, `this.grade`와 같이 `this` 키워드가 사용되었습니다. `this`는 해당 객체 내에서 자기 자신을 참조할 때 사용하는 문법입니다.

```java
public class MemberInit {
    String name;
    int age;
    int grade;

    public MemberInit(String name, int age, int grade) {
        // this는 인스턴스 변수를 참조함
        this.name = name;
        this.age = age;
        this.grade = grade;
    }
}
```

이 코드에서는 매개변수와 인스턴스 변수가 같은 이름을 가지기 때문에, `this`를 사용하여 인스턴스 변수를 명시적으로 구분합니다. 만약 `this`가 생략되면, 매개변수와 인스턴스 변수가 혼동될 수 있습니다.

```java
void initMember(String name, int age, int grade) {
    name = name;  // 매개변수 name을 그대로 다시 할당
    age = age;
    grade = grade;
}
```

이 경우, 아무런 변화도 일어나지 않으며, 결과적으로 객체의 상태는 초기값으로 유지됩니다.

> [!result] 
>이름: null 나이: 0 성적: 0  
>이름: null 나이: 0 성적: 0

이는 `this`를 사용하지 않아 인스턴스 변수에 접근하지 않고, 매개변수 자체에 값을 다시 할당했기 때문에 발생하는 문제입니다.

## 생성자에서의 this() 사용법

`this.`는 인스턴스의 멤버 변수에 접근할 때 사용되지만, **`this()`** 는 동일 클래스 내의 다른 생성자를 호출할 때 사용됩니다. 이를 통해 생성자 간 중복된 코드를 제거할 수 있습니다.

다음 예시에서는 `this()`를 사용하여 중복 코드를 줄였습니다.

```java
public class MemberConstruct {
    String name;
    int age;
    int grade;

    // 생성자 간 중복 코드를 줄이기 위해 this() 사용
    MemberConstruct(String name, int age) {
        this(name, age, 50);  // 기본 성적을 50으로 설정하는 생성자 호출
    }

    MemberConstruct(String name, int age, int grade) {
        this.name = name;
        this.age = age;
        this.grade = grade;
    }
}
```


위 코드에서 `this(name, age, 50);`는 `MemberConstruct(String name, int age, int grade)` 생성자를 호출하여 중복 코드를 제거하는 방법입니다.
