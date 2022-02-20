# **DispatcherServlet**

다른 많은 웹 프레임워크와 마찬가지로 Spring MVC는 중심 서블릿인 `DispatcherServlet`이 요청(request)을 처리하기 위해 공유된 알고리즘을 제공하면서 실제 작업은 구성 가능한(configurable) 위임(delegate) 컴포넌트들에 의해 수행되는 **프론트 컨트롤러 패턴**으로 설계되었다. 이 모델은 유연하고 다양한 워크플로우들을 지원한다.

`DispatcherServlet`은 어느 서블릿과 마찬가지로 `web.xml`이나 Java configuration을 사용하여 (서블릿 사양에 맞게)선언하고 매핑되어야 한다. 그러면 `DispatcherServlet`은 Spring configuration을 사용하여 요청(request) 매핑, 뷰 정의, 예외 처리 [등](https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/web.html#mvc-servlet-special-bean-types)에 필요한 위임 컴포넌트들을 찾는다.

아래는 Java configuration은 (서블릿 컨테이너에 의해 자동으로 탐지된다)`DispatcherServlet`을 등록하고 초기화하는 예이다.

```java
public class MyWebApplicationInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext servletContext) {

        // Load Spring web application configuration
        AnnotationConfigWebApplicationContext context = new AnnotationConfigWebApplicationContext();
        context.register(AppConfig.class);

        // Create and register the DispatcherServlet
        DispatcherServlet servlet = new DispatcherServlet(context);
        ServletRegistration.Dynamic registration = servletContext.addServlet("app", servlet);
        registration.setLoadOnStartup(1);
        registration.addMapping("/app/*");
    }
}
```

아래의 `web.xml` 설정은 `DispatcherServlet`을 등록하고 초기화한다.

```xml
<web-app>

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/app-context.xml</param-value>
    </context-param>

    <servlet>
        <servlet-name>app</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value></param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>app</servlet-name>
        <url-pattern>/app/*</url-pattern>
    </servlet-mapping>

</web-app>
```

## 참조

[https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/web.html#mvc-servlet](https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/web.html#mvc-servlet)
