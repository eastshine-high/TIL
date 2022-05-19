# 영속성 컨텍스트(Persistence Context)

JPA를 이해하는 데 가장 중요한 용어는 **영속성 컨텍스트(Persistence Context**)이다. `org.hibernate.Session` API와 `javax.persistence.EntityManager` API는 모두 영속 데이터를 다루기 위한 컨텍스트를 나타낸다(represent). 이를 영속성 컨텍스트(persistence context)라고 한다.

영속성 컨텍스트는 **애플리케이션과 데이터베이스 사이에서 객체를 보관하는 가상의 데이터베이스 같은 역할**을 한다. 영속성 컨텍스트 덕분에 **1차 캐시**, **동일성 보장**, 트랜잭션을 지원하는 **쓰기 지연(transactional write-behind)**, **변경 감지(dirty checking)**, **지연 로딩** 기능을 사용할 수 있다.

- 영속성 컨텍스트에 저장한 엔티티는 **[플러시(flush)](https://docs.jboss.org/hibernate/orm/5.6/userguide/html_single/Hibernate_User_Guide.html#flushing)** 시점에 데이터베이스에 반영되는데 일반적으로 트랜잭션을 커밋할 때 영속성 컨텍스트가 플러시 된다.
- 영속성 컨텍스트가 관리하는 엔티티를 영속 상태의 엔티티라 하는데, 영속성 컨텍스트가 해당 엔티티를 더 이상 관리하지 못하면 그 엔티티는 준영속 상태의 엔티티라 한다. 준영속 상태의 엔티티는 더는 영속성 컨텍스트의 관리를 받지 못하므로 영속성 컨텍스트가 제공하는 1차 캐시, 동일성 보장, 트랜잭션을 지원하는 쓰기 지연, 변경 감지, 지연 로딩 같은 기능들을 사용할 수 없다.

### 엔티티 매니저(Entity Manager)

엔티티 매니저는 엔티티 매니저 팩토리에서 생성한다. 자바를 직접 다루는 J2SE 환경에서는 엔티티 매니저를 만들면 그 내부에 영속성 컨텍스트도 함께 만들어진다. 이 영속성 컨텍스트는 엔티티 매니저를 통해서 접근할 수 있다.

```
EntityManagerFactory entityMangerFactory =
    Persistence.createEntityManagerFactory("nameOfpersistence-unit");
EntityManager entityManger = entityMangerFactory.createEntityManager( );

```

엔티티 매니저 팩토리는 여러 스레드가 동시에 접근해도 안전하므로 서로 다른 스레드 간에 공유해도 되지만, 엔티티 매니저는 여러 스레드가 동시에 접근하면 동시성 문제가 발생하므로 스레드 간에 절대 공유하면 안 된다. 엔티티 매니저는 엔티티를 저장하고, 수정하고, 삭제하고, 조회하는 등 엔티티와 관련된 모든 일을 처리한다.

### 참조

<김영한, 자바 ORM 표준 JPA 프로그래밍, 에이콘>

https://docs.jboss.org/hibernate/orm/5.6/userguide/html_single/Hibernate_User_Guide.html#pc
