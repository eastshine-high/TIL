# Spring Framework

## Table of Contents

- Core

    - [DI 컨테이너](https://github.com/eastshine-high/til/blob/main/spring/spring-framework/core/di-container.md)

- MVC

    - [Dispatcher Servlet](https://github.com/eastshine-high/til/tree/main/spring/spring-framework/web-servlet/spring-mvc/dispatcher-servlet)

        - [특수한 객체 유형(Special Bean Types)](https://github.com/eastshine-high/til/tree/main/spring/spring-framework/web-servlet/spring-mvc/dispatcher-servlet/special-bean-types)

            - [HandlerAdapter](https://github.com/eastshine-high/til/blob/main/spring/spring-framework/web-servlet/spring-mvc/dispatcher-servlet/special-bean-types/handler-adapter.md)

        - [인터셉터interceptor)](https://github.com/eastshine-high/til/blob/main/spring/spring-framework/web-servlet/spring-mvc/dispatcher-servlet/interception.md)

    - [Testing](https://github.com/eastshine-high/til/blob/main/spring/spring-framework/web-servlet/spring-mvc/testing.md)

- Blog

    - [JSON Merge Patch를 이용한 PATCH API 구현](https://github.com/eastshine-high/til/blob/main/spring/spring-framework/blog/json-merge-patch.md)

    - [동시성 문제(1) - 동시성 문제 살펴보기](https://github.com/eastshine-high/til/blob/main/spring/spring-framework/blog/concurrency-Issue-1.md)

    - [동시성 문제(2) - MySQL에서 동시성 문제 해결하기](https://github.com/eastshine-high/til/blob/main/spring/spring-framework/blog/concurrency-Issue-2.md)

    - 동시성 문제(3) - Redis에서 동시성 문제 해결하기(예정)

---

## 스프링 프레임워크는 하나로 구성된 기술이 아닙니다

[Spring Framework Documentation 5.3.15의 문서](https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/)를 보면 스프링 프레임워크가 아래와 같이 다양한 기술로 구성되어 있음을 확인할 수 있습니다. 이 기술들을 통합해서 스프링 프레임워크라 합니다(최근에는 스프링 부트를 통해서 스프링 프레임워크의 기술들을 더 편리하게 사용할 수 있습니다).

- [핵심 기술](https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/core.html) : 스프링 DI 컨테이너, AOP, 이벤트, 기타
- 웹 기술: [스프링 MVC](https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/web.html), [스프링 WebFlux](https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/web-reactive.html)
- [데이터 접근 기술](https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/data-access.html) : 트랜잭션, JDBC, ORM 지원, XML 지원
- [기술 통합](https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/integration.html) : 캐시, 이메일, 원격접근, 스케줄링
- [테스트](https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/testing.html) : 스프링 기반 테스트 지원
- [언어](https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/languages.html) : 코틀린, 그루비
