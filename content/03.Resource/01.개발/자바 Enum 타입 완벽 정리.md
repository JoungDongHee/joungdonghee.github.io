---
tags:
  - 개발
  - 기술정리
  - "#Enum타입"
  - "#자바Enum"
  - "#열거형타입"
created: 2025-05-09
type: 기술정리
aliases:
  - 자바 열거형
  - Enum 활용
  - 자바 Enum 예제
  - Enum 타입 직접 구현
  - 상수 vs Enum
---

# 📘 # Enum Type

자바에서는 Enum Type 이라고 불리는 **열거형 타입**이 존재한다. 열거형 타입 은 고정된 상수들의 집합을 표현하는 특별한 데이터 타입 이며 변수에 미리 정해진 값만 사용할수 있도록 제한할수 있다.

## Enum Type을 사용하는 이유

다음 코드에서는 `discount()` 메서드가 `grade`를 입력받아 `BASIC`, `GOLD`, `DIAMOND` 값에 따라 할인을 적용합니다.

기능적으로는 문제가 없지만, `grade` 입력에 실수가 발생할 경우 문제가 생길 수 있으며, 서비스가 커질수록 유지보수와 확장성 측면에서 한계가 있습니다.

### 기본 코드 예시

#### Service Class

```java
public class DiscountService {  
    public int discount(String grade , int price){  
        int discountPercent = 0;  
  
        if(grade.equals("BASIC")){
            discountPercent = 10;  
        }else if(grade.equals("GOLD")){
            discountPercent = 20;  
        }else if(grade.equals("DIAMOND")){
            discountPercent = 30;  
        }else {  
            System.out.println(grade + ":할인 x");  
        }
  
        return price * discountPercent / 100;  
    }
}
```

#### Main Class

```java
public class StringGradeEx0_1 {  
    public static void main(String[] args) {  
        int price = 10000;  
  
        DiscountService discountService = new DiscountService();  
        int basic = discountService.discount("BASIC",price);
        int gold = discountService.discount("gold",price);
        int diamond = discountService.discount("DIAMOND",price);  
  
  
        System.out.println("BASIC 등급의 할인 금액 : "+ basic);  
        System.out.println("gold 등급의 할인 금액 : "+ gold);  
        System.out.println("diamond 등급의 할인 금액 : "+ diamond);  
    }
}
```

이 코드는 몇 가지 문제를 가지고 있습니다. **오타**나 **잘못된 값**이 입력될 가능성이 있으며, 컴파일 시 이러한 문제를 잡지 못하고 런타임에서만 오류가 발생할 수 있습니다. 또한, 데이터 일관성이 떨어질 수 있습니다 (`GOLD`, `gold`, `Gold` 등 다양하게 입력될 수 있음).

> [!danger]  Danger
> -   타임의 안전성 부족 : 사용자가 문자열을 입력하여 관리하는 경우 오타가 발생하기 쉽고 유효하지 않는 값이 입력될수 있다.
> -  값의 제한 부족: String 으로 상태나 카테고리를 표현하면, 잘못된 문자열을 실수로 입력할 가능성이 있다. 예를들어, "Monday", "Tuesday" 등을 나타내는 데 String 을 사용한다면, 오타("Munday")나 잘못된 값 ("Funday")이 입력될 위험이 있다.
>  - 컴파일 시 오류 감지 불가: 이러한 잘못된 값은 컴파일 시에는 감지되지 않고, 런타임에서만 문제가 발견되기 때문 에 디버깅이 어려워질 수 있다.
>   - 데이터 일관성 : GOLD , gold ,Gold 등 과 같이 다양한 문자열을 입력할수 있기 때문에 일관성이 떨어진다.

## 해결책 1: 상수(Constants) 사용

이 문제를 해결하기 위해 상수를 사용하여 `grade`를 관리할 수 있습니다.

```java
public class StringGrade {  
    public static final String BASIC = "BASIC";  
    public static final String GOLD = "GOLD";  
    public static final String DIAMOND = "DIAMOND";  
}
```

이 경우 `grade` 값에 정해진 상수만 사용할 수 있게 되어 코드의 안전성이 높아집니다.

### 수정된 Service 클래스

