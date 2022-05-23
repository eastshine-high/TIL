# Fetching

너무 많은 데이터를 가져오는 것은 대다수의 JPA 애플리케이션의 가장 큰 성능 문제이다.

Hibernate는 엔터티 쿼리(JPQL/HQL 및 Criteria API)와 기본 SQL 문을 모두 지원한다. 엔터티 쿼리는 가져온 엔터티를 수정해야 하는 경우(자동 더티 체크 메커니즘의 이점이 있다)에만 유용하다.

읽기 전용 트랜잭션들의 경우, DTO 프로젝션들을 가져와야 한다. 이는 특정 비즈니스를 수행하는 데 필요한 만큼의 열만 선택할 수 있다. 또한 DTO 프로젝션들은 관리할 필요가 없기 때문에 현재 실행 중인 영속성 컨텍스트의 부하를 줄이는 것 등의 이점이 있다.

## Fetching associations

연관 관계(associations)와 관련하여 두 가지 주요한 페치(fetch) 전략이 있다.

- `EAGER` : 엔티티를 조회할 때 연관된 엔티티도 함께 조회한다.
- `LAZY` : 연관된 엔티티를 실제 사용할 때 조회한다.

`**EAGER` 페치는 거의 항상 나쁜 선택이다.**

> JPA 이전에 하이버네이트는 모든 연관에 `LAZY`를 적용했었다. 하지만 JPA 1.0 표준이 나왔을 때, 모든 JPA 구현체 제공자들이 프록시를 사용하지는 않는 것으로 간주되었다. 이런 이유로 `@ManyToOne` 연관과 `@OneToOne` 연관은 `EAGER`를 기본 전략으로 사용한다.
`EAGER` 페치 전략은 쿼리별로 덮어쓸 수 없으므로 필요하지 않은 경우에도 항상 연관 관계가 검색된다. 게다가 JPQL 쿼리에서 `JOIN` `FETCH`를 잊어버린 경우 Hibernate는 보조 명령문으로 이를 초기화하여 N+1 쿼리 문제를 일으킬 수 있다.
이러한 이유로 모든 연결이 기본적으로 `LAZY`로 표시되는 것이 좋다.
> 

### `EAGER` 전략과 N+1 query issues

다음 `Order`엔티티의 `member`는 `EAGER` 전략을 적용했다.

```java
@Entity
public class Order {
		@Id @GeneratedValue
		private long id;
		
		@ManyToOne(fetch = FetchType.EAGER)
		private Member member;
		...
}
```

`em.find()` 메소드로 엔티티를 조회할 때 연관된 엔티티를 로딩하는 전략이 `EAGER`이면 데이터베이스에 `JOIN` 쿼리를 사용해서 한 번에 연관된 엔티티까지 조회한다.

```java
Order order = em.find(Order.class, 1L);
```

실행된 SQL은 다음과 같다.

```
select o.*, m.*
from Order o
left outer join Member m on o.MEBER_ID=m.MEMBER_ID
where o.id = 1
```

실행된 SQL을 보면 EAGER로 설정한 `member` 엔티티를 `JOIN` 쿼리로 함께 조회한다. 여기까지 보면 `EAGER` 페치 전략이 상당히 좋아보이지만 **문제는 JPQL을 사용할 때 발생한다.** JPQL로 조회를 해보자.

```java
List<Order> orders = 
		em.createQuery("select o from Order o", Order.class)
		.getResultList(); // 연관된 모든 엔티티를 조회한다.
```

실행된 SQL은 다음과 같다.

```
select * from Order // JPQL로 실행된 SQL
select * from Member where id=? // EAGER로 실행된 SQL
select * from Member where id=? // EAGER로 실행된 SQL
select * from Member where id=? // EAGER로 실행된 SQL
```

JPA가 JPQL을 분석해서 SQL을 생성할 때는 글로벌 페치 전략을 참고하지 않고 오직 JPQL 자체만 사용한다. 따라서 `LAZY` 전략이든 `EAGER` 전략이든 구분하지 않고 JPQL 자체에 충실하게 SQL을 만든다. 

코드를 분석하면 내부에서 다음과 같은 순서로 동작한다.

