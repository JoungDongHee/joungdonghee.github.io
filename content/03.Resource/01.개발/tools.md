# 도구 호출 (Tool Calling)

_Tool calling_ (또는 _function calling_)은 AI 애플리케이션에서 일반적으로 사용되는 패턴으로, 모델이 일련의 API 또는 _도구_와 상호작용하여 기능을 확장할 수 있도록 합니다.

도구는 주로 다음과 같은 용도로 사용됩니다:

* ***정보 검색 (Information Retrieval)***  
  이 범주의 도구는 외부 소스(예: 데이터베이스, 웹 서비스, 파일 시스템, 웹 검색 엔진 등)로부터 정보를 검색하는 데 사용됩니다. 목적은 모델의 지식을 보완하여, 그렇지 않으면 답할 수 없는 질문에 응답할 수 있게 하는 것입니다. 이러한 도구는 Retrieval Augmented Generation(RAG) 시나리오에서 활용될 수 있습니다. 예를 들어, 특정 위치의 현재 날씨를 가져오거나, 최신 뉴스 기사를 검색하거나, 특정 레코드를 조회하는 쿼리를 데이터베이스에 실행하는 데 사용할 수 있습니다.

* ***행동 수행 (Taking Action)***  
  이 범주의 도구는 소프트웨어 시스템 내에서 어떤 작업을 수행하는 데 사용됩니다. 예를 들어 이메일을 보내거나, 데이터베이스에 새로운 레코드를 생성하거나, 폼을 제출하거나, 워크플로를 트리거하는 등의 작업이 해당됩니다. 목적은 사람이 직접 개입하거나 명시적인 프로그래밍이 필요한 작업을 자동화하는 것입니다. 예를 들어, 챗봇과 상호작용 중인 고객의 항공편을 예약하거나, 웹 페이지에서 폼을 자동으로 작성하거나, 자동화된 테스트(TDD)에 기반하여 Java 클래스를 생성하는 코드 생성 시나리오에 도구를 사용할 수 있습니다.

_tool calling_ 은 일반적으로 모델의 기능으로 언급되지만, 실제로는 클라이언트 애플리케이션이 도구 호출 로직을 제공해야 합니다. 모델은 도구 호출을 요청하고 입력 인자를 제공할 수는 있지만, 입력 인자를 바탕으로 도구를 실제로 실행하고 결과를 반환하는 책임은 애플리케이션에 있습니다. 모델은 도구로 제공되는 어떤 API에도 직접 접근할 수 없으며, 이는 보안상 매우 중요한 고려 사항입니다.

Spring AI는 도구를 정의하고, 모델의 도구 호출 요청을 처리하며, 도구 호출을 실행할 수 있도록 편리한 API를 제공합니다. 다음 섹션에서는 Spring AI의 도구 호출 기능에 대한 개요를 설명합니다.

**📌 참고**
어떤 AI 모델이 도구 호출(tool calling)을 지원하는지 확인하려면 [Chat Model Comparisons](api/chat/comparison.adoc)를 참고하세요.

**💡 팁**
더 이상 사용되지 않는 [FunctionCallback에서 ToolCallback API로 마이그레이션](api/tools-migration.adoc)하는 가이드를 따라 진행하세요.

## 빠른 시작 (Quick Start)

이제 Spring AI에서 도구 호출을 사용하는 방법을 알아보겠습니다. 정보 검색용 도구 하나와 행동 수행용 도구 하나, 이렇게 두 가지 간단한 도구를 구현할 것입니다.  
정보 검색 도구는 사용자의 시간대에 맞는 현재 날짜와 시간을 가져오는 데 사용됩니다.  
행동 수행 도구는 지정된 시간에 알람을 설정하는 데 사용됩니다.

### 정보 검색 (Information Retrieval)

AI 모델은 실시간 정보에 접근할 수 없습니다. 현재 날짜나 날씨 예보 등과 같은 실시간 정보를 전제로 한 질문에는 모델이 스스로 응답할 수 없습니다.  
하지만 우리는 이러한 정보를 검색하는 도구를 제공하고, 모델이 실시간 정보가 필요할 때 이 도구를 호출할 수 있도록 할 수 있습니다.

사용자의 시간대에 맞춰 현재 날짜와 시간을 가져오는 도구를 `DateTimeTools` 클래스에서 구현해보겠습니다.  
이 도구는 인자를 받지 않습니다.  
Spring Framework의 `LocaleContextHolder`를 사용하면 사용자의 시간대를 가져올 수 있습니다.  
도구는 `@Tool` 애노테이션이 지정된 메서드로 정의됩니다.  
모델이 언제, 왜 이 도구를 호출해야 하는지 이해할 수 있도록, 도구가 수행하는 작업에 대한 **상세한 설명**도 함께 제공합니다.

```java
import java.time.LocalDateTime;
import org.springframework.ai.tool.annotation.Tool;
import org.springframework.context.i18n.LocaleContextHolder;

class DateTimeTools {

    @Tool(description = "Get the current date and time in the user's timezone")
    String getCurrentDateTime() {
        return LocalDateTime.now().atZone(LocaleContextHolder.getTimeZone().toZoneId()).toString();
    }

}
```

이제 모델이 이 도구를 사용할 수 있도록 만들어 보겠습니다.  
이번 예제에서는 `ChatClient`를 사용하여 모델과 상호작용할 것입니다.  
`tools()` 메서드를 통해 `DateTimeTools` 인스턴스를 전달하여 도구를 모델에 제공하게 됩니다.  
모델이 현재 날짜와 시간이 필요할 때, 해당 도구를 호출해달라고 요청하게 됩니다.

내부적으로는 `ChatClient`가 도구를 호출하고, 그 결과를 모델에 반환합니다.  
모델은 도구 호출 결과를 사용하여 원래 질문에 대한 최종 응답을 생성하게 됩니다.

```java
ChatModel chatModel = ...

String response = ChatClient.create(chatModel)
        .prompt("What day is tomorrow?")
        .tools(new DateTimeTools())
        .call()
        .content();

System.out.println(response);
```

출력 결과는 다음과 비슷하게 나타납니다:

```
Tomorrow is 2015-10-21.
```

같은 질문을 다시 시도해볼 수 있습니다.  이번에는 모델에 도구를 제공하지 마세요.  

출력 결과는 다음과 비슷하게 나타날 것입니다:

```
I am an AI and do not have access to real-time information. Please provide the current date so I can accurately determine what day tomorrow will be.
```

도구가 없으면 모델은 현재 날짜와 시간을 판단할 수 있는 능력이 없기 때문에 질문에 어떻게 답해야 할지 모릅니다.

### 행동 수행 (Taking Actions)

AI 모델은 특정 목표를 달성하기 위한 계획을 생성하는 데 사용할 수 있습니다.  
예를 들어, 모델은 덴마크 여행 예약 계획을 생성할 수 있습니다.  
하지만 모델은 그 계획을 실제로 **수행**할 수 있는 능력은 없습니다.  
이럴 때 **도구**가 필요한데, 도구는 모델이 생성한 계획을 실행하는 데 사용될 수 있습니다.

앞선 예제에서는 현재 날짜와 시간을 확인하는 도구를 사용했습니다.  
이번 예제에서는 특정 시간에 알람을 설정하는 두 번째 도구를 정의하겠습니다.  
목표는 **현재 시간으로부터 10분 뒤에 알람을 설정**하는 것이므로, 이 작업을 수행하려면 두 도구 모두 모델에 제공해야 합니다.

새로운 도구는 이전과 동일한 `DateTimeTools` 클래스에 추가할 것입니다.  
이 도구는 **ISO-8601 형식의 시간**을 단일 매개변수로 받습니다.  
그 후 해당 시간에 알람이 설정되었음을 나타내는 메시지를 콘솔에 출력합니다.  
이전과 마찬가지로 도구는 `@Tool` 애노테이션이 지정된 메서드로 정의되며,  
모델이 이 도구를 **언제**, **어떻게** 사용할지 이해할 수 있도록 **상세한 설명**도 함께 제공합니다.


```java
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import org.springframework.ai.tool.annotation.Tool;
import org.springframework.context.i18n.LocaleContextHolder;

class DateTimeTools {

    @Tool(description = "Get the current date and time in the user's timezone")
    String getCurrentDateTime() {
        return LocalDateTime.now().atZone(LocaleContextHolder.getTimeZone().toZoneId()).toString();
    }

    @Tool(description = "Set a user alarm for the given time, provided in ISO-8601 format")
    void setAlarm(String time) {
        LocalDateTime alarmTime = LocalDateTime.parse(time, DateTimeFormatter.ISO_DATE_TIME);
        System.out.println("Alarm set for " + alarmTime);
    }

}
```

이제 두 가지 도구를 모두 모델에서 사용할 수 있도록 해보겠습니다.  
`ChatClient`를 사용하여 모델과 상호작용할 것입니다.  
`tools()` 메서드를 통해 `DateTimeTools` 인스턴스를 전달하여 도구들을 모델에 제공합니다.

