---
tags:
  - 디자인패턴
  - MVC
  - 웹아키텍처
aliases:
  - MVC
  - Model-View-Controller
  - MVC 패턴 설명
  - MVC Pattern
date: 2025-02-10 13:02
author: Joung Dong Hee
---

# MVC 패턴

![MVC 구조도](https://file-api.ksq9511.synology.me:5353/obsidian-image/20250210130563.png)

**MVC 패턴(Model-View-Controller)** 은 소프트웨어 설계에서 [[관심사의 분리(Separation of Concerns)]]를 실현하기 위한 대표적인 디자인 패턴입니다. 애플리케이션을 **Model(모델)**, **View(뷰)**, **Controller(컨트롤러)** 로 구분하여 각 구성 요소의 책임을 분리함으로써 유지보수성과 확장성을 높입니다.

---

## MVC 패턴의 개념

- **Model (모델)**: 애플리케이션의 **데이터 및 비즈니스 로직**을 담당합니다.
- **View (뷰)**: 사용자에게 데이터를 시각적으로 표현하는 역할을 합니다.
- **Controller (컨트롤러)**: 사용자의 입력을 받아 Model을 조작하고, View에 데이터를 전달하는 **중개자 역할**을 합니다.

---

## 각 구성 요소의 역할

### 🔹 Model (모델)

- 애플리케이션의 **데이터를 관리**하며, 비즈니스 로직을 포함합니다.
- 일반적으로 데이터베이스와의 상호작용을 포함합니다.

#### ✅ 예제 (Java)

```java
public class User {
    private String id;
    private String name;
    
    public User(String id, String name) {
        this.id = id;
        this.name = name;
    }
    
    public String getId() { return id; }
    public String getName() { return name; }
}
```

---

### 🔹 View (뷰)

- 사용자 인터페이스(UI)를 담당합니다.
- Controller를 통해 전달받은 데이터를 표시하며, 비즈니스 로직은 포함하지 않습니다.

#### ✅ 예제 (Thymeleaf 기반 View)

```html
<html>
<body>
    <h1>사용자 정보</h1>
    <p>아이디: <span th:text="${user.id}"></span></p>
    <p>이름: <span th:text="${user.name}"></span></p>
</body>
</html>
```

---

### 🔹 Controller (컨트롤러)

- 사용자 입력(요청)을 받아 적절한 Model을 호출하고 처리 결과를 View로 전달합니다.
- Model과 View 사이의 **다리 역할(Bridge)** 을 수행합니다.

#### ✅ 예제 (Servlet 기반)

```java
@WebServlet("/user")
public class UserServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) {
        User user = new User("1", "홍길동");
        request.setAttribute("user", user);
        request.getRequestDispatcher("/userView.jsp").forward(request, response);
    }
}
```

---

## MVC 패턴의 장점

- ✅ **관심사의 분리 (Separation of Concerns)**: 각 계층의 책임이 분명하여 유지보수가 용이합니다.
- ✅ **유연성 및 확장성**: UI(View)를 변경해도 비즈니스 로직(Model)은 영향을 받지 않습니다.
- ✅ **코드 재사용성 증가**: 동일한 Model을 다양한 View에서 재사용할 수 있습니다.

---

## 추가로 알아두면 좋은 점

- 🔄 **MVC의 변형 패턴**
    - **MVVM (Model-View-ViewModel)**: ViewModel을 통해 View와 Model을 분리 (예: Angular, Vue.js)
    - **MVP (Model-View-Presenter)**: View와 Presenter가 상호작용하며 Model을 제어
- 🌍 MVC는 웹뿐만 아니라 데스크탑, 모바일 애플리케이션에도 적용 가능
- 🔧 주요 프레임워크: Spring MVC, ASP.NET MVC, Django 등

---

## 결론

MVC 패턴은 유지보수성과 확장성이 중요한 현대 애플리케이션 개발에서 널리 사용됩니다. 각 구성 요소의 역할을 명확히 하고, 효율적인 아키텍처 설계를 통해 개발 생산성을 높일 수 있습니다.
