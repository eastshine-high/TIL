# Testing

Spring MVC 애플리케이션의 `spring-test`에서 사용할 수 있는 옵션을 요약하면 다음과 같다.

- Servlet API Mocks: 컨트롤러, 필터 및 웹 컴포넌트들의 단위 테스트에 대한 Servlet API 규약들의 Mock 구현이다.
    - 참조: mock objects의 [Servlet API](https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/testing.html#mock-objects-servlet)
- TestContext Framework: JUnit 및 TestNG 테스트들에서 Spring configuration 로드를 지원한다. 또한 테스트 메소드들 전반에 걸쳐 로드된 configuration의 효율적인 캐싱과 `MockServletContext`를 통한 `WebApplicationContext`의 로드를 지원한다.
    - 참조: [TestContext Framework](https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/testing.html#testcontext-framework)
- Spring MVC Test: `MockMvc`로도 알려졌다. `DispatcherServlet`(어노테이션들을 지원)을 통해 어노테이션된 컨트롤러들을 테스트하기 위한 프레임워크이다. HTTP 서버 없이 Spring MVC 인프라를 완성한다.
    - 참조: [Spring MVC Test](https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/testing.html#spring-mvc-test-framework)
- Client-side REST: `spring-test`는 내부적으로 `RestTemplate`을 사용하는 클라이언트 측 코드를 테스트하기 위한 목(mock) 서버로 사용할 수 있는 `MockRestServiceServer`를 제공한다.
    - 참조: [Client REST Tests](https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/testing.html#spring-mvc-test-client)
- `WebTestClient`: WebFlux 애플리케이션 테스트용으로 제작되었지만 HTTP 연결을 통해 모든 서버에 대한 종단 간 통합 테스트에도 사용할 수 있다. 비차단 반응 클라이언트이며 비동기 및 스트리밍 시나리오를 테스트하는 데 적합하다.

### 참조

[https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/web.html#testing](https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/web.html#testing)
