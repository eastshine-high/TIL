## Contents

- 도메인 모델
    - [연관 관계(Associations)](https://github.com/eastshine-high/til/blob/main/java/specification/jpa-hibernate/domain-model/associations.md)
    - [복합 식별자(Composite identifiers)](https://github.com/eastshine-high/til/blob/main/java/specification/jpa-hibernate/domain-model/composite-identifiers.md)
- [영속성 컨텍스트](https://github.com/eastshine-high/til/tree/main/java/specification/jpa-hibernate/persistence-context)
    - [영속 엔티티의 상태](https://github.com/eastshine-high/til/blob/main/java/specification/jpa-hibernate/persistence-context/persistent-data-status.md)
    - [영속성 전이, 고아 객체 제거](https://github.com/eastshine-high/til/blob/main/java/specification/jpa-hibernate/persistence-context/cascading-entity-state-transitions.md)
- 성능 개선(Performance Tuning)
    - [Fetching](https://github.com/eastshine-high/til/blob/main/java/specification/jpa-hibernate/performance-tuning/fetching.md)

## JPA(Java Persistence API)

JPA는 자바 진영에서 ORM(Object-Relational Mapping) 기술 표준으로 사용되는 인터페이스의 모음입니다.

The Java Persistence API provides a POJO persistence model for object-relational mapping. The Java Persistence API was developed by the EJB 3.0 software expert group as part of JSR 220, but its use is not limited to EJB software components. It can also be used directly by web applications and application clients, and even outside the Java EE platform, for example, in Java SE applications. See [JSR 220](http://www.jcp.org/en/jsr/detail?id=220).

## Hibernate

Hibernate는 영속화 모델(표준)인 JPA를 구현한 ORM 솔루션입니다. 아래 다이어그램은 JPA와 Hibernate의 상속 및 구현 관계를 나타냅니다.

As a JPA provider, Hibernate implements the Java Persistence API specifications and the association between JPA interfaces and Hibernate specific implementations can be visualized in the following diagram:

![https://docs.jboss.org/hibernate/orm/5.6/userguide/html_single/images/architecture/JPA_Hibernate.svg](https://docs.jboss.org/hibernate/orm/5.6/userguide/html_single/images/architecture/JPA_Hibernate.svg)

JPA의 핵심인 `EntityManagerFactory`, `EntityManager`, `EntityTransaction`을 Hibernate에서는 각각 `SessionFactory`, `Session`, `Transaction`으로 상속받고 각각 `Impl`로 구현하고 있음을 확인할 수 있습니다.

---

**참조**

https://www.oracle.com/java/technologies/persistence-jsp.html

https://docs.jboss.org/hibernate/orm/5.6/userguide/html_single/Hibernate_User_Guide.html#architecture-overview
