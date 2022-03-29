# Interception

스프링 인터셉터도 서블릿 필터와 같이 웹과 관련된 공통 관심 사항을 효과적으로 해결할 수 있는 기술이다.
서블릿 필터가 서블릿이 제공하는 기술이라면, **스프링 인터셉터는 스프링 MVC가 제공하는 기술**이다. 둘다
웹과 관련된 공통 관심 사항을 처리하지만, 적용되는 순서와 범위, 그리고 사용방법이 다르다. 스프링 인터셉터는 서블릿 필터보다 편리하고, 더 정교하고 다양한 기능을 지원한다.

### 스프링 인터셉터 흐름

```
HTTP 요청 ->WAS-> 필터 -> 서블릿(dispatcher) -> 스프링 인터셉터 -> 컨트롤러
```

- 스프링 인터셉터는 디스패처 서블릿과 컨트롤러 사이에서 컨트롤러 호출 직전에 호출 된다.
- 스프링 인터셉터는 스프링 MVC가 제공하는 기능이기 때문에 결국 디스패처 서블릿 이후에 등장하게 된다.
스프링 MVC의 시작점이 디스패처 서블릿이라고 생각해보면 이해가 될 것이다.
- 스프링 인터셉터에도 URL 패턴을 적용할 수 있는데, 서블릿 URL 패턴과는 다르고, 매우 정밀하게 설정할
수 있다.
    - 스프링이 제공하는 URL 경로에 대한 설명은 다음을 참조한다.
    - [https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/util/pattern/PathPattern.html](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/util/pattern/PathPattern.html)

### 스프링 인터셉터 제한

```
HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 스프링 인터셉터 -> 컨트롤러 //로그인 사용자
HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 스프링 인터셉터(적절하지 않은 요청이라 판단, 컨트롤러 호출 X) // 비 로그인 사용자
```

- 인터셉터에서 적절하지 않은 요청이라고 판단하면 거기에서 끝을 낼 수도 있다. 그래 로그인 여부를 체크하기에 딱 좋다.

### 스프링 인터셉터 체인

```
HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 인터셉터1 -> 인터셉터2 -> 컨트롤러
```

- 스프링 인터셉터는 체인으로 구성되는데, 중간에 인터셉터를 자유롭게 추가할 수 있다. 예를 들어서 로그를 남기는 인터셉터를 먼저 적용하고, 그 다음에 로그인 여부를 체크하는 인터셉터를 만들 수 있다.

## 스프링 인터셉터 구현하기

스프링의 인터셉터를 사용하려면 `org.springframework.web.servlet.HandlerInterceptor` 인터페이스를 구현하면 된다.

```java
public interface HandlerInterceptor {

    default boolean preHandle(HttpServletRequest request, 
                              HttpServletResponse response, 
                              Object handler) throws Exception { }
    
    default void postHandle(HttpServletRequest request, 
                            HttpServletResponse response, 
                            Object handler,
                            @Nullable ModelAndView modelAndView) throws Exception {
    }
    
    default void afterCompletion(HttpServletRequest request, 
                                 HttpServletResponse response, 
                                 Object handler,
                                 @Nullable Exception ex) throws Exception {}    
}
```

### 인터셉터 호출 흐름

![https://media.vlpt.us/images/eastshine-high/post/9c04a19f-a261-4103-abfa-ce433bedade0/spring-interceptor.png](https://media.vlpt.us/images/eastshine-high/post/9c04a19f-a261-4103-abfa-ce433bedade0/spring-interceptor.png)

### 정상적인 상황

- `preHandle` : 컨트롤러 호출 전에 호출된다. (더 정확히는 핸들러 어댑터 호출 전에 호출된다.)
    - `preHandle` 의 응답값이 `true` 이면 다음으로 진행하고, `false` 이면 더는 진행하지 않는다. `false`인 경우 나머지 인터셉터는 물론이고, 핸들러 어댑터도 호출되지 않는다. 그림에서 1번에서 끝이 나버린다.
- `postHandle` : 컨트롤러 호출 후에 호출된다. (더 정확히는 핸들러 어댑터 호출 후에 호출된다.)
- `afterCompletion` : 뷰가 렌더링 된 이후에 호출된다(After the complete request has finished).

### 예외 발생시

- `preHandle` : 컨트롤러 호출 전에 호출된다.
- `postHandle` : 컨트롤러에서 예외가 발생하면 `postHandle`은 호출되지 않는다.
- `afterCompletion` : `afterCompletion` 은 예외가 발생해도 항상 호출된다.
    - 예외가 발생하면 `afterCompletion()`에 예외 정보(`ex`)를 포함해서 호출된다. 예외(`ex`)를 파라미터로 받아서 어떤 예외가 발생했는지 로그로 출력할 수 있다.
    - 예외가 발생하면 `postHandle()` 는 호출되지 않으므로 예외와 무관하게 공통 처리를 하려면`afterCompletion()` 을 사용해야 한다.

## 인터셉터 등록

`WebMvcConfigurer`가 제공하는 `addInterceptors()`를 사용해서 인터셉터를 등록할 수 있다. 

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
   @Override
   public void addInterceptors(InterceptorRegistry registry) {
				registry.addInterceptor(new LogInterceptor())
				                  .order(1)
				                  .addPathPatterns("/**")
				                  .excludePathPatterns("/css/**", "/*.ico", "/error");

				registry.addInterceptor(new LoginCheckInterceptor())
				                  .order(2)
				                  .addPathPatterns("/**")
				                  .excludePathPatterns(
			                          "/", "/members/add", "/login", "/logout",
			                          "/css/**", "/*.ico", "/error"
													);
   }
	 //...
}
```

- `registry.addInterceptor(new LogInterceptor())` : 인터셉터를 등록한다.
- `order()` : 인터셉터의 호출 순서를 지정한다. 낮을 수록 먼저 호출된다.
- `addPathPatterns("/**")` : 인터셉터를 적용할 URL 패턴을 지정한다.
- `excludePathPatterns("/css/**", "/*.ico", "/error")` : 인터셉터에서 제외할 패턴을 지정한다.

## 참조

<김영한, (강의)스프링 MVC 2편 - 백엔드 웹 개발 활용 기술, 인프런>

[https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/web.html#mvc-handlermapping-interceptor](https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/web.html#mvc-handlermapping-interceptor)