1. `select o from Order o` JPQL을 분석해서 `select * from Order` SQL을 생성한다.
2. 데이터베이스에서 결과를 받아 `order` 엔티티 인스턴스들을 생성한다.
3. `Order.member`의 글로벌 페치 전략이 즉시 로딩이므로 `order` 를 로딩하는 즉시 연관된 `member`도 로딩해야 한다.
4. 연관된 `member`를 영속성 컨텍스트에서 찾는다.
5. 만약 영속성 컨텍스트에 없으면 `SELECT * FROM MEMBER WHERE id=?` SQL을 조회한 `order` 엔티티 수만큼 실행한다.

만약 조회한 order 엔티티가 10개이면 member를 조회하는 SQL도 10번 실행한다. 이처럼 처음 조회한 데이터 수만큼 다시 SQL을 사용해서 조회하는 것을 N+1 문제라 한다. N+1이 발생하면 SQL이 상당히 많이 호출되므로 조회 성능에 치명적이다. 따라서 최우선 최적화 대상이다. 이런 N+1 문제는 JPQL 페치 조인으로 해결할 수 있다.

### LAZY 전략과 N+1 query issues

특정 회원 하나를 

회원과 주문을 `LAZY` 전략으로 설정하면 어떻게 될까? 방금 살펴본 `EAGER` 시나리오를 `LAZY` 로딩으로 변경해도 N+1 문제에서 자유로울 수는 없다.

이번에는 회원이 참조하는 주문정보에 `LAZY`페치 전략을 지정해 보자.

```java
@Entity
public class Member {
		@Id @GeneratedValue
		private long id;
		
		@ManyToOne(amppedBy = "member", fetch = FetchType.LAZY)
		private List<Order> orders = new ArrayList<Order>();
		...
}
```

`LAZY` 전략으로 설정하면 JPQL에서는 N+1 문제가 발생하지 않는다. JPQL로 조회해보자.

```java
List<Order> orders = 
		em.createQuery("select o from Order o", Order.class)
		.getResultList();
```

`LAZY` 전략은 지연 로딩이므로 데이터베이스에서 회원만 조회된다. 따라서 다음 SQL만 실행되고 연관된 주문 컬렉션은 지연 로딩된다.

```
select * from Order // JPQL로 실행된 SQL
```

이후 비즈니스 로직에서 주문 컬렉션을 실제 사용할 때 지연 로딩이 발생한다.

```java
firstMember = members.get(0);
firstMember.getOrders().size(); // 지연 로딩 초기화
```

`members.get(0)`로 회원 하나만 조회해서 사용했기 때문에 `fisetMember.getOrders().size()`를 호출하면서 실행되는 SQL은 다음과 같다.

```
SELECT * FROM ORDER WHERE MEMBER_ID=?
```

문제는 다음처럼 모든 회원에 대해 연관된 주문 컬렉션을 사용할 때 발생한다.

```java
for(Member member : members) {
		// 지연 로딩 초기화
		System.out.println("member = " + member.getOrders().size();
}
```

주문 컬렉션을 초기화하는 수만큼 다음 SQL이 실행될 수 있다. 회원이 5명이면 회원에 따른 주몬도 5번 조회된다.

```
SELECT * FROM ORDER WHERE MEMBER_ID=1
SELECT * FROM ORDER WHERE MEMBER_ID=2
SELECT * FROM ORDER WHERE MEMBER_ID=3
SELECT * FROM ORDER WHERE MEMBER_ID=4
SELECT * FROM ORDER WHERE MEMBER_ID=5
```

### Hibernate에서 N+1 문제를 피하는 방법은 다음의 방법을 고려해볼 수 있다.

- 페치 조인 사용
- 하이버네이트 `@BatchSize`
- 하이버네이트 `@Fetch(FetchMode.SUBSELECT)`

## 참조

<김영한, 자바 ORM 표준 JPA 프로그래밍, 에이콘>

[https://docs.jboss.org/hibernate/orm/5.6/userguide/html_single/Hibernate_User_Guide.html#best-practices-fetching](https://docs.jboss.org/hibernate/orm/5.6/userguide/html_single/Hibernate_User_Guide.html#best-practices-fetching)
