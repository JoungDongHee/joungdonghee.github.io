---
date: 2024 년 10 월 05 일 22 시 10 분
tags:
  - Dev
  - Java
  - DTO
  - VO
  - "#데이터전송객체"
  - "#값객체"
  - "#객체불변성"
aliases:
  - 데이터 전송 객체
  - 값 객체
  - DTO VO 비교
  - Java 객체
  - 객체 설계
---

# 📘DTO (**Data Transfer Object**)

## DTO란?

DTO는 **Data Transfer Object**의 약자로, **데이터 전송**을 위한 객체입니다. 주로 **계층 간**의 데이터 이동을 위해 사용됩니다.

계층 간 이동은 주로 **Controller → Service → DAO → DB** 순서로 이루어지며, 각 계층 간에 데이터를 주고받을 때 DTO가 사용됩니다.

DTO는 일반적으로 **getter**와 **setter** 메서드를 사용하며, 그 외에 **비즈니스 로직이 포함되지 않는** 순수한 데이터 전송 객체입니다.

클래스명에 `DTO`를 붙일 때는 보통 대문자로 표기합니다.

```Java
public class BoardDTO {  
    private String title;  
    private String content;  
    private int count;  
  
    public void setTitle(String title) {  
        this.title = title;  
    }  
  
    public void setContent(String content) {  
        this.content = content;  
    }  
  
    public void setCount(int count) {  
        this.count = count;  
    }  
  
    public String getTitle() {  
        return title;  
    }  
  
    public String getContent() {  
        return content;  
    }  
  
    public int getCount() {  
        return count;  
    }  
}

```

## VO (**Value Object**)

VO는 **Value Object**로, **값을 표현하는 객체**를 의미합니다.

VO와 DTO의 가장 큰 차이점은 **불변성(immutable)** 입니다. **VO는 값 자체를 나타내며 변경되지 않도록 설계**되어야 합니다. 

반면, DTO는 계층 간 데이터 전송을 목적으로 하므로, 데이터의 변경을 허용하는 setter 메서드가 있을 수 있습니다.

VO는 값 그 자체를 표현하기 때문에 일반적으로 **getter** 만 사용하고, **setter는 사용하지 않으며** 생성 시점 이후 값이 변경되지 않도록 합니다. **VO는 본질적으로 읽기 전용 객체**로 설계되어야 합니다.


```java
public class BoardVO {  
    private final String title;  
    private final String content;  
    private final int count;  
  
    public BoardVO(String title, String content, int count) {
        this.title = title;
        this.content = content;
        this.count = count;
    }

    public String getTitle() {  
        return title;  
    }  
  
    public String getContent() {  
        return content;  
    }  
  
    public int getCount() {  
        return count;  
    }  
}

```

### VO의 불변성

VO의 중요한 속성 중 하나는 **불변성**입니다. 값을 한 번 설정하면 변경할 수 없도록 해야 합니다. 이는 VO가 시스템 내에서 **안정적인 값을 전달**하는 역할을 하기 때문입니다. 주로 **도메인 로직**에서 사용되는 중요한 객체입니다.

## DTO와 VO 활용 예시

다음은 위에서 설명한 DTO와 VO를 활용한 **Rest API** 예시입니다.

- `BoardDTO`는 클라이언트로부터 데이터를 받아 **비즈니스 로직**을 처리하는 데 사용됩니다.
- `BoardVO`는 비즈니스 로직의 결과 값을 나타내는 **불변 객체**로 반환됩니다.

```java
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/boards")
public class BoardController {

    @PostMapping("/create")
    public BoardVO createBoard(@RequestBody BoardDTO boardDTO) {
        // BoardDTO를 사용하여 비즈니스 로직 수행
        System.out.println("Received DTO: " + boardDTO.getTitle() + ", " + boardDTO.getContent());

        // 비즈니스 로직 수행 후, 결과를 VO로 생성하여 반환
        BoardVO boardVO = new BoardVO("Created Board Title", "Created Board Content", 100);
        return boardVO;
    }
}

```

위 예시에서, `BoardDTO`는 데이터를 받아서 처리하고, 결과는 **불변성을 가진** `BoardVO`로 반환됩니다. DTO는 데이터를 받아서 가공하는 객체로, VO는 그 결과값을 표현하는 객체입니다.

## DTO 와 VO의 차이점 비교

||DTO|VO|
|---|---|---|
|용도|계층 간 데이터 이동|값 자체의 표현|
|가변성|가변적 (setter 존재 시)|불변 (setter 없음)|
|메서드 포함 여부|getter, setter 외에도 비즈니스 로직 포함 가능|값 자체의 변경을 위한 메서드 없음 (불변성 유지)|