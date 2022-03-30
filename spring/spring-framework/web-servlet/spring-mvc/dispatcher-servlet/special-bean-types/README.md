# Special Bean Types

DispatcherServlet은 요청을 처리하고 적절한 응답을 렌더링하기 위해 **특수한 객체들(special beans**)에 위임한다. **특수한 객체들**은 프레임워크 계약들을 구현하는 Spring에서 관리되는 객체 인스턴스들을 의미한다. 일반적으로 기본 내장 계약들로 제공되지만 빈을 교체하거나 확장하거나 빈의 속성들을 커스텀할 수 있다.

Spring MVC 구조

![https://velog.velcdn.com/images/eastshine-high/post/fb66b86f-9b40-4439-8f78-c9bdd192c9b0/image.png](https://velog.velcdn.com/images/eastshine-high/post/fb66b86f-9b40-4439-8f78-c9bdd192c9b0/image.png)

아래 나열한 bean들은 DispatcherServlet에 의해 탐지되는 **특수한 객체들(special beans**)이다.

- HandlerMapping
- HandlerAdapter
- [HandlerExceptionResolver](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-exceptionhandlers)
- [ViewResolver](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-viewresolver)
- [LocaleResolver](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-localeresolver), LocaleContextResolver
- [ThemeResolver](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-themeresolver)
- [MultipartResolver](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-multipart)
- [FlashMapManager](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-flash-attributes)

### 참조

[https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-servlet-special-bean-types](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-servlet-special-bean-types)

이미지 : <김영한, (강의)스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술, 인프런>
