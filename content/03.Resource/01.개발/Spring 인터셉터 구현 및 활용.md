---
tags:
  - 개발
  - 기술정리
  - "#SpringMVC"
  - "#HandlerInterceptor"
  - "#WebMvcConfigurer"
  - "#인터셉터구현"
created: 2025-05-08
type: 기술정리
aliases:
  - Spring 인터셉터
  - HandlerInterceptor
  - Spring MVC 인터셉터
  - 인터셉터 vs 필터
  - WebMvcConfigurer
---

# 📘 Spring HandlerInterceptor

`HandlerInterceptor`는 Spring MVC에서 제공하는 기능으로, 컨트롤러 실행 전/후에 요청을 가로채어 전처리 또는 후처리를 할 수 있도록 해주는 인터페이스입니다. 이는 **DispatcherServlet 이후 의 처리 단계에 위치하며, Servlet Filter 와 달리 Spring MVC 요청에만 작동** 합니다.


## HandlerInterceptor 실행 흐름

Spring HandlerInterceptor 는 다음과 같은 순서로 실행됩니다.

```txt
HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 스프링 인터셉터 -> 컨트롤러
```


![image.png](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250516071638_image.png)

## HandlerInterceptor 인터페이스

HandlerInterceptor 또한 Filter 와 마찬가지로 하나의 인터페이스로 구성이 되어 있습니다.

```java
  
public interface HandlerInterceptor {  
    default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {  
        return true;  
    }
  
    default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable ModelAndView modelAndView) throws Exception {  
    }
  
    default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable Exception ex) throws Exception {  
    }
}
```

* preHandle() : 컨트롤러 실행 전 호출. `false`를 반환하면 컨트롤러 호출이 중단됩니다.
* postHandle() : 컨트롤러 로직 실행 후, View 렌더팅 을 할 필요가 없습니다. 
`
```java 
@Slf4j  
public class TestInterCeptor implements HandlerInterceptor {  
    @Override  
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {  
        String requestURI = request.getRequestURI();  
        String uuid = UUID.randomUUID().toString();  
  
        request.setAttribute("uuid", uuid);  
          
        log.info("Request URI: [{}],[{}],[{}]", uuid, requestURI, handler);  
        return true; // 다음 컨트롤러 호출  
    }
  
    @Override  
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {  
        log.info("postHandle [{}]",modelAndView);  
    }
  
    @Override  
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {  
        String requestURI = request.getRequestURI();  
        String uuid = (String)request.getAttribute("uuid");  
  
        log.info("Reponse [{}],[{}],[{}]", uuid, uuid, handler);  
        if(ex != null){  
            log.error("ERROR [{}],[{}]", requestURI, uuid, ex);  
        }
    }
}
```

## HandlerInterceptor 등록

이렇게 작성한  `TestInterCeptor` 를 사용하기 위해서는 Filter 와 동일하게 Spring Bean 으로 등록을 해줘야 합니다.

등록을 위해서는 `WebMvcConfigurer` 의 `addInterceptors` 를 구현하면 된다.  매개변수 인 InterceptorRegistry 의 addInterceptor 메소드를 활용하여 다음과 같이 등록할수 있습니다. 

```java
@Configuration  
public class WebConfig implements WebMvcConfigurer {  
    @Override  
    public void addInterceptors(InterceptorRegistry registry) {   
        registry.addInterceptor(new TestInterCeptor())
                .order(1)
                .addPathPatterns("/**")
                .excludePathPatterns("/css/**", "/*.ico", "/error");  
    }
}
```

Builder Pattern 을 활용하여 등록할수 있습니다. 

* order() : 인터셉터 실행 순서 지정
* addPathPatterns() : 인터셉터를 적용할 URL 패턴 지정 
* excludePathPatterns() : 인터셉터 적용 대상에서 제외할 경로 지정 (정적 리소스 등)


> [!tip] PathPattern
> PathPatters 를 활용하면 다양한 방법으로 URL 매핑이 가능합니다. 
> 공식 문서는 https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/util/pattern/PathPattern.html 에서 확인 가능합니다.

### PathPattern 클래스

Spring에서는 `PathPattern` 클래스를 통해 정교하고 성능 좋은 경로 매칭을 제공합니다.  PathPattern의 주요 매칭 규칙입니다:
- `?` : 하나의 문자와 일치
- `*` : 경로 세그먼트 내에서 0개 이상의 문자와 일치
- `**` : 경로의 끝까지 0개 이상의 세그먼트와 일치
- `{spring}` : 경로 세그먼트 하나와 일치하며 "spring"이라는 변수로 캡처
- `{spring:[a-z]+}` : `[a-z]+` 정규 표현식과 일치하며, "spring" 변수로 캡처
- `{*spring}` : 경로의 끝까지 0개 이상의 세그먼트와 일치하며, "spring" 변수로 캡처

## HandlerInterceptor 실행 결과 확인

다음 로그를 보면 우리가 예상했던 순서로 실행된 것을 확인할수 있다.

```txt
2025-04-16 10:20:30.759  INFO 1560 --- [nio-8080-exec-2] h.login.web.interceptor.TestInterCeptor  : TEST Request URI: [61736ed8-fdbc-4a03-aa6e-e4055376e2f3],[/],[hello.login.web.HomeController#homeLoginV3ArgumentResolver(Member, Model)]
2025-04-16 10:20:30.760  INFO 1560 --- [nio-8080-exec-2] h.l.w.a.LoginMemberArgumentResolver      : LoginMemberArgumentResolver resolveArgument
2025-04-16 10:20:30.760  INFO 1560 --- [nio-8080-exec-2] h.login.web.interceptor.TestInterCeptor  : postHandle [ModelAndView [view="home"; model={}]]
2025-04-16 10:20:30.761  INFO 1560 --- [nio-8080-exec-2] h.login.web.interceptor.TestInterCeptor  : TEST Reponse [61736ed8-fdbc-4a03-aa6e-e4055376e2f3],[61736ed8-fdbc-4a03-aa6e-e4055376e2f3],[hello.login.web.HomeController#homeLoginV3ArgumentResolver(Member, Model)]
```

## HandlerInterceptor 활용 예시: 세션 체크

### 세션 체크용 Interceptor 구현 

```java
@Slf4j
public class LoginCheckInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        String requestURI = request.getRequestURI();
        log.info("인증 체크 실행: {}", requestURI);

        HttpSession session = request.getSession(false);
        if (session == null || session.getAttribute("loginMember") == null) {
            log.info("미인증 사용자 요청 차단: {}", requestURI);
            response.sendRedirect("/login?redirectURL=" + URLEncoder.encode(requestURI, "UTF-8"));
            return false;
        }

        return true;
    }
}
```

### Interceptor 등록

```java
@Override
public void addInterceptors(InterceptorRegistry registry) {
    registry.addInterceptor(new LoginCheckInterceptor())
            .order(2)
            .addPathPatterns("/**")
            .excludePathPatterns("/", "/login", "/logout", "/css/**", "/js/**", "/favicon.ico", "/error");
}
```

## Filter vs Interceptor 비교

|항목|Filter|HandlerInterceptor|
|---|---|---|
|위치|WAS 레벨|Spring MVC 레벨|
|대상|전체 요청 (정적 포함)|컨트롤러 요청|
|기술 기반|Servlet API|Spring MVC|
|주요 용도|인코딩, 보안, 전역 처리|로그인, 권한 체크, 로깅|
|요청 객체|ServletRequest|HttpServletRequest|