“지금으로부터 10분 뒤에 알람을 설정해 주세요”라고 요청하면,  
모델은 먼저 현재 날짜와 시간을 알아야 합니다.  
그다음 현재 시간을 기반으로 알람 시간을 계산하고,  
마지막으로 알람 도구를 사용하여 알람을 설정하게 됩니다.

내부적으로 `ChatClient`는 모델이 요청한 도구 호출을 처리하고,  
도구 실행 결과를 모델에 다시 전달합니다.  
이 결과를 바탕으로 모델은 최종 응답을 생성하게 됩니다.


```java
ChatModel chatModel = ...

String response = ChatClient.create(chatModel)
        .prompt("Can you set an alarm 10 minutes from now?")
        .tools(new DateTimeTools())
        .call()
        .content();

System.out.println(response);
```

애플리케이션 로그를 통해 알람이 올바른 시간에 설정되었는지 확인할 수 있습니다.

## 개요 (Overview)

Spring AI는 도구 호출(tool calling)을 위한 유연한 추상화 계층을 제공하여, 도구를 정의하고, 해석하고, 실행하는 작업을 일관된 방식으로 수행할 수 있도록 지원합니다.  
이 섹션에서는 Spring AI의 도구 호출 기능과 관련된 주요 개념 및 구성 요소에 대해 개요를 제공합니다.

![image.png](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250520181500_image.png)

1. 모델이 도구를 사용할 수 있도록 하려면, 채팅 요청(chat request)에 해당 도구의 정의를 포함시킵니다. 각 도구 정의는 이름(name), 설명(description), 입력 매개변수의 스키마(schema)로 구성됩니다.
2. 모델이 도구를 호출하기로 결정하면, 정의된 스키마를 기반으로 도구 이름과 입력 매개변수가 포함된 응답을 전송합니다.
3. 애플리케이션은 도구 이름을 이용해 적절한 도구를 식별하고, 제공된 입력 매개변수를 사용하여 실행할 책임이 있습니다.
4. 도구 호출 결과는 애플리케이션에서 처리됩니다.
5. 애플리케이션은 도구 호출 결과를 모델에 다시 전달합니다.
6. 모델은 도구 호출 결과를 추가적인 문맥으로 사용하여 최종 응답을 생성합니다.

도구(tools)는 도구 호출 기능의 핵심 요소이며, `ToolCallback` 인터페이스를 통해 모델링됩니다.  
Spring AI는 메서드 및 함수에서 `ToolCallback`을 지정할 수 있는 내장 기능을 제공하지만,  
더 많은 사용 사례를 지원하기 위해 사용자가 직접 `ToolCallback` 구현체를 정의할 수도 있습니다.

`ChatModel` 구현체는 도구 호출 요청을 해당하는 `ToolCallback` 구현체에 자동으로 전달하고,  
도구 호출 결과를 다시 모델에 전달하여 최종 응답 생성을 돕습니다.  
이 작업은 도구 실행 생명 주기를 관리하는 `ToolCallingManager` 인터페이스를 통해 수행됩니다.

`ChatClient`와 `ChatModel` 모두 모델이 사용할 수 있도록 도구를 제공하기 위해  
`ToolCallback` 객체의 목록과 도구를 실행할 `ToolCallingManager`를 인자로 받을 수 있습니다.

`ToolCallback` 객체들을 직접 전달하는 것 외에도,  
도구 이름 목록을 전달하여 `ToolCallbackResolver` 인터페이스를 통해 동적으로 도구를 해석할 수도 있습니다.

다음 섹션에서는 이러한 개념과 API들에 대해 더 자세히 설명하고,  
다양한 사용 사례를 지원하기 위한 사용자 정의 및 확장 방법도 함께 소개합니다.

## 메서드를 도구로 사용하기 (Methods as Tools)

Spring AI는 메서드를 통해 도구(`ToolCallback`)를 지정할 수 있는 두 가지 기본 방식을 제공합니다:

* 선언적으로 `@Tool` 애노테이션을 사용하는 방법
* 프로그래밍적으로 `MethodToolCallback`이라는 저수준 구현체를 사용하는 방법


### Declarative Specification: `@Tool`

메서드에 `@Tool` 애노테이션을 추가하면 해당 메서드를 도구로 변환할 수 있습니다.


```java
class DateTimeTools {

    @Tool(description = "Get the current date and time in the user's timezone")
    String getCurrentDateTime() {
        return LocalDateTime.now().atZone(LocaleContextHolder.getTimeZone().toZoneId()).toString();
    }

}
```

`@Tool` 애노테이션을 사용하면 도구에 대한 핵심 정보를 제공할 수 있습니다:

* `name`: 도구의 이름입니다. 지정하지 않으면 메서드 이름이 사용됩니다.  
  AI 모델은 이 이름을 사용하여 도구를 호출하므로, 동일한 클래스 내에 같은 이름의 도구가 두 개 이상 존재해서는 안 됩니다.  
  특정 채팅 요청에서 모델이 접근할 수 있는 모든 도구들 간에 이름은 고유해야 합니다.

* `description`: 도구에 대한 설명으로, 모델이 도구를 언제, 어떻게 호출해야 하는지를 이해하는 데 사용됩니다.  
  지정하지 않으면 메서드 이름이 설명으로 사용됩니다.  
  하지만 **모델이 도구의 목적과 사용법을 이해하는 데 있어 설명은 매우 중요**하므로,  
  가능한 한 상세한 설명을 제공하는 것이 강력히 권장됩니다.  
  설명이 충분하지 않으면 모델이 도구를 **필요할 때 사용하지 않거나**, **잘못 사용하는** 문제가 발생할 수 있습니다.

