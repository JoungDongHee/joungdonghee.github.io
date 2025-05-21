---
tags:
  - 개발
  - 기술정리
  - "#JavaFinal"
  - "#상수선언"
  - "#불변성"
  - "#final키워드"
created: 2025-05-09
type: 기술정리
aliases:
  - Java 상수
  - 불변 변수
  - final 필드
  - final 메서드
  - final 클래스
---

# 📘 Final
자바에서는 상수(변하지 않는 값) 를 표현하기 위해 `final static` 과 같은 키워드를 사용하여 상수를 표현한다.

> 영문으로 `final` 을 해석하면 `마지막` 이라는 의미 이다.

그렇다면 java 에서의 마지막은 무엇을 의미할까?

## Final 이란?

### 변수의 final 선언

`변수` 에 **final** 키워드가 붙을 경우 해당 변수의 값은 더이상 변경이 불가능하다.

```java
public static void main(String[] args) {  
    String name = "test";  
    name = "test1";  
    System.out.println(name);  
}
```

> [!example]+ Result
> test1

위 처럼 `name` 변수에 `test` 라고 변수 를 저장한뒤 중간에 바꾸거나 수정이 가능하다.  
그렇기 때문에 결론적으로 두번째 줄에 바꾼 `test1` 이 출력이 된다.

하지만 해당 `name` 변수를 **final** 키워드를 사용하여 변수를 선언할경우 어떻게 될까?

```java
public static void main(String[] args) {  
    final String name = "test";  
    // name = "test1";  컴파일 오류
  
    System.out.println(name);  
}
```

> [!failure]+ Result
> java: cannot assign a value to final variable name

위 처럼 **final** 키워드를 사용하여 변수를 선언할경우 중간에 값을 바꿀려고 할경우 컴파일 오류로 인해 실행이 불가능하다.

### final 필드 선언

final 을 클래스 내부의 필드 로 선언할경우 따로 변수에 저장하지 않을경우 이때에는 생성자를 통해 **딱 한번만 초기화 될수 있다**

```java
public class Example1{
	final int value;
	public Example1(int value){
		this.value = value;
	}
}
```

만약 다음과 같이 필드 선언과 동시에 초기화 할경우 더이상의 값 변경은 불가능하다.

```java
public class Example1{
	final int value = 10;
}
```

### 메서드의 final 선언

**Final** 을 메서드에 사용할경우 이 오버라이딩 과 오버로딩 이 불가능하다.

final 문자 그대로 "끝났다" 라고 이미 선언하였기 때문에 메서드를 재정의 할수 없습니다.

### 클래스의 final 선언

**Final** 키워드는 Class 에도 선언이 가능합니다.

이렇게 선언한 `Final` 클래스는 상속 이 불가능 하기 때문에 클래스 그대로 사용해야만 한다.
예를들어 특정 로직을 수행하고 종료하는 `Util` 클래스 나 여러상수 들을 모아놓는 클래스에 final 키워드를 선언해야만 한다.

```java
public final class Example1{
	final int value = 10;
}
```

## Final 특징

1. final 을 지역변수에 설정 할경우 **최초 한번만** 할당이 가능하다 이후에 변수값을 변경하고자 할경우 컴파일 오류가 발생한다.
2. final 을 변수 선언과 동시에 초기화 할경우 이미 값이 할당되어 있기 때문에 값 의 재할당이 불가능 하다.
3. 매개변수에 final 이 붙을 경우 메서드 내부에서 매개변수 값을 변경할수 없다. 따라서 메서드 호출 시점에 사용된 값이 끝까지 사용된다.
