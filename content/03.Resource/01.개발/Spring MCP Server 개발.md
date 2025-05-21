---
tags:
  - 개발
  - 기술정리
created: 2025-05-20
type: 기술정리
aliases:
  - Spring MCP
  - MCP Server with Spring
---

# 📘 Spring 을 활용한 MCP Server 개발

[[Model Context Protocol 정리|MCP]]가 큰 주목을 받고 있는 가운데, Java 진영에서도 Spring을 활용한 MCP 서버 개발이 본격화되고 있다. Spring에서는 이를 위한 전용 스타터 모듈을 지원하여 빠르게 MCP 서버를 구성할 수 있다.

---

## ✅ 필요한 의존성

다음의 의존성 하나만 추가하면 Spring Boot 기반의 MCP 서버를 구현할 수 있다:

```xml
<dependency>  
    <groupId>org.springframework.ai</groupId>  
    <artifactId>spring-ai-starter-mcp-server</artifactId>  
</dependency>
```

---

## ⚙️ 기본 구조 설명

### 1. `Course` 모델 클래스

```java
public class Course {
    private String title;
    private String url;

    public Course(String title, String url) {
        this.title = title;
        this.url = url;
    }

    public String getTitle() {
        return title;
    }

    public String getUrl() {
        return url;
    }
}
```

### 2. `CourseService` - Tool을 제공하는 서비스

```java
@Service
public class CourseService {

    private final List<Course> courses = new ArrayList<>();

    @Tool(name = "dv_get_courses", description = "Get a list of courses from Dan Vega")
    public List<Course> getCourses() {
        return List.copyOf(courses);
    }

    @Tool(name = "dv_get_course", description = "Get a single course from Dan Vega by title")
    public Course getCourse(String title) {
        if (title == null || title.isBlank()) return null;
        return courses.stream()
            .filter(course -> course.getTitle().equalsIgnoreCase(title))
            .findFirst()
            .orElse(null);
    }

    @PostConstruct
    public void init() {
        courses.addAll(List.of(
            new Course("Building Web Applications with Spring Boot (FreeCodeCamp)", "https://youtu.be/31KTdfRH6nY"),
            new Course("Spring Boot Tutorial for Beginners - 2023 Crash Course using Spring Boot 3", "https://youtu.be/UgX5lgv4uVM")
        ));
    }
}
```

---

## 🧠 `@Tool` 애노테이션이란?

Spring AI에서 제공하는 `@Tool` 애노테이션은 LLM(Large Language Model)과 상호작용할 수 있는 도구 메서드를 정의하는 데 사용된다.

공식 문서 기준:

> The `@Tool` annotation is used to expose methods as callable tools for language models such as OpenAI or Claude. This allows the model to call your methods based on natural language prompts.  
> – [Spring AI 공식 문서](https://docs.spring.io/spring-ai/reference/api/tools.html)

### 핵심 속성

| 속성 | 설명 |
|------|------|
| `name` | 툴 호출 시 사용할 이름 (`dv_get_courses` 등) |
| `description` | LLM이 기능을 이해하도록 돕는 자연어 설명 |

LLM은 `@Tool`이 달린 메서드를 "명령어(Command)"처럼 호출할 수 있다. 예를 들어 `"Show me the Spring Boot courses"`와 같은 요청을 하면 AI는 자동으로 `dv_get_courses` 메서드를 호출하게 된다.

---

## 🧱 MCP 서버 초기화 – `SpringMcpApplication`

```java
@SpringBootApplication
public class SpringMcpApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringMcpApplication.class, args);
    }

    @Bean
    public List<ToolCallback> danTools(CourseService courseService) {
        return List.of(ToolCallbacks.from(courseService));
    }
}
```

`ToolCallbacks.from(...)` 메서드는 `@Tool` 애노테이션이 지정된 메서드를 탐색하여 AI가 호출할 수 있는 `ToolCallback` 리스트로 변환한다.

---

## 🔧 application.properties 설정

MCP는 웹 서버가 아닌 STDIO 기반으로 작동하기 때문에 다음 설정이 필수이다:

```properties
spring.application.name=courses
spring.main.web-application-type=none
spring.ai.mcp.server.name=dan-vega-mcp
spring.ai.mcp.server.version=0.0.1

# STDIO 통신을 위해 배너 및 콘솔 로그 제거
spring.main.banner-mode=off
logging.pattern.console=
```

이 설정을 통해 MCP 서버는 LLM과의 통신에서 오염되지 않은 JSON만을 표준 입출력으로 송수신하게 된다.

---

## 📌 정리

- `@Tool` 애노테이션을 통해 LLM이 호출 가능한 메서드를 정의할 수 있다.
- `ToolCallbacks.from()`를 통해 Spring Bean을 MCP Tool로 변환한다.
- `STDIO` 기반 통신을 위해 콘솔 출력을 비활성화해야 한다.
- Spring AI MCP는 LLM과의 통합을 위한 간단하면서도 유연한 방식을 제공한다.

---

## 🔗 참고 자료

- [Spring AI MCP Server 공식 문서](https://docs.spring.io/spring-ai/reference/api/tools.html)
- [Dan Vega의 Spring AI 소개 영상](https://www.youtube.com/watch?v=w5YVHG1j3Co&t=262s&ab_channel=DanVega)