```java
public class DiscountService {  
    public int discount(String grade , int price){  
        int discountPercent = 0;  
  
        if(grade.equals(StringGrade.BASIC)){
            discountPercent = 10;  
        }else if(grade.equals(StringGrade.GOLD)){
            discountPercent = 20;  
        }else if(grade.equals(StringGrade.DIAMOND)){
            discountPercent = 30;  
        }else {  
            System.out.println(grade + ":할인 x");  
        }
  
        return price * discountPercent / 100;  
    }
}
```

### 수정된 Main 클래스

```java
public class StringGradeEx1_1 {  
    public static void main(String[] args) {  
        int price = 10000;  
        DiscountService discountService = new DiscountService();  
        int basic = discountService.discount(StringGrade.BASIC,price);
        int gold = discountService.discount(StringGrade.GOLD,price);
        int diamond = discountService.discount(StringGrade.DIAMOND,price);  
        System.out.println("BASIC 등급의 할인 금액 : "+ basic);  
        System.out.println("gold 등급의 할인 금액 : "+ gold);  
        System.out.println("diamond 등급의 할인 금액 : "+ diamond);  
    }
}
```

하지만 여전히 문자열로 등급을 표현하기 때문에, 존재하지 않는 등급이 입력될 경우 문제가 발생할 수 있습니다. 예를 들어, `vip`나 `오타`로 인한 잘못된 입력이 들어가도 컴파일 시 오류가 발생하지 않습니다.

```java
public class StringGradeEx1_2 {  
    public static void main(String[] args) {  
        int price = 10000;  
        DiscountService discountService = new DiscountService();  
        
        // 존재 하지 않는 등급 입력  
        int vip = discountService.discount("vip",price);
        System.out.println("vip 등급의 할인 금액 : "+ vip);  
        
        //오타  
        int diamoondd = discountService.discount("DIAMOONDD", price);  
        System.out.println("diamond 등급의 할인 금액 : "+ diamoondd);  
        
        //소문자 입력  
        int gold = discountService.discount("gold", price);  
        System.out.println("diamond 등급의 할인 금액 : "+ gold);  
    }
}
```

## 해결책 2: Enum 타입 사용

이러한 문제를 완전히 해결하기 위해서는 **Enum 타입**을 사용하는 것이 좋습니다. Enum을 사용하면 값과 타입 모두에 제한을 두어, 유효하지 않은 값이나 오타로 인한 문제를 방지할 수 있습니다

### Enum 타입 정의

```java
public enum Grade {  
    BASIC , GOLD , DIAMOND;  
}
```

이제 `grade` 값은 Enum 타입으로만 받을 수 있으며, 잘못된 값이 입력될 경우 컴파일 시 오류가 발생합니다.

#### 수정된 Service 클래스

``` java 
public class DiscountService {  
    public int discount(Grade grade, int price) {  
        int discountPercent = 0;  
        switch (grade) {  
            case BASIC:
                discountPercent = 10;  
                break;  
            case GOLD:
                discountPercent = 20;  
                break;  
            case DIAMOND:
                discountPercent = 30;  
                break;  
        }
  
        return price * discountPercent / 100;  
    }
}
```

#### 수정된 Main 클래스

```java
public class EnumGradeExample {  
    public static void main(String[] args) {  
        int price = 10000;  
        DiscountService discountService = new DiscountService();  
        int basic = discountService.discount(Grade.BASIC, price);  
        int gold = discountService.discount(Grade.GOLD, price);  
        int diamond = discountService.discount(Grade.DIAMOND, price);  
        System.out.println("BASIC 등급의 할인 금액: " + basic);  
        System.out.println("GOLD 등급의 할인 금액: " + gold);  
        System.out.println("DIAMOND 등급의 할인 금액: " + diamond);  
    }
}
```

참고로 Enum 을 사용할 때에는 다음 코드에서 처럼 `import static enumeration.ex3.Grade.*`  와 같이 선언하면 좀더 가독성이 좋게 코드를 만들수 있다. 

``` java
import static enumeration.ex3.Grade.*;  
  
public class ClassGradeEx3_1 {  
    public static void main(String[] args) {  
        int pirce = 10000;  
  
        DiscountService discountService = new DiscountService();  
        int basic = discountService.discount(Grade.BASIC, pirce);  
        int gold = discountService.discount(GOLD, pirce);  
        int diamond = discountService.discount(DIAMOND, pirce);  
  
        System.out.println("BASIC 등급의 할인 금액 : "+ basic);  
        System.out.println("gold 등급의 할인 금액 : "+ gold);  
        System.out.println("diamond 등급의 할인 금액 : "+ diamond);  
    }
}
```