* `returnDirect`: 도구 실행 결과를 클라이언트에 직접 반환할지, 아니면 모델에 다시 전달할지를 지정합니다.  
  자세한 내용은 [Return Direct](#return-direct) 항목을 참조하세요.

* `resultConverter`: 도구 호출 결과를 `String 객체`로 변환하여 AI 모델에 다시 전달하기 위한  
  `ToolCallResultConverter` 구현체입니다.  
  자세한 내용은 [Result Conversion](#result-conversion) 항목을 참조하세요.

메서드는 `static`이거나 인스턴스 메서드일 수 있으며, 접근 제어자도 `public`, `protected`, `package-private`, `private` 모두 가능합니다.  
이 메서드를 포함하는 클래스는 최상위 클래스이거나 중첩 클래스일 수 있으며,  
인스턴스화가 가능한 범위 내에서 접근 가능하면 어떤 접근 수준도 괜찮습니다.

**📌 참고**  
Spring AI는 해당 메서드를 포함하는 클래스가 Spring Bean(`@Component` 등)일 경우,  
`@Tool` 애노테이션이 적용된 메서드에 대해 AOT 컴파일을 기본적으로 지원합니다.  
그렇지 않은 경우, GraalVM 컴파일러에 필요한 설정을 별도로 제공해야 합니다.  
예를 들어, 클래스에 `@RegisterReflection(memberCategories = MemberCategory.INVOKE_DECLARED_METHODS)`를 지정하는 방식이 있습니다.

해당 메서드는 대부분의 타입(기본형, POJO, enum, List, 배열, Map 등)을 포함하여  
**임의 개수의 인자**(또는 인자가 없는 경우도 가능)를 가질 수 있습니다.  
반환값 또한 대부분의 타입이 가능하며 `void`도 허용됩니다.  
단, 메서드가 값을 반환하는 경우, 해당 반환 타입은 **직렬화 가능한 타입**이어야 하며,  
모델로 다시 전송되기 위해 직렬화 과정이 수행됩니다.

**📌 참고**  
일부 타입은 지원되지 않습니다. 자세한 내용은 [Method Tool Limitations](#method-tool-limitations)를 참고하세요.

Spring AI는 `@Tool` 애노테이션이 적용된 메서드의 입력 매개변수에 대해  
**자동으로 JSON 스키마**를 생성합니다.  
이 스키마는 모델이 도구를 어떻게 호출하고 요청을 구성해야 하는지를 이해하는 데 사용됩니다.

또한 `@ToolParam` 애노테이션을 사용하면,  
입력 매개변수에 대한 설명이나 필수 여부와 같은 추가 정보를 지정할 수 있습니다.  
기본적으로 **모든 입력 매개변수는 필수(required)** 로 간주됩니다.


```java
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import org.springframework.ai.tool.annotation.Tool;
import org.springframework.ai.tool.annotation.ToolParam;

class DateTimeTools {

    @Tool(description = "Set a user alarm for the given time")
    void setAlarm(@ToolParam(description = "Time in ISO-8601 format") String time) {
        LocalDateTime alarmTime = LocalDateTime.parse(time, DateTimeFormatter.ISO_DATE_TIME);
        System.out.println("Alarm set for " + alarmTime);
    }

}
```

`@ToolParam` 애노테이션을 사용하면 도구 매개변수에 대한 핵심 정보를 제공할 수 있습니다:

* `description`: 매개변수에 대한 설명으로, 모델이 해당 매개변수를 어떻게 사용해야 하는지 더 잘 이해하는 데 도움을 줍니다.  
  예를 들어, 매개변수가 어떤 형식이어야 하는지, 어떤 값들이 허용되는지 등을 명시할 수 있습니다.

* `required`: 해당 매개변수가 필수인지 선택사항인지 여부를 지정합니다.  
  기본적으로는 **모든 매개변수는 필수(required)**로 간주됩니다.

만약 매개변수가 `@Nullable`로 애노테이션되어 있다면,  
`@ToolParam`에서 명시적으로 `required = true`로 설정하지 않는 한 선택 사항으로 간주됩니다.

`@ToolParam` 애노테이션 외에도, Swagger의 `@Schema` 애노테이션이나 Jackson의 `@JsonProperty` 애노테이션을 사용할 수도 있습니다.  
자세한 내용은 [JSON Schema](#json-schema) 항목을 참고하세요.

#### `ChatClient`에 도구 추가하기

선언적 방식(declarative specification approach)을 사용할 경우,  
`ChatClient`를 호출할 때 `tools()` 메서드에 도구 클래스 인스턴스를 전달할 수 있습니다.  
이 방식으로 전달된 도구들은 해당 채팅 요청에만 유효하게 적용됩니다.


```java
ChatClient.create(chatModel)
    .prompt("What day is tomorrow?")
    .tools(new DateTimeTools())
    .call()
    .content();
```

내부적으로 `ChatClient`는 도구 클래스 인스턴스 내의 각 `@Tool` 애노테이션이 지정된 메서드로부터  
`ToolCallback`을 생성하고 이를 모델에 전달합니다.  

직접 `ToolCallback`을 생성하고 싶다면, `ToolCallbacks` 유틸리티 클래스를 사용할 수 있습니다.


```java
ToolCallback[] dateTimeTools = ToolCallbacks.from(new DateTimeTools());
```

#### 기본 도구를 `ChatClient`에 추가하기

선언적 방식(declarative specification approach)을 사용할 경우,  
도구 클래스 인스턴스를 `defaultTools()` 메서드에 전달하여  
`ChatClient.Builder`에 기본 도구(default tools)를 추가할 수 있습니다.  

기본 도구와 런타임 도구가 모두 제공되는 경우,  
**런타임 도구가 기본 도구를 완전히 덮어씌우게 됩니다.**

**⚠️ 경고**  
기본 도구는 동일한 `ChatClient.Builder`로 생성된 **모든 `ChatClient` 인스턴스에서 수행되는 모든 채팅 요청에 공유**됩니다.  
여러 채팅 요청에서 공통으로 사용되는 도구에는 유용하지만,  
**주의해서 사용하지 않으면 원치 않는 상황에서 도구가 노출될 수 있는 위험**이 있습니다.


```java
ChatModel chatModel = ...
ChatClient chatClient = ChatClient.builder(chatModel)
    .defaultTools(new DateTimeTools())
    .build();
```

#### `ChatModel`에 도구 추가하기

선언적 방식(declarative specification approach)을 사용할 경우,  
`ChatModel`을 호출할 때 사용하는 `ToolCallingChatOptions`의 `toolCallbacks()` 메서드에  
도구 클래스 인스턴스를 전달하여 도구를 추가할 수 있습니다.  

이 방식으로 추가된 도구들은 **해당 채팅 요청에만 유효**합니다.

```java
ChatModel chatModel = ...
ToolCallback[] dateTimeTools = ToolCallbacks.from(new DateTimeTools());
ChatOptions chatOptions = ToolCallingChatOptions.builder()
    .toolCallbacks(dateTimeTools)
    .build();
Prompt prompt = new Prompt("What day is tomorrow?", chatOptions);
chatModel.call(prompt);
```

#### `ChatModel`에 기본 도구 추가하기

선언적 방식(declarative specification approach)을 사용할 경우,  
`ChatModel`을 생성할 때 사용된 `ToolCallingChatOptions` 인스턴스의 `toolCallbacks()` 메서드에  
도구 클래스 인스턴스를 전달하여 기본 도구를 추가할 수 있습니다.  

기본 도구와 런타임 도구가 모두 제공되면,  
**런타임 도구가 기본 도구를 완전히 덮어씌웁니다.**

**⚠️ 경고**  
기본 도구는 해당 `ChatModel` 인스턴스에서 수행되는 **모든 채팅 요청에 공유**됩니다.  
여러 채팅 요청에서 공통으로 사용되는 도구에는 유용하지만,  
**주의해서 사용하지 않으면 원치 않는 요청에서 도구가 노출될 수 있는 위험**이 있습니다.


```java
ToolCallback[] dateTimeTools = ToolCallbacks.from(new DateTimeTools());
ChatModel chatModel = OllamaChatModel.builder()
    .ollamaApi(OllamaApi.builder().build())
    .defaultOptions(ToolCallingChatOptions.builder()
            .toolCallbacks(dateTimeTools)
            .build())
    .build();
```

### 프로그래밍 방식 지정: `MethodToolCallback`

메서드를 도구로 변환하려면 `MethodToolCallback`을 프로그래밍 방식으로 생성하면 됩니다.


```java
class DateTimeTools {

    String getCurrentDateTime() {
        return LocalDateTime.now().atZone(LocaleContextHolder.getTimeZone().toZoneId()).toString();
    }

}
```

`MethodToolCallback.Builder`를 사용하면 `MethodToolCallback` 인스턴스를 생성하면서  
도구에 대한 핵심 정보를 지정할 수 있습니다:

* `toolDefinition`: 도구의 이름, 설명, 입력 스키마를 정의하는 `ToolDefinition` 인스턴스입니다.  
  `ToolDefinition.Builder` 클래스를 사용하여 생성할 수 있습니다. **필수 항목입니다.**

* `toolMetadata`: 도구의 결과를 클라이언트에 직접 반환할지 여부, 사용할 결과 변환기 등  
  추가 설정을 정의하는 `ToolMetadata` 인스턴스입니다.  
  `ToolMetadata.Builder` 클래스를 사용하여 생성할 수 있습니다.

* `toolMethod`: 도구로 사용할 메서드를 나타내는 `Method` 인스턴스입니다. **필수 항목입니다.**

* `toolObject`: 도구 메서드를 포함하는 객체 인스턴스입니다.  
  메서드가 `static`이라면 이 파라미터는 생략 가능합니다.

* `toolCallResultConverter`: 도구 호출 결과를 `String` 객체로 변환하여  
  AI 모델에 다시 전달하기 위한 `ToolCallResultConverter` 인스턴스입니다.  
  생략하면 기본 변환기(`DefaultToolCallResultConverter`)가 사용됩니다.

---

`ToolDefinition.Builder`는 `ToolDefinition` 인스턴스를 생성하고,  
도구의 이름, 설명, 입력 스키마를 정의할 수 있도록 합니다:

* `name`: 도구의 이름입니다. 지정하지 않으면 메서드 이름이 사용됩니다.  
  AI 모델은 이 이름을 통해 도구를 식별하므로, 동일한 클래스 내에 같은 이름을 가진 도구가 존재해서는 안 됩니다.  
  특정 채팅 요청에서 모델이 사용할 수 있는 모든 도구 중에서도 이름은 고유해야 합니다.

* `description`: 도구에 대한 설명으로, 모델이 도구를 언제, 어떻게 호출해야 하는지를 이해하는 데 도움을 줍니다.  
  지정하지 않으면 메서드 이름이 설명으로 사용됩니다.  
  하지만 **도구의 목적과 사용법을 모델이 명확히 이해하도록 하기 위해서는 상세한 설명을 제공하는 것이 매우 중요**합니다.  
  설명이 부족하면 모델이 **도구를 사용하지 않거나**, **잘못 사용할** 수 있습니다.

* `inputSchema`: 도구의 입력 매개변수에 대한 JSON 스키마입니다.  
  지정하지 않으면 메서드의 매개변수를 기반으로 자동 생성됩니다.  
  `@ToolParam` 애노테이션을 사용하면 입력 매개변수에 대한 설명, 필수 여부 등의 추가 정보를 제공할 수 있습니다.  
  기본적으로 모든 매개변수는 필수로 간주됩니다. 자세한 내용은 [JSON Schema](#json-schema)를 참고하세요.

---

`ToolMetadata.Builder`는 `ToolMetadata` 인스턴스를 생성하고  
도구에 대한 추가 설정을 정의할 수 있도록 합니다:

* `returnDirect`: 도구 실행 결과를 클라이언트에 직접 반환할지,  
  아니면 모델에 다시 전달할지를 지정합니다.  
  자세한 내용은 [Return Direct](#return-direct) 항목을 참고하세요.


```java
Method method = ReflectionUtils.findMethod(DateTimeTools.class, "getCurrentDateTime");
ToolCallback toolCallback = MethodToolCallback.builder()
    .toolDefinition(ToolDefinition.builder(method)
            .description("Get the current date and time in the user's timezone")
            .build())
    .toolMethod(method)
    .toolObject(new DateTimeTools())
    .build();
```

메서드는 `static`일 수도 있고 인스턴스 메서드일 수도 있으며,  
접근 제어자도 `public`, `protected`, `package-private`, `private` 모두 가능합니다.  
해당 메서드를 포함하는 클래스는 최상위 클래스이거나 중첩 클래스일 수 있으며,  
인스턴스를 생성하려는 위치에서 접근 가능하기만 하면 접근 수준은 제한이 없습니다.

**📌 참고**  
Spring AI는 해당 메서드를 포함하는 클래스가 Spring Bean(`@Component` 등)일 경우,  
도구 메서드에 대해 AOT 컴파일을 기본으로 지원합니다.  
그렇지 않은 경우에는 GraalVM 컴파일러에 필요한 설정을 별도로 제공해야 합니다.  
예: `@RegisterReflection(memberCategories = MemberCategory.INVOKE_DECLARED_METHODS)` 애노테이션을 클래스에 지정

해당 메서드는 **인자 없이 정의할 수도 있고**,  
기본형(primitives), POJO, enum, 리스트, 배열, 맵 등 **대부분의 타입을 인자로 사용할 수 있습니다**.  
반환 타입도 `void`를 포함해 대부분의 타입을 지원합니다.  
단, 값을 반환하는 경우에는 해당 반환 타입이 **직렬화 가능한 타입**이어야 하며,  
결과는 직렬화되어 모델로 다시 전송됩니다.

**📌 참고**  
일부 타입은 지원되지 않습니다.  
자세한 내용은 [Method Tool Limitations](#method-tool-limitations)를 참고하세요.

메서드가 `static`인 경우, `toolObject()` 메서드는 생략할 수 있습니다.  
(정적 메서드는 객체 인스턴스가 필요하지 않기 때문입니다.)


```java
class DateTimeTools {

    static String getCurrentDateTime() {
        return LocalDateTime.now().atZone(LocaleContextHolder.getTimeZone().toZoneId()).toString();
    }

}
```

```java
Method method = ReflectionUtils.findMethod(DateTimeTools.class, "getCurrentDateTime");
ToolCallback toolCallback = MethodToolCallback.builder()
    .toolDefinition(ToolDefinition.builder(method)
            .description("Get the current date and time in the user's timezone")
            .build())
    .toolMethod(method)
    .build();
```

Spring AI는 메서드의 입력 매개변수에 대해 **JSON 스키마를 자동으로 생성**합니다.  
이 스키마는 모델이 도구를 어떻게 호출하고 요청을 어떻게 구성할지를 이해하는 데 사용됩니다.  

입력 매개변수에 대한 설명, 필수 여부 등의 추가 정보를 제공하려면  
`@ToolParam` 애노테이션을 사용할 수 있습니다.  
기본적으로 **모든 입력 매개변수는 필수(required)**로 간주됩니다.


```java
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import org.springframework.ai.tool.annotation.ToolParam;

class DateTimeTools {

    void setAlarm(@ToolParam(description = "Time in ISO-8601 format") String time) {
        LocalDateTime alarmTime = LocalDateTime.parse(time, DateTimeFormatter.ISO_DATE_TIME);
        System.out.println("Alarm set for " + alarmTime);
    }

}
```

`@ToolParam` 애노테이션을 사용하면 도구 매개변수에 대한 핵심 정보를 제공할 수 있습니다:

* `description`: 매개변수에 대한 설명으로, 모델이 해당 매개변수를 어떻게 사용하는지 더 잘 이해하는 데 도움이 됩니다.  
  예를 들어, 매개변수가 어떤 형식이어야 하는지, 어떤 값들이 허용되는지 등을 명시할 수 있습니다.

* `required`: 해당 매개변수가 필수인지, 선택 사항인지 여부를 지정합니다.  
  기본적으로 **모든 매개변수는 필수(required)**로 간주됩니다.

만약 매개변수가 `@Nullable`로 애노테이션되어 있다면,  
`@ToolParam` 애노테이션에서 명시적으로 `required = true`로 설정하지 않는 한  
선택 사항으로 간주됩니다.

`@ToolParam` 애노테이션 외에도 Swagger의 `@Schema` 애노테이션이나  
Jackson의 `@JsonProperty` 애노테이션을 사용할 수도 있습니다.  
자세한 내용은 [JSON Schema](#json-schema) 항목을 참고하세요.

#### `ChatClient` 및 `ChatModel`에 도구 추가하기

프로그래밍 방식의 지정 방법(programmatic specification approach)을 사용할 경우,  
`MethodToolCallback` 인스턴스를 `ChatClient`의 `tools()` 메서드에 전달하여 도구를 추가할 수 있습니다.  
이 도구는 **해당 채팅 요청에만 유효**합니다.


```java
ToolCallback toolCallback = ...
ChatClient.create(chatModel)
    .prompt("What day is tomorrow?")
    .tools(toolCallback)
    .call()
    .content();
```

#### `ChatClient`에 기본 도구 추가하기

프로그래밍 방식의 지정 방법(programmatic specification approach)을 사용할 경우,  
`MethodToolCallback` 인스턴스를 `defaultTools()` 메서드에 전달하여  
`ChatClient.Builder`에 기본 도구를 추가할 수 있습니다.  

기본 도구와 런타임 도구가 모두 제공되는 경우,  
**런타임 도구가 기본 도구를 완전히 덮어씁니다.**

**⚠️ 경고**  
기본 도구는 동일한 `ChatClient.Builder`로 생성된  
**모든 `ChatClient` 인스턴스에서 수행되는 모든 채팅 요청에 공유**됩니다.  
여러 채팅 요청에서 공통으로 사용되는 도구에는 유용하지만,  
**주의해서 사용하지 않으면 원치 않는 요청에서 도구가 노출될 수 있는 위험**이 있습니다.


```java
ChatModel chatModel = ...
ToolCallback toolCallback = ...
ChatClient chatClient = ChatClient.builder(chatModel)
    .defaultTools(toolCallback)
    .build();
```

#### `ChatModel`에 도구 추가하기

프로그래밍 방식의 지정 방법(programmatic specification approach)을 사용할 경우,  
`ChatModel`을 호출할 때 사용하는 `ToolCallingChatOptions`의 `toolCallbacks()` 메서드에  
`MethodToolCallback` 인스턴스를 전달하여 도구를 추가할 수 있습니다.  

이 도구는 **해당 채팅 요청에만 유효**합니다.


```java
ChatModel chatModel = ...
ToolCallback toolCallback = ...
ChatOptions chatOptions = ToolCallingChatOptions.builder()
    .toolCallbacks(toolCallback)
    .build():
Prompt prompt = new Prompt("What day is tomorrow?", chatOptions);
chatModel.call(prompt);
```

#### `ChatModel`에 기본 도구 추가하기

프로그래밍 방식의 지정 방법(programmatic specification approach)을 사용할 경우,  
`ChatModel`을 생성할 때 사용된 `ToolCallingChatOptions` 인스턴스의  
`toolCallbacks()` 메서드에 `MethodToolCallback` 인스턴스를 전달하여  
기본 도구를 추가할 수 있습니다.

기본 도구와 런타임 도구가 모두 제공되는 경우,  
**런타임 도구가 기본 도구를 완전히 덮어씁니다.**

**⚠️ 경고**  
기본 도구는 해당 `ChatModel` 인스턴스에서 수행되는  
**모든 채팅 요청에 공유**됩니다.  
여러 요청에서 공통적으로 사용되는 도구에는 유용하지만,  
**주의해서 사용하지 않으면 도구가 원치 않는 상황에서 노출될 수 있는 위험**이 있습니다.


```java
ToolCallback toolCallback = ...
ChatModel chatModel = OllamaChatModel.builder()
    .ollamaApi(OllamaApi.builder().build())
    .defaultOptions(ToolCallingChatOptions.builder()
            .toolCallbacks(toolCallback)
            .build())
    .build();
```

### 메서드 기반 도구의 제한 사항 (Method Tool Limitations)

다음과 같은 타입들은 도구로 사용되는 메서드의 매개변수나 반환 타입으로 현재 지원되지 않습니다:

* `Optional`
* 비동기 타입 (예: `CompletableFuture`, `Future`)
* 리액티브 타입 (예: `Flow`, `Mono`, `Flux`)
* 함수형 타입 (예: `Function`, `Supplier`, `Consumer`)

함수형 타입은 함수 기반 도구 지정 방식(function-based tool specification approach)을 통해 지원됩니다.  
자세한 내용은 [Functions as Tools](#functions-as-tools) 항목을 참고하세요.

---

## 함수를 도구로 사용하기 (Functions as Tools)

Spring AI는 함수에서 도구를 지정할 수 있도록  
저수준 `FunctionToolCallback` 구현체를 사용하는 **프로그래밍 방식**이나  
런타임에 `@Bean`으로 동적으로 등록하는 **동적 방식**을 기본 지원합니다.

### 프로그래밍 방식 지정: `FunctionToolCallback`

`Function`, `Supplier`, `Consumer`, 또는 `BiFunction` 과 같은 함수형 타입을  
`FunctionToolCallback`을 프로그래밍 방식으로 생성하여 도구로 변환할 수 있습니다.


```java
public class WeatherService implements Function<WeatherRequest, WeatherResponse> {
    public WeatherResponse apply(WeatherRequest request) {
        return new WeatherResponse(30.0, Unit.C);
    }
}

public enum Unit { C, F }
public record WeatherRequest(String location, Unit unit) {}
public record WeatherResponse(double temp, Unit unit) {}
```

`FunctionToolCallback.Builder`를 사용하면 `FunctionToolCallback` 인스턴스를 생성하면서  
도구에 대한 핵심 정보를 지정할 수 있습니다:

* `name`: 도구의 이름입니다. AI 모델은 이 이름을 사용하여 도구를 식별하고 호출합니다.  
  따라서 동일한 컨텍스트 내에서 **같은 이름의 도구가 두 개 이상 존재할 수 없습니다**.  
  특정 채팅 요청에서 모델이 사용할 수 있는 모든 도구들 간에 이름은 고유해야 합니다. **필수 항목입니다.**

* `toolFunction`: 도구 역할을 수행할 함수형 객체입니다 (`Function`, `Supplier`, `Consumer`, `BiFunction`). **필수 항목입니다.**

* `description`: 도구에 대한 설명으로, 모델이 도구를 **언제**, **어떻게** 호출해야 하는지 이해하는 데 사용됩니다.  
  지정하지 않으면 메서드 이름이 설명으로 사용됩니다.  
  그러나 **도구의 목적과 사용법을 모델이 명확히 이해하도록 하기 위해서는 상세한 설명을 제공하는 것이 매우 중요**합니다.  
  설명이 부족하면 모델이 **도구를 사용하지 않거나**, **잘못 사용할** 수 있습니다.

* `inputType`: 함수의 입력 타입입니다. **필수 항목입니다.**

* `inputSchema`: 도구의 입력 매개변수에 대한 JSON 스키마입니다.  
  지정하지 않으면 `inputType`을 기준으로 자동 생성됩니다.  
  `@ToolParam` 애노테이션을 사용하면 입력 매개변수에 대한 설명, 필수 여부 등의 추가 정보를 제공할 수 있습니다.  
  기본적으로 모든 매개변수는 **필수(required)**로 간주됩니다.  
  자세한 내용은 [JSON Schema](#json-schema) 항목을 참고하세요.

* `toolMetadata`: 도구의 실행 결과를 클라이언트에 직접 반환할지 여부,  
  사용할 결과 변환기 등 추가 설정을 정의하는 `ToolMetadata` 인스턴스입니다.  
  `ToolMetadata.Builder` 클래스를 사용하여 생성할 수 있습니다.

* `toolCallResultConverter`: 도구 호출 결과를 `String` 객체로 변환하여  
  AI 모델에 다시 전달하기 위한 `ToolCallResultConverter` 인스턴스입니다.  
  생략하면 기본 변환기(`DefaultToolCallResultConverter`)가 사용됩니다.

---

`ToolMetadata.Builder`를 사용하면 `ToolMetadata` 인스턴스를 생성하고  
도구에 대한 추가 설정을 정의할 수 있습니다:

* `returnDirect`: 도구 실행 결과를 클라이언트에 직접 반환할지,  
  아니면 모델에 다시 전달할지를 지정합니다.  
  자세한 내용은 [Return Direct](#return-direct) 항목을 참고하세요.


```java
ToolCallback toolCallback = FunctionToolCallback
    .builder("currentWeather", new WeatherService())
    .description("Get the weather in location")
    .inputType(WeatherRequest.class)
    .build();
```

함수의 입력값과 출력값은 `Void` 또는 POJO일 수 있습니다.  
입력 및 출력 POJO는 **직렬화 가능**해야 하며, 결과는 직렬화되어 모델로 다시 전송됩니다.  
또한 함수 자체와 입력/출력 타입은 모두 **`public` 접근 제어자**를 가져야 합니다.

**📌 참고**  
일부 타입은 지원되지 않습니다. 자세한 내용은 [Function Tool Limitations](#function-tool-limitations)를 참고하세요.

#### `ChatClient`에 도구 추가하기

프로그래밍 방식의 지정 방법(programmatic specification approach)을 사용할 경우,  
`FunctionToolCallback` 인스턴스를 `ChatClient`의 `tools()` 메서드에 전달하여 도구를 추가할 수 있습니다.  
이 도구는 **해당 채팅 요청에만 유효**합니다.


```java
ToolCallback toolCallback = ...
ChatClient.create(chatModel)
    .prompt("What's the weather like in Copenhagen?")
    .tools(toolCallback)
    .call()
    .content();
```

#### `ChatClient`에 기본 도구 추가하기

프로그래밍 방식의 지정 방법(programmatic specification approach)을 사용할 경우,  
`FunctionToolCallback` 인스턴스를 `defaultTools()` 메서드에 전달하여  
`ChatClient.Builder`에 기본 도구를 추가할 수 있습니다.

기본 도구와 런타임 도구가 모두 제공되는 경우,  
**런타임 도구가 기본 도구를 완전히 덮어씁니다.**

**⚠️ 경고**  
기본 도구는 동일한 `ChatClient.Builder`로 생성된  
**모든 `ChatClient` 인스턴스에서 수행되는 모든 채팅 요청에 공유**됩니다.  
여러 채팅 요청에서 공통으로 사용되는 도구에는 유용하지만,  
**주의해서 사용하지 않으면 도구가 원치 않는 상황에서 노출될 수 있는 위험**이 있습니다.


```java
ChatModel chatModel = ...
ToolCallback toolCallback = ...
ChatClient chatClient = ChatClient.builder(chatModel)
    .defaultTools(toolCallback)
    .build();
```

#### `ChatModel`에 도구 추가하기

프로그래밍 방식의 지정 방법(programmatic specification approach)을 사용할 경우,  
`FunctionToolCallback` 인스턴스를 `ToolCallingChatOptions`의 `toolCallbacks()` 메서드에 전달하여  
도구를 추가할 수 있습니다.  

이 도구는 **해당 채팅 요청에만 유효**합니다.


```java
ChatModel chatModel = ...
ToolCallback toolCallback = ...
ChatOptions chatOptions = ToolCallingChatOptions.builder()
    .toolCallbacks(toolCallback)
    .build():
Prompt prompt = new Prompt("What's the weather like in Copenhagen?", chatOptions);
chatModel.call(prompt);
```

#### `ChatModel`에 기본 도구 추가하기

프로그래밍 방식의 지정 방법(programmatic specification approach)을 사용할 경우,  
`ChatModel`을 생성할 때 사용된 `ToolCallingChatOptions` 인스턴스의  
`toolCallbacks()` 메서드에 `FunctionToolCallback` 인스턴스를 전달하여  
기본 도구를 추가할 수 있습니다.

기본 도구와 런타임 도구가 모두 제공되는 경우,  
**런타임 도구가 기본 도구를 완전히 덮어씁니다.**

**⚠️ 경고**  
기본 도구는 해당 `ChatModel` 인스턴스에서 수행되는  
**모든 채팅 요청에 공유**됩니다.  
여러 요청에서 공통으로 사용되는 도구에는 유용하지만,  
**주의하지 않으면 원치 않는 요청에서 도구가 노출될 수 있는 위험**이 있습니다.


```java
ToolCallback toolCallback = ...
ChatModel chatModel = OllamaChatModel.builder()
    .ollamaApi(OllamaApi.builder().build())
    .defaultOptions(ToolCallingChatOptions.builder()
            .toolCallbacks(toolCallback)
            .build())
    .build();
```

### 동적 지정 방식: `@Bean`

도구를 프로그래밍 방식으로 지정하는 대신,  
도구를 **Spring Bean으로 정의**하고  
Spring AI가 런타임에 `ToolCallbackResolver` 인터페이스  
(구현체: `SpringBeanToolCallbackResolver`)를 통해 **동적으로 해석하도록** 할 수 있습니다.

이 방식을 사용하면 `Function`, `Supplier`, `Consumer`, `BiFunction` 타입의 Bean을  
도구로 활용할 수 있게 됩니다.

Bean의 이름은 도구 이름으로 사용되며,  
도구에 대한 설명은 Spring Framework의 `@Description` 애노테이션을 통해 제공할 수 있습니다.  
이 설명은 모델이 도구를 **언제**, **어떻게** 호출할지를 이해하는 데 활용됩니다.  
설명을 제공하지 않으면 메서드 이름이 도구 설명으로 사용됩니다.

그러나 **모델이 도구의 목적과 사용법을 명확히 이해할 수 있도록,  
상세한 설명을 제공하는 것이 강력히 권장됩니다.**  
설명이 부족할 경우, 모델이 **도구를 적절한 시점에 사용하지 않거나**,  
**잘못 사용하는 문제**가 발생할 수 있습니다.


```java
@Configuration(proxyBeanMethods = false)
class WeatherTools {

    WeatherService weatherService = new WeatherService();

	@Bean
	@Description("Get the weather in location")
	Function<WeatherRequest, WeatherResponse> currentWeather() {
		return weatherService;
	}

}
```

**📌 참고**  
일부 타입은 지원되지 않습니다. 자세한 내용은 [Function Tool Limitations](#function-tool-limitations)를 참고하세요.

도구의 입력 매개변수에 대한 **JSON 스키마는 자동으로 생성**됩니다.  
입력 매개변수에 대한 설명이나 필수 여부 등의 추가 정보를 제공하려면  
`@ToolParam` 애노테이션을 사용할 수 있습니다.  
기본적으로 **모든 입력 매개변수는 필수(required)**로 간주됩니다.  
자세한 내용은 [JSON Schema](#json-schema)를 참고하세요.


```java
record WeatherRequest(@ToolParam(description = "The name of a city or a country") String location, Unit unit) {}
```

This tool specification approach has the drawback of not guaranteeing type safety, as the tool resolution is done at runtime. To mitigate this, you can specify the tool name explicitly using the `@Bean` annotation and storing the value in a constant, so that you can use it in a chat request instead of hard-coding the tool name.

```java
@Configuration(proxyBeanMethods = false)
class WeatherTools {

    public static final String CURRENT_WEATHER_TOOL = "currentWeather";

	@Bean(CURRENT_WEATHER_TOOL)
	@Description("Get the weather in location")
	Function<WeatherRequest, WeatherResponse> currentWeather() {
		...
	}

}
```

#### Adding Tools to `ChatClient`

When using the dynamic specification approach, you can pass the tool name (i.e. the function bean name) to the `tools()` method of `ChatClient`.
The tool will only be available for the specific chat request it’s added to.

```java
ChatClient.create(chatModel)
    .prompt("What's the weather like in Copenhagen?")
    .tools("currentWeather")
    .call()
    .content();
```

#### Adding Default Tools to `ChatClient`

When using the dynamic specification approach, you can add default tools to a `ChatClient.Builder` by passing the tool name to the `defaultTools()` method.
If both default and runtime tools are provided, the runtime tools will completely override the default tools.

**⚠️ WARNING**\
Default tools are shared across all the chat requests performed by all the `ChatClient` instances built from the same `ChatClient.Builder`. They are useful for tools that are commonly used across different chat requests, but they can also be dangerous if not used carefully, risking to make them available when they shouldn’t.

```java
ChatModel chatModel = ...
ChatClient chatClient = ChatClient.builder(chatModel)
    .defaultTools("currentWeather")
    .build();
```

#### Adding Tools to `ChatModel`

When using the dynamic specification approach, you can pass the tool name to the `toolNames()` method of the `ToolCallingChatOptions` you use to call the `ChatModel`. The tool will only be available for the specific chat request it’s added to.

```java
ChatModel chatModel = ...
ChatOptions chatOptions = ToolCallingChatOptions.builder()
    .toolNames("currentWeather")
    .build():
Prompt prompt = new Prompt("What's the weather like in Copenhagen?", chatOptions);
chatModel.call(prompt);
```

#### Adding Default Tools to `ChatModel`

When using the dynamic specification approach, you can add default tools to `ChatModel` at construction time by passing the tool name to the `toolNames()` method of the `ToolCallingChatOptions` instance used to create the `ChatModel`.
If both default and runtime tools are provided, the runtime tools will completely override the default tools.

**⚠️ WARNING**\
Default tools are shared across all the chat requests performed by that `ChatModel` instance. They are useful for tools that are commonly used across different chat requests, but they can also be dangerous if not used carefully, risking to make them available when they shouldn’t.

```java
ChatModel chatModel = OllamaChatModel.builder()
    .ollamaApi(OllamaApi.builder().build())
    .defaultOptions(ToolCallingChatOptions.builder()
            .toolNames("currentWeather")
            .build())
    .build();
```

### Function Tool Limitations

The following types are not currently supported as input or output types for functions used as tools:

* Primitive types
* `Optional`
* Collection types (e.g. `List`, `Map`, `Array`, `Set`) 
* Asynchronous types (e.g. `CompletableFuture`, `Future`)
* Reactive types (e.g. `Flow`, `Mono`, `Flux`).

Primitive types and collections are supported using the method-based tool specification approach. See [Methods as Tools](#methods-as-tools) for more details.

## Tool Specification

In Spring AI, tools are modeled via the `ToolCallback` interface. In the previous sections, we’ve seen how to define tools from methods and functions using the built-in support provided by Spring AI (see [Methods as Tools](#methods-as-tools) and [Functions as Tools](#functions-as-tools)). This section will dive deeper into the tool specification and how to customize and extend it to support more use cases.

### Tool Callback

The `ToolCallback` interface provides a way to define a tool that can be called by the AI model, including both definition and execution logic. It’s the main interface to implement when you want to define a tool from scratch. For example, you can define a `ToolCallback` from an MCP Client (using the Model Context Protocol) or a `ChatClient` (to build a modular agentic application).

The interface provides the following methods:

```java
public interface ToolCallback {

	/**
	 * Definition used by the AI model to determine when and how to call the tool.
	 */
	ToolDefinition getToolDefinition();

	/**
	 * Metadata providing additional information on how to handle the tool.
	 */
	ToolMetadata getToolMetadata();

    /**
	 * Execute tool with the given input and return the result to send back to the AI model.
	 */
	String call(String toolInput);

    /**
	 * Execute tool with the given input and context, and return the result to send back to the AI model.
	 */
	String call(String toolInput, ToolContext tooContext);

}
```

Spring AI provides built-in implementations for tool methods (`MethodToolCallback`) and tool functions (`FunctionToolCallback`).

### Tool Definition

The `ToolDefinition` interface provides the required information for the AI model to know about the availability of the tool, including the tool name, description, and input schema. Each `ToolCallback` implementation must provide a `ToolDefinition` instance to define the tool.

The interface provides the following methods:

```java
public interface ToolDefinition {

	/**
	 * The tool name. Unique within the tool set provided to a model.
	 */
	String name();

	/**
	 * The tool description, used by the AI model to determine what the tool does.
	 */
	String description();

	/**
	 * The schema of the parameters used to call the tool.
	 */
	String inputSchema();

}
```

**📌 NOTE**\
See [JSON Schema](#json-schema) for more details on the input schema.

The `ToolDefinition.Builder` lets you build a `ToolDefinition` instance using the default implementation (`DefaultToolDefinition`).

```java
ToolDefinition toolDefinition = ToolDefinition.builder()
    .name("currentWeather")
    .description("Get the weather in location")
    .inputSchema("""
        {
            "type": "object",
            "properties": {
                "location": {
                    "type": "string"
                },
                "unit": {
                    "type": "string",
                    "enum": ["C", "F"]
                }
            },
            "required": ["location", "unit"]
        }
    """)
    .build();
```

#### Method Tool Definition

When building tools from a method, the `ToolDefinition` is automatically generated for you. In case you prefer to generate the `ToolDefinition` yourself, you can use this convenient builder.

```java
Method method = ReflectionUtils.findMethod(DateTimeTools.class, "getCurrentDateTime");
ToolDefinition toolDefinition = ToolDefinition.from(method);
```

The `ToolDefinition` generated from a method includes the method name as the tool name, the method name as the tool description, and the JSON schema of the method input parameters. If the method is annotated with `@Tool`, the tool name and description will be taken from the annotation, if set.

**📌 NOTE**\
See [Methods as Tools](#methods-as-tools) for more details.

If you’d rather provide some or all of the attributes explicitly, you can use the `ToolDefinition.Builder` to build a custom `ToolDefinition` instance.

```java
Method method = ReflectionUtils.findMethod(DateTimeTools.class, "getCurrentDateTime");
ToolDefinition toolDefinition = ToolDefinition.builder(method)
    .name("currentDateTime")
    .description("Get the current date and time in the user's timezone")
    .inputSchema(JsonSchemaGenerator.generateForMethodInput(method))
    .build();
```

#### Function Tool Definition

When building tools from a function, the `ToolDefinition` is automatically generated for you. When you use the `FunctionToolCallback.Builder` to build a `FunctionToolCallback` instance, you can provide the tool name, description, and input schema that will be used to generate the `ToolDefinition`. See [Functions as Tools](#functions-as-tools) for more details.

### JSON Schema

When providing a tool to the AI model, the model needs to know the schema of the input type for calling the tool. The schema is used to understand how to call the tool and prepare the tool request. Spring AI provides built-in support for generating the JSON Schema of the input type for a tool via the `JsonSchemaGenerator` class. The schema is provided as part of the `ToolDefinition`.

**📌 NOTE**\
See [Tool Definition](#tool-definition) for more details on the `ToolDefinition` and how to pass the input schema to it.

The `JsonSchemaGenerator` class is used under the hood to generate the JSON schema for the input parameters of a method or a function, using any of the strategies described in [Methods as Tools](#methods-as-tools) and [Functions as Tools](#functions-as-tools). The JSON schema generation logic supports a series of annotations that you can use on the input parameters for methods and functions to customize the resulting schema.

This section describes two main options you can customize when generating the JSON schema for the input parameters of a tool: description and required status.

#### Description

Besides providing a description for the tool itself, you can also provide a description for the input parameters of a tool. The description can be used to provide key information about the input parameters, such as what format the parameter should be in, what values are allowed, and so on. This is useful to help the model understand the input schema and how to use it. Spring AI provides built-in support for generating the description for an input parameter using one of the following annotations:

* `@ToolParam(description = "...")` from Spring AI
* `@JsonClassDescription(description = "...")` from Jackson
* `@JsonPropertyDescription(description = "...")` from Jackson
* `@Schema(description = "...")` from Swagger.

This approach works for both methods and functions, and you can use it recursively for nested types.

```java
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import org.springframework.ai.tool.annotation.Tool;
import org.springframework.ai.tool.annotation.ToolParam;
import org.springframework.context.i18n.LocaleContextHolder;

class DateTimeTools {

    @Tool(description = "Set a user alarm for the given time")
    void setAlarm(@ToolParam(description = "Time in ISO-8601 format") String time) {
        LocalDateTime alarmTime = LocalDateTime.parse(time, DateTimeFormatter.ISO_DATE_TIME);
        System.out.println("Alarm set for " + alarmTime);
    }

}
```

#### Required/Optional

By default, each input parameter is considered required, which forces the AI model to provide a value for it when calling the tool. However, you can make an input parameter optional by using one of the following annotations, in this order of precedence:

* `@ToolParam(required = false)` from Spring AI
* `@JsonProperty(required = false)` from Jackson
* `@Schema(required = false)` from Swagger
* `@Nullable` from Spring Framework.

This approach works for both methods and functions, and you can use it recursively for nested types.

```java
class CustomerTools {

    @Tool(description = "Update customer information")
    void updateCustomerInfo(Long id, String name, @ToolParam(required = false) String email) {
        System.out.println("Updated info for customer with id: " + id);
    }

}
```

**⚠️ WARNING**\
Defining the correct required status for the input parameter is crucial to mitigate the risk of hallucinations and ensure the model provides the right input when calling the tool. In the previous example, the `email` parameter is optional, which means the model can call the tool without providing a value for it. If the parameter was required, the model would have to provide a value for it when calling the tool. And if no value existed, the model would probably make one up, leading to hallucinations.

### Result Conversion

The result of a tool call is serialized using a `ToolCallResultConverter` and then sent back to the AI model. The `ToolCallResultConverter` interface provides a way to convert the result of a tool call to a `String` object.

The interface provides the following method:

```java
@FunctionalInterface
public interface ToolCallResultConverter {

	/**
	 * Given an Object returned by a tool, convert it to a String compatible with the
	 * given class type.
	 */
	String convert(@Nullable Object result, @Nullable Type returnType);

}
```

The result must be a serializable type. By default, the result is serialized to JSON using Jackson (`DefaultToolCallResultConverter`), but you can customize the serialization process by providing your own `ToolCallResultConverter` implementation.

Spring AI relies on the `ToolCallResultConverter` in both method and function tools.

#### Method Tool Call Result Conversion

When building tools from a method with the declarative approach, you can provide a custom `ToolCallResultConverter` to use for the tool by setting the `resultConverter()` attribute of the `@Tool` annotation.

```java
class CustomerTools {

    @Tool(description = "Retrieve customer information", resultConverter = CustomToolCallResultConverter.class)
    Customer getCustomerInfo(Long id) {
        return customerRepository.findById(id);
    }

}
```

If using the programmatic approach, you can provide a custom `ToolCallResultConverter` to use for the tool by setting the `resultConverter()` attribute of the `MethodToolCallback.Builder`.

See [Methods as Tools](#methods-as-tools) for more details.

#### Function Tool Call Result Conversion

When building tools from a function using the programmatic approach, you can provide a custom `ToolCallResultConverter` to use for the tool by setting the `resultConverter()` attribute of the `FunctionToolCallback.Builder`.

See [Functions as Tools](#functions-as-tools) for more details.

### Tool Context

Spring AI supports passing additional contextual information to tools through the `ToolContext` API. This feature allows you to provide extra, user-provided data that can be used within the tool execution along with the tool arguments passed by the AI model. 

![image.png](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250520181323_image.png)


```java
class CustomerTools {

    @Tool(description = "Retrieve customer information")
    Customer getCustomerInfo(Long id, ToolContext toolContext) {
        return customerRepository.findById(id, toolContext.get("tenantId"));
    }

}
```

The `ToolContext` is populated with the data provided by the user when invoking `ChatClient`.

```java
ChatModel chatModel = ...

String response = ChatClient.create(chatModel)
        .prompt("Tell me more about the customer with ID 42")
        .tools(new CustomerTools())
        .toolContext(Map.of("tenantId", "acme"))
        .call()
        .content();

System.out.println(response);
```

**📌 NOTE**
None of the data provided in the `ToolContext` is sent to the AI model.

Similarly, you can define tool context data when invoking the `ChatModel` directly.

```java
ChatModel chatModel = ...
ToolCallback[] customerTools = ToolCallbacks.from(new CustomerTools());
ChatOptions chatOptions = ToolCallingChatOptions.builder()
    .toolCallbacks(customerTools)
    .toolContext(Map.of("tenantId", "acme"))
    .build();
Prompt prompt = new Prompt("Tell me more about the customer with ID 42", chatOptions);
chatModel.call(prompt);
```

If the `toolContext` option is set both in the default options and in the runtime options, the resulting `ToolContext` will be the merge of the two,
where the runtime options take precedence over the default options.

### Return Direct

By default, the result of a tool call is sent back to the model as a response. Then, the model can use the result to continue the conversation.

There are cases where you’d rather return the result directly to the caller instead of sending it back to the model. For example, if you build an agent that relies on a RAG tool, you might want to return the result directly to the caller instead of sending it back to the model for unnecessary post-processing. Or perhaps you have certain tools that should end the reasoning loop of the agent.

Each `ToolCallback` implementation can define whether the result of a tool call should be returned directly to the caller or sent back to the model. By default, the result is sent back to the model. But you can change this behavior per tool.

The `ToolCallingManager`, responsible for managing the tool execution lifecycle, is in charge of handling the `returnDirect` attribute associated with the tool. If the attribute is set to `true`, the result of the tool call is returned directly to the caller. Otherwise, the result is sent back to the model.

**📌 NOTE**\
If multiple tool calls are requested at once, the `returnDirect` attribute must be set to `true` for all the tools to return the results directly to the caller. Otherwise, the results will be sent back to the model.

![image.png](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250520181357_image.png)


1. When we want to make a tool available to the model, we include its definition in the chat request. If we want the result of the tool execution to be returned directly to the caller, we set the `returnDirect` attribute to `true`.
2. When the model decides to call a tool, it sends a response with the tool name and the input parameters modeled after the defined schema.
3. The application is responsible for using the tool name to identify and execute the tool with the provided input parameters.
4. The result of the tool call is processed by the application.
5. The application sends the tool call result directly to the caller, instead of sending it back to the model.

#### Method Return Direct

When building tools from a method with the declarative approach, you can mark a tool to return the result directly to the caller by setting the `returnDirect` attribute of the `@Tool` annotation to `true`.

```java
class CustomerTools {

    @Tool(description = "Retrieve customer information", returnDirect = true)
    Customer getCustomerInfo(Long id) {
        return customerRepository.findById(id);
    }

}
```

If using the programmatic approach, you can set the `returnDirect` attribute via the `ToolMetadata` interface and pass it to the `MethodToolCallback.Builder`.

```java
ToolMetadata toolMetadata = ToolMetadata.builder()
    .returnDirect(true)
    .build();
```

See [Methods as Tools](#methods-as-tools) for more details.

#### Function Return Direct

When building tools from a function with the programmatic approach, you can set the `returnDirect` attribute via the `ToolMetadata` interface and pass it to the `FunctionToolCallback.Builder`.

```java
ToolMetadata toolMetadata = ToolMetadata.builder()
    .returnDirect(true)
    .build();
```

See [Functions as Tools](#functions-as-tools) for more details.

## Tool Execution

The tool execution is the process of calling the tool with the provided input arguments and returning the result. The tool execution is handled by the `ToolCallingManager` interface, which is responsible for managing the tool execution lifecycle.

```java
public interface ToolCallingManager {

	/**
	 * Resolve the tool definitions from the model's tool calling options.
	 */
	List<ToolDefinition> resolveToolDefinitions(ToolCallingChatOptions chatOptions);

	/**
	 * Execute the tool calls requested by the model.
	 */
	ToolExecutionResult executeToolCalls(Prompt prompt, ChatResponse chatResponse);

}
```

If you’re using any of the Spring AI Spring Boot Starters, `DefaultToolCallingManager` is the autoconfigured implementation of the `ToolCallingManager` interface. You can customize the tool execution behavior by providing your own `ToolCallingManager` bean.

```java
@Bean
ToolCallingManager toolCallingManager() {
    return ToolCallingManager.builder().build();
}
```

By default, Spring AI manages the tool execution lifecycle transparently for you from within each `ChatModel` implementation. But you have the possibility to opt-out of this behavior and control the tool execution yourself. This section describes these two scenarios.

### Framework-Controlled Tool Execution

When using the default behavior, Spring AI will automatically intercept any tool call request from the model, call the tool and return the result to the model. All of this is done transparently for you by each `ChatModel` implementation using a `ToolCallingManager`.

![image.png](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250520181442_image.png)



1. When we want to make a tool available to the model, we include its definition in the chat request (`Prompt`) and invoke the `ChatModel` API which sends the request to the AI model.
2. When the model decides to call a tool, it sends a response (`ChatResponse`) with the tool name and the input parameters modeled after the defined schema.
3. The `ChatModel` sends the tool call request to the `ToolCallingManager` API.
4. The `ToolCallingManager` is responsible for identifying the tool to call and executing it with the provided input parameters.
5. The result of the tool call is returned to the `ToolCallingManager`.
6. The `ToolCallingManager` returns the tool execution result back to the `ChatModel`.
7. The `ChatModel` sends the tool execution result back to the AI model (`ToolResponseMessage`).
8. The AI model generates the final response using the tool call result as additional context and sends it back to the caller (`ChatResponse`) via the `ChatClient`.

**⚠️ WARNING**\
Currently, the internal messages exchanged with the model regarding the tool execution are not exposed to the user. If you need to access these messages, you should use the user-controlled tool execution approach.

The logic determining whether a tool call is eligible for execution is handled by the `ToolExecutionEligibilityPredicate` interface. By default, the tool execution eligibility is determined by checking if the `internalToolExecutionEnabled` attribute of `ToolCallingChatOptions` is set to `true` (the default value), and if the `ChatResponse` contains any tool calls.

```java
public class DefaultToolExecutionEligibilityPredicate implements ToolExecutionEligibilityPredicate {

	@Override
	public boolean test(ChatOptions promptOptions, ChatResponse chatResponse) {
		return ToolCallingChatOptions.isInternalToolExecutionEnabled(promptOptions) && chatResponse != null
				&& chatResponse.hasToolCalls();
	}

}
```

You can provide your custom implementation of `ToolExecutionEligibilityPredicate` when creating the `ChatModel` bean.

### User-Controlled Tool Execution

There are cases where you’d rather control the tool execution lifecycle yourself. You can do so by setting the `internalToolExecutionEnabled` attribute of `ToolCallingChatOptions` to `false`.

When you invoke a `ChatModel` with this option, the tool execution will be delegated to the caller, giving you full control over the tool execution lifecycle. It’s your responsibility checking for tool calls in the `ChatResponse` and executing them using the `ToolCallingManager`.

The following example demonstrates a minimal implementation of the user-controlled tool execution approach:

```java
ChatModel chatModel = ...
ToolCallingManager toolCallingManager = ToolCallingManager.builder().build();

ChatOptions chatOptions = ToolCallingChatOptions.builder()
    .toolCallbacks(new CustomerTools())
    .internalToolExecutionEnabled(false)
    .build();
Prompt prompt = new Prompt("Tell me more about the customer with ID 42", chatOptions);

ChatResponse chatResponse = chatModel.call(prompt);

while (chatResponse.hasToolCalls()) {
    ToolExecutionResult toolExecutionResult = toolCallingManager.executeToolCalls(prompt, chatResponse);

    prompt = new Prompt(toolExecutionResult.conversationHistory(), chatOptions);

    chatResponse = chatModel.call(prompt);
}

System.out.println(chatResponse.getResult().getOutput().getText());
```

**📌 NOTE**
When choosing the user-controlled tool execution approach, we recommend using a `ToolCallingManager` to manage the tool calling operations. This way, you can benefit from the built-in support provided by Spring AI for tool execution. However, nothing prevents you from implementing your own tool execution logic.

The next examples shows a minimal implementation of the user-controlled tool execution approach combined with the usage of the `ChatMemory` API:

```java
ToolCallingManager toolCallingManager = DefaultToolCallingManager.builder().build();
ChatMemory chatMemory = MessageWindowChatMemory.builder().build();
String conversationId = UUID.randomUUID().toString();

ChatOptions chatOptions = ToolCallingChatOptions.builder()
    .toolCallbacks(ToolCallbacks.from(new MathTools()))
    .internalToolExecutionEnabled(false)
    .build();
Prompt prompt = new Prompt(
        List.of(new SystemMessage("You are a helpful assistant."), new UserMessage("What is 6 * 8?")),
        chatOptions);
chatMemory.add(conversationId, prompt.getInstructions());

Prompt promptWithMemory = new Prompt(chatMemory.get(conversationId), chatOptions);
ChatResponse chatResponse = chatModel.call(promptWithMemory);
chatMemory.add(conversationId, chatResponse.getResult().getOutput());

while (chatResponse.hasToolCalls()) {
    ToolExecutionResult toolExecutionResult = toolCallingManager.executeToolCalls(promptWithMemory,
            chatResponse);
    chatMemory.add(conversationId, toolExecutionResult.conversationHistory()
        .get(toolExecutionResult.conversationHistory().size() - 1));
    promptWithMemory = new Prompt(chatMemory.get(conversationId), chatOptions);
    chatResponse = chatModel.call(promptWithMemory);
    chatMemory.add(conversationId, chatResponse.getResult().getOutput());
}

UserMessage newUserMessage = new UserMessage("What did I ask you earlier?");
chatMemory.add(conversationId, newUserMessage);

ChatResponse newResponse = chatModel.call(new Prompt(chatMemory.get(conversationId)));
```

### Exception Handling

When a tool call fails, the exception is propagated as a `ToolExecutionException` which can be caught to handle the error. A `ToolExecutionExceptionProcessor` can be used to handle a `ToolExecutionException` with two outcomes: either producing an error message to be sent back to the AI model or throwing an exception to be handled by the caller.

```java
@FunctionalInterface
public interface ToolExecutionExceptionProcessor {

	/**
	 * Convert an exception thrown by a tool to a String that can be sent back to the AI
	 * model or throw an exception to be handled by the caller.
	 */
	String process(ToolExecutionException exception);

}
```

If you’re using any of the Spring AI Spring Boot Starters, `DefaultToolExecutionExceptionProcessor` is the autoconfigured implementation of the `ToolExecutionExceptionProcessor` interface. By default, the error message is sent back to the model. The `DefaultToolExecutionExceptionProcessor` constructor lets you set the `alwaysThrow` attribute to `true` or `false`. If `true`, an exception will be thrown instead of sending an error message back to the model.

```java
@Bean
ToolExecutionExceptionProcessor toolExecutionExceptionProcessor() {
    return new DefaultToolExecutionExceptionProcessor(true);
}
```

**📌 NOTE**
If you defined your own `ToolCallback` implementation, make sure to throw a `ToolExecutionException` when an error occurs as part of the tool execution logic in the `call()` method.

The `ToolExecutionExceptionProcessor` is used internally by the default `ToolCallingManager` (`DefaultToolCallingManager`) to handle exceptions during tool execution. See [Tool Execution](#tool-execution) for more details about the tool execution lifecycle.

## Tool Resolution

The main approach for passing tools to a model is by providing the `ToolCallback`(s) when invoking the `ChatClient` or the `ChatModel`,
using one of the strategies described in [Methods as Tools](#methods-as-tools) and [Functions as Tools](#functions-as-tools).

However, Spring AI also supports resolving tools dynamically at runtime using the `ToolCallbackResolver` interface. 

```java
public interface ToolCallbackResolver {

	/**
	 * Resolve the {@link ToolCallback} for the given tool name.
	 */
	@Nullable
	ToolCallback resolve(String toolName);

}
```

When using this approach:

* On the client-side, you provide the tool names to the `ChatClient` or the `ChatModel` instead of the `ToolCallback`(s).
* On the server-side, a `ToolCallbackResolver` implementation is responsible for resolving the tool names to the corresponding `ToolCallback` instances.

By default, Spring AI relies on a `DelegatingToolCallbackResolver` that delegates the tool resolution to a list of `ToolCallbackResolver` instances:

* The `SpringBeanToolCallbackResolver` resolves tools from Spring beans of type `Function`, `Supplier`, `Consumer`, or `BiFunction`. See [_dynamic_specification_bean](#_dynamic_specification_bean) for more details.
* The `StaticToolCallbackResolver` resolves tools from a static list of `ToolCallback` instances. When using the Spring Boot Autoconfiguration, this resolver is automatically configured with all the beans of type `ToolCallback` defined in the application context.

If you rely on the Spring Boot Autoconfiguration, you can customize the resolution logic by providing a custom `ToolCallbackResolver` bean.

```java
@Bean
ToolCallbackResolver toolCallbackResolver(List<FunctionCallback> toolCallbacks) {
    StaticToolCallbackResolver staticToolCallbackResolver = new StaticToolCallbackResolver(toolCallbacks);
    return new DelegatingToolCallbackResolver(List.of(staticToolCallbackResolver));
}
```

The `ToolCallbackResolver` is used internally by the `ToolCallingManager` to resolve tools dynamically at runtime, supporting both [_framework_controlled_tool_execution](#_framework_controlled_tool_execution) and [_user_controlled_tool_execution](#_user_controlled_tool_execution).

## Observability

Tool calling includes observability support with spring.ai.tool observations that measure completion time and propagate tracing information. See [Tool Calling Observability](observability/index.adoc#_tool_calling).

Optionally, Spring AI can export tool call arguments and results as span attributes, disabled by default for sensitivity reasons. Details: [Tool Call Arguments and Result Data](observability/index.adoc#_tool_call_arguments_and_result_data).

### Logging

All the main operations of the tool calling features are logged at the `DEBUG` level. You can enable the logging by setting the log level to `DEBUG` for the `org.springframework.ai` package.
