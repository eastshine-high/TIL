# Persistent data status

## 영속 데이터(엔티티)의 상태

영속 데이터(엔티티)는 영속성 컨텍스트와 기반 데이터베이스에 관련한 상태를 갖는다.

Persistent data has a state in relation to both a persistence context and the underlying database.

- 비영속(transient)
    - 영속성 컨텍스트와 전혀 관계가 없는 상태.
    - 객체를 생성하고 아직 저장하지 않았을 때의 상태이다.
- 영속(managed or persistent)
    - 영속성 컨텍스트에 저장(관리)된 상태
    - 명령어 : `em.persist(member)` , `find()` , `merge()`
- 준영속(detached)
    - 영속성 컨텍스트에 저장되었다가 분리된 상태
    - `em.clear(member)`를 호출해서 영속성 컨텍스트를 초기화해도 영속성 컨텍스트가 관리하던 영속 상태의 엔티티는 준영속 상태가 된다.
    - 명령어 : `em.detach(member)` , `em.close()`
- 삭제(removed)
    - 삭제된 상태
    - 엔티티를 영속성 컨텍스트와 데이터베이스에서 삭제한다.
    - 명령어 : `em.remove(member)`

## 영속화(Making entities persistent)

먼저 새 엔터티 인스턴스(표준 `new` 연산자를 사용하여)를 만들면, `new` 상태가 된다. 이것을 `org.hibernate.Session` 또는 `javax.persistence.EntityManager`에 연결하면 영속적으로 만들 수 있다.

JPA를 이용한 엔티티 영속화

```java
Person person = new Person();
person.setId( 1L );
person.setName("John Doe");

entityManager.persist( person );
```

Hibernate API을 이용한 엔티티 영속화

```java
Person person = new Person();
person.setId( 1L );
person.setName("John Doe");

session.save( person );
```

### 쓰기 지연(transactional write-behind)

엔티티 매니저는 트랜잭션을 커밋하기 직전까지 데이터베이스에 엔티티를 저장하지 않고 내부 쿼리 저장소에 INSERT SQL을 차곡차곡 모아둔다.

```groovy
transaction.begin();

entityManager.persist(person1);
entityManager.persist(person2);

transaction.commit();
```

그리고 트랜잭션을 커밋할 때 모아둔 쿼리를 데이터베이스에 보내는데 이것을 트랜잭션을 지원하는 **쓰기 지연(transactional write-behind)**이라 한다. 트랜잭션을 커밋하면 엔티티 매니저는 우선 영속성 컨텍스트를 플러시한다. 좀 더 구체적으로 쓰기 지연 SQL 저장소에 모인 쿼리를 데이터베이스에 보낸다.

## 엔티티 삭제(Deleting entities)

엔티티들은 삭제될 수 있다.

Hibernate API를 이용한 엔티티 삭제

```java
session.delete( person );
```

JPA를 이용한 엔티티 삭제

```java
entityManager.remove( person );
```

- JPA에서 엔티티를 삭제하려면 엔티티 인스턴스는 반드시 영속 상태일 때 가능하다.
- `entityManager.remove( person )`를 호출하는 순간 `person`는 영속성 컨텍스트에서 제거된다. 이후 트랜잭션을 커밋해서 플러시를 호출하면 실제 데이터베이스에 삭제 쿼리를 전달한다. 이렇게 삭제된 엔티티는 재사용하지 말고 자연스럽게 가비지 컬렉션의 대상이 되도록 두는 것이 좋다.

## 엔티티 조회

**영속성 컨텍스트는 내부에 캐시를 가지고 있는데 이것을 1차 캐시라 한다. 영속 상태의 엔티티는 모두 이곳에 저장된다.** 쉽게 이야기하면 영속성 컨텍스트 내부에 Map이 하나 있는데 키는 `@Id`로 매핑한 식별자고 값은 엔티티 인스턴스다. 그리고 식별자(`@Id`)는 데이터베이스 기본 키와 매핑되어 있다.

### 데이터를 초기화하여 조회하기