## Enum Type 직접 구현

위에 모든 내용을 종합적으로 볼때 우리는 회원 등급 관리를 위해서는 2가지의 조건이 충족해야만 한다.

1. 값에 대한 제한 을 둬야한다.
    * 회원 등급은 BASIC , GOLD , DIAMOND 만 입력이 되어야 하며 반드시 대문자로 관리된다.
2. 타입에 대한 제한을 둬야 한다.
    *  회원 등급은 특정 Class 타입 이외에 다른 값이 입력 되지 못하도록 해야한다.

회원 등급이 정의되어 있는 `ClassGrade` 는 `static final` 키워드를 사용하여 상수로 선언하며 `각 각의 상수는 서로다른 인스턴스를 가지도록 new 를 사용하여 선언한다.`

```java
public class ClassGrade {  
    public static final ClassGrade BASIC = new ClassGrade();  
    public static final ClassGrade GOLD = new ClassGrade();  
    public static final ClassGrade DIAMOND = new ClassGrade();  
    
    // private 생성자 추가  
    private ClassGrade(){  
        // 외부에서 생성은 불가능 하며 private  을 선언함으로서 내부에서만 생성 가능  
    }
}
```

위 코드를 도식화 한다면 다음과 같이 각각의 서로 다른 인스턴스를 참조하는 객체가 생성된다.


![image.png](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250516072494_image.png)


이를 활용하여 Service Class 를 재 구현 하면 다음과 같이 된다. 기존 Enum Type#^bc246f |Service class 와는 다르게  인자에 **ClassGrade classGrade** 로 타입 제한을 걸어둠으로서 문자열이 아닌 객체를 직접적으로 받도록 수정하였다. 

이렇게 함으로서 우리는 회원등급을 안전하게 관리하는 코드를 완성할수 있다.

```java
public class DiscountService {  
    public int discount(ClassGrade classGrade , int price){  
        int discountPercent = 0;  
  
        if(classGrade == ClassGrade.BASIC){
            discountPercent = 10;  
        }else if(classGrade == ClassGrade.GOLD){
            discountPercent = 20;  
        }else if(classGrade == ClassGrade.DIAMOND){
            discountPercent = 30;  
        }else {  
            System.out.println("할인 X");  
        }
  
        return price * discountPercent / 100;  
    }
}
```

## Enum Type Method

Enume Type 는 기본적으로  `java.lang.Enum` 을 상속 받는 객체이기 때문에 해당 클래스에서 제공하는 Method 를 사용할수 있다

1. values() : 모든 ENUM 상수를 포함하는 배열을 반환한다
2. valuesOf(String name) : 주어진 인자와 일치하는 Enue 상수를 반환한다. 
3. name() : ENUM 상수의 이름을 문자열로 반환한다.
4. ordinal() : ENUM 상수의 선언 순서(0부터 시작)를 반환한다.
5. toString(): ENUM 상수의 이름을 문자열로 반환한다. name() 메서드와 유사하지만, toString() 은 직접 오버라이드 할 수 있다.

``` java
public class EnumeMethodMain {  
    public static void main(String[] args) {  
        // 모든 Enum 반환  
        Grade[] values = Grade.values();  
        System.out.println("values = "+ Arrays.toString(values));  
  
        // Enum 의 이름 과 순서 반환  
        for (Grade value : values){
            System.out.println("name = "+value.name()+", ordinal = "+value.ordinal());  
        }
  
        // String -> ENUM 반환  
        String input = "GOLD";  
        Grade gold =Grade.valueOf(input);  
        System.out.println("gold = "+gold);  
    }
}
```

> [!note|result]
> values = BASIC, GOLD, DIAMOND
> name=BASIC, ordinal=0
> name=GOLD, ordinal=1
> name=DIAMOND, ordinal=2
> gold = GOLD

## Enum 타입의 장점

1. **타입 안전성**: 잘못된 값이 입력되는 것을 방지할 수 있습니다.
2. **데이터 일관성**: 정해진 상수만 사용하므로 데이터의 일관성이 유지됩니다.
3. **확장성**: 새로운 값이 추가될 경우 Enum에 상수만 추가하면 됩니다.
4. **가독성**: 코드가 더 명확하고 읽기 쉽습니다.
