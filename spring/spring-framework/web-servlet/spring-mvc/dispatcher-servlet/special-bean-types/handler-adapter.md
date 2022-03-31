# HandlerAdapter

**HandlerAdapter**는 DispatcherServlet이 (핸들러가 실제로 어떻게 호출되는 지와 관계없이)요청에 매핑된 핸들러([**HandlerMapping**](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-servlet-special-bean-types)에 의해 매핑됨)를 호출하도록 돕는다. 예를 들어 어노테이션이 달린 컨트롤러를 호출하려면 먼저 어노테이션을 해결해야 한다. HandlerAdapter의 주요 목적은 그런한 세부 사항에서 DispatcherServlet을 보호하는 것이다.

**RequestMappingHandlerAdapter 동작 방식**

![https://velog.velcdn.com/images/eastshine-high/post/f0f2ca7c-53b6-4cc6-9d67-6402aa1d3845/image.png](https://velog.velcdn.com/images/eastshine-high/post/f0f2ca7c-53b6-4cc6-9d67-6402aa1d3845/image.png)

## HandlerMethodArgumentResolver(ArgumentResolver)

[애노테이션 기반의 컨트롤러](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-controller)는 매우 다양한 파라미터를 사용할 수 있다. `HttpServletRequest`, `Model` 은 물론이고, `@RequestParam`, `@ModelAttribute` 같은 애노테이션 그리고 `@RequestBody`, `HttpEntity` 같은 HTTP 메시지를 처리하는 부분까지 매우 큰 유연함을 보여준다.
이렇게 파라미터를 유연하게 처리할 수 있는 이유가 바로 ArgumentResolver 덕분이다. 애노테이션 기반 컨트롤러를 처리하는 RequestMappingHandlerAdaptor는 바로 이 ArgumentResolver 를 호출해서 컨트롤러(핸들러)가 필요로 하는 다양한 파라미터의 값(객체)을 생성한다. 그리고 이렇게 파리미터의 값이 모두 준비되면 컨트롤러를 호출하면서 값을 넘겨준다.

스프링은 30개가 넘는 ArgumentResolver 를 기본으로 제공한다. 사용 가능한 파라미터 목록은 아래 공식 문서에서 확인할 수 있다.

[https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-arguments](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-arguments)

정확히는 HandlerMethodArgumentResolver 인데 줄여서 ArgumentResolver라고 부른다.

```java
public interface HandlerMethodArgumentResolver {
    boolean supportsParameter(MethodParameter parameter);

    @Nullable
    Object resolveArgument(MethodParameter parameter, 
                           @Nullable ModelAndViewContainer mavContainer,
                           NativeWebRequest webRequest, 
                           @Nullable WebDataBinderFactory binderFactory) throws Exception;
}
```

ArgumentResolver의 `supportsParameter()` 를 호출해서 해당 파라미터를 지원하는지 체크하고,
지원하면 `resolveArgument()` 를 호출해서 실제 객체(컨토롤러의 핸들러 아규먼트에 선언한 것들 전부)를 생성한다. 그리고 이렇게 생성된 객체가 컨트롤러 호출시 넘어가는 것이다.

## ReturnValueHandler(HandlerMethodReturnValueHandler)

HandlerMethodReturnValueHandler 를 줄여서 ReturnValueHandle 라 부른다. ArgumentResolver 와 비슷한데, 이것은 응답 값을 변환하고 처리한다. 컨트롤러에서 String으로 뷰 이름을 반환해도, 동작하는 이유가 바로 ReturnValueHandler 덕분이다. 
스프링은 10여개가 넘는 ReturnValueHandler를 지원한다. ex)`ModelAndView`, `@ResponseBody`, `HttpEntity`, `String` 가능한 응답 값 목록은 다음 공식 문서에서 확인할 수 있다.

[https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-return-types](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-return-types)

## 참조

<김영한, (강의)스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술, 인프런>

[https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-servlet-special-bean-types](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-servlet-special-bean-types)