엔티티를 조회하는 기본 메소드는 `find`다.

```java
 public <T> T find(Class<T> entityClass, Object primaryKey)
```

예를 들어, `entityManger.find( Member.class, "33");`를 호출하면 먼저 1차 캐시에서 엔티티를 찾고 만약 찾는 엔티티가 1차 캐시에 없으면 데이터베이스에서 조회한다. 이 때, 조회한 엔티티를 실제 사용하든 사용하지 않든 데이터베이스를 조회하여 엔티티의 데이터를 초기화한다. 이러한 데이터 초기화 방식을 즉시 로딩(eager loading)이라 한다.

### 데이터 초기화 없이 조회하기

엔티티 데이터의 초기화를 엔티티의 실제 사용 시점까지 미룰 수도 있다. 이 때는 `getReference` 메소드를 사용한다.

예를 들어, `entityManger.getReference(Meber.class, “33”);`를 호출하면 JPA는 데이터베이스르 조회하지 않고 실제 엔티티 객체도 생성하지 않는다. 대신에 데이터베이스 접근을 위임한 프록시 객체를 반환한다. 이 프록시 객체에  실제 데이터를 조회하는 메소드(`member.getName()`)가 호출되면, 프록시 객체는 실제 엔티티가 생성되어 있지 않으면 영속성 컨텍스트에 실제 엔티티 생성을 요청하는데 이것을 초기화라 한다. 영속성 컨텍스트는 데이터베이스를 조회해서 실제 엔티티 객체를 생성한다. 이러한 데이터 초기화 방식을 지연 로딩(lazy loading)이라 한다.

### 영속 엔티티의 동일성 보장

`em.find(Member.class, "member1")`을 반복해서 호출해도 영속성 컨텍스트는 1차 캐시에 있는 같은 엔티티 인스턴스를 반환한다. 따라서 영속성 컨텍스트는 성능상 이점과 엔티티의 동일성(`==`)을 보장한다.

## **엔티티 수정**

**변경 감지(dirty checking)**

JPA로 엔티티를 수정할 대는 단순히 엔티티를 조회해서 데이터만 변경하면 된다. 트랜잭션 커밋 직전에 `em.update()` 메소드를 실행해야 할 것 같지만 이런 메소드는 없다. 엔티티의 데이터만 변경했는데 어떻게 데이터베이스에 반영이 되는 걸까? 이렇게 엔티티의 변경사항을 데이터베이스에 자동으로 반영하는 기능을 변경 감지(dirty checking)라 한다.

JPA는 엔티티를 영속성 컨텍스트에 보관할 때, 최초 상태를 복사해서 저장해두는데 이것을 스냅샷이라 한다. 그리고 플러시 시점에 스냅샷과 엔티티를 비교해서 변경된 엔티티를 찾는다.

엔티티가 수정되는 과정은 다음과 같다.

1. 트랜잭션을 커밋하면 앤티티 매니저 내부에서 먼저 플러시(flush)가 호출된다.
2. 엔티티와 스냅샷을 비교해서 변경된 엔티티를 찾는다.
3. 변경된 엔티티가 있으면 수정 쿼리를 생성해서 쓰기 지연 SQL 저장소에 보낸다.
4. 쓰기 지연 저장소의 SQL을 데이터베이스에 보낸다.
5. 데이터베이스 트랜잭션을 커밋한다.

> 변경 감지는 영속성 컨텍스트가 관리하는 영속 상태의 엔티티에만 적용된다. 비영속, 준영속처럼 영속성 컨텍스트의 관리를 받지 못하는 엔티티는 값을 변경해도 데이터베이스에 반영되지 않는다.
> 

## 참조

[https://docs.jboss.org/hibernate/orm/5.6/userguide/html_single/Hibernate_User_Guide.html#pc](https://docs.jboss.org/hibernate/orm/5.6/userguide/html_single/Hibernate_User_Guide.html#pc)

<김영한, 자바 ORM 표준 JPA 프로그래밍, 에이콘>
