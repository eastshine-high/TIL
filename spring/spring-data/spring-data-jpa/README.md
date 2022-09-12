## Contents

- Query Methods
    - [메소드 이름으로 쿼리 정의하기](https://github.com/eastshine-high/til/blob/main/spring/spring-data/spring-data-jpa/query-methods/defining-query-from-the-method-name.md)
    - [다양한 방법으로 쿼리 정의하기](https://github.com/eastshine-high/til/blob/main/spring/spring-data/spring-data-jpa/query-methods/the-various-ways-to-create-a-query-method.md)
- [Auditing](https://github.com/eastshine-high/til/blob/main/spring/spring-data/spring-data-jpa/auditing.md)
- [Pagination](https://github.com/eastshine-high/til/blob/main/spring/spring-data/spring-data-jpa/pagination.md)

## Spring Data JPA

Spring Data 패밀리 중의 하나인 Spring Data JPA는 JPA에 기반한 리포지토리 구현을 쉽게 해줍니다. 이 모듈은 JPA 기반의 데이터 접근 계층에 대한 향상된 지원을 다룹니다. 이 모듈은 데이터 접근 기술들을 사용하는 Spring 기반 애플리케이션을 더 쉽게 구축할 수 있게 합니다.

애플리케이션의 데이터 접근 계층을 구현하는 일은 꽤 오랫동안 성가신 일이었습니다. 간단한 쿼리를 실행하고 페이지 매김 및 감사를 수행하려면 너무 많은 상용구 코드를 작성해야 합니다. Spring Data JPA는 데이터 접근 계층을 구현하는 수고를 실제 필요한 양으로 줄이도록 개선하는 것을 목표로 합니다. 개발자가 리포지토리 인터페이스와 그 안에 커스텀 검색 메소드(finder methods)를 작성하면, Spring이 자동으로 구현을 제공합니다.

### Features

- Sophisticated support to build repositories based on Spring and JPA
- Support for [Querydsl](http://www.querydsl.com/) predicates and thus type-safe JPA queries
- Transparent auditing of domain class
- Pagination support, dynamic query execution, ability to integrate custom data access code
- Validation of `@Query` annotated queries at bootstrap time
- Support for XML based entity mapping
- JavaConfig based repository configuration by introducing `@EnableJpaRepositories`.

### Reference

[https://spring.io/projects/spring-data-jpa](https://spring.io/projects/spring-data-jpa)
