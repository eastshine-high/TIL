# 다양한 방법으로 쿼리 정의하기

앞선 페이지에서는 Spring Data JPA에서 메소드 이름으로 쿼리를 정의하는 방법에 대해 알아보았습니다. 이외에도 Spring Data JPA의 쿼리 메소드 기능은 다양한 쿼리를 생성 방법을 지원합니다.

## `@Query` 어노테이션을 이용하여 쿼리를 직접 정의

리포지토리 인터페이스에 `@Query` 어노테이션을 사용해서 쿼리를 직접 정의할 수 있습니다.

```java
public interface MemberRepository extends JpaRepository<Member, Long> {

    @Query("select m from Member m where m.username= :username and m.age = :age")
    List<Member> findUser(@Param("username") String username, @Param("age") int
    age);
}
```

- 실행할 메서드에 정적 쿼리를 직접 작성합니다.
- 이 방식은 애플리케이션 실행 시점에 문법 오류를 발견할 수 있습니다(장점).
- `@Param("age")` 어노테이션를 사용한 파라미터에 전달된 값은 쿼리의 `:age`에 바인딩 됩니다.

**위치 기반 파라미터 바이딩 vs 이름 기반 파라미터 바인딩**

> Spring Data JPA는 `?N`을 사용하여 위치를 기반으로 파라미터를 바인딩할 수 있습니다(아래 Native Queries 예시 참고). 이는 쿼리 메소드의 파라미터 위치를 리팩토링하였을 때, 오류가 발생하기 쉽습니다. 따라서 `@Param` 어노테이션과 `:`을 사용하여 파라미터의 구체적인 이름을 쿼리에 바인딩하는 것이 권장됩니다(위의 `@Query` 어노테이션 예시).
> 

### Native Queries

`@Query` 어노테이션은 `nativeQuery` 플래그를 `true`로 설정하여 네이티브 쿼리를 사용할 수 있습니다.

```java
public interface UserRepository extends JpaRepository<User, Long> {

  @Query(value = "SELECT * FROM USERS WHERE EMAIL_ADDRESS = ?1", nativeQuery = true)
  User findByEmailAddress(String emailAddress);
}
```

### DTO로 직접 조회

DTO로 직접 조회 하려면 JPA의 `new` 명령어를 사용합니다. 그리고 그에 맞는 생성자가 DTO에 필요합니다.

```java
@Query("select new study.datajpa.dto.MemberDto(m.id, m.username, t.name) " +
        "from Member m join m.team t")
List<MemberDto> findMemberDto();
```

- `new study.datajpa.dto.MemberDto` : 패키지명을 모두 적어주어야 합니다.

### 컬렉션 파라미터 바인딩

`IN`을 사용하는 쿼리의 파라미터에 바인딩을 할 때 주로 사용합니다.

```java
@Query("select m from Member m where m.username in :names")
List<Member> findByNames(@Param("names") List<String> names);

```

## `@EntityGraph`

`@EntityGraph`를 사용하면 JPQL의 `fetch` 조인을 편리하게 사용할 수 있습니다.

다음 `fetch` 를 사용한 JPQL 문장입니다.

```java
@Query("select m from Member m left join fetch m.team")
List<Member> findMemberFetchJoin();
```

위 문장을 `@EntityGraph`를 사용하여 간단한 문장으로 대치할 수 있습니다.

```java
@EntityGraph(attributePaths = {"team"})
List<Member> findEntityGraphBy();
```

다음은 `@EntityGraph` 를 사용한 여러 예시입니다.

```java
//공통 메서드 오버라이드
@Override
@EntityGraph(attributePaths = {"team"})
List<Member> findAll();

//JPQL + 엔티티 그래프 
@EntityGraph(attributePaths = {"team"}) 
@Query("select m from Member m") 
List<Member> findMemberEntityGraph();

//메서드 이름으로 쿼리에서 특히 편리하다. 
@EntityGraph(attributePaths = {"team"})
List<Member> findByUsername(String username)

// Overide를 원하지 않을 경우 find와 by 사이에 설명(식별하기 위한 내용, descriptive)을 추가할 수 있다.
@EntityGraph(attributePaths = {"category"})
Optional<Product> findEntityGraphById(Long id);
```

- `@EntityGraph` 는 자동으로 성능 최적화하여 객체 그래프를 한 번에 엮어서 가져옵니다.
- 간단한 `fetch` 조인에 사용하기 좋으며, 조금 복잡한 경우라면 JPQL을 작성해 `fetch` 조인하는 것이 좋습니다.

## `@Modifing`

### 벌크성 수정 쿼리

JPA에서 데이터를 변경할 때는 보통 [변경감지(Dirty checking)](https://github.com/eastshine-high/til/blob/main/java/specification/jpa-hibernate/persistence-context/persistent-data-status.md#%EC%97%94%ED%8B%B0%ED%8B%B0-%EC%88%98%EC%A0%95)를 이용하여 단건의 엔터티를 수정합니다. “모든 직원의 연봉을 10% 인상한다.” 처럼 여러 데이터를 한 번에 수정하는 쿼리를 **벌크성 수정 쿼리**라 합니다.

### JPA를 이용한 벌크성 수정 쿼리

```java
public int bulkAgePlus(int age) {
      int resultCount = em.createQuery(
              "update Member m set m.age = m.age + 1" +
                      "where m.age >= :age")
              .setParameter("age", age)
              .executeUpdate();
      return resultCount;
 }
```

- JPA에서는 `executeUpdate` 메소드를 이용해 벌크성 수정 쿼리를 수행합니다.

### Spring Data JPA에서의 벌크성 수정 쿼리

```java
@Modifying
@Query("update Member m set m.age = m.age + 1 where m.age >= :age")
int bulkAgePlus(@Param("age") int age);
```

- Spring Data JPA에서는 `@Modifing` 어노테이션을 이용해 벌크성 수정 쿼리를 수행할 수 있습니다.
- 벌크성 수정, 삭제 쿼리는 `@Modifying` 어노테이션을 사용하지 않으면 예외 발생가 발생합니다. `org.hibernate.hql.internal.QueryExecutionRequestException: Not supported for`
    
    `DML operations`
    
- 반환 타입은 `int` 형으로 정의합니다.

벌크성 쿼리는 영속성 컨텍스트를 무시하고 데이터베이스에 바로 접근하기 때문에 주의가 필요합니다.

```java
@Test
public void bulkUpdate() throws Exception {
		//given
    memberRepository.save(new Member("member1", 10));
    memberRepository.save(new Member("member2", 19));
    memberRepository.save(new Member("member3", 20));
    memberRepository.save(new Member("member4", 21));
   	memberRepository.save(new Member("member5", 40));

    //when
    int resultCount = memberRepository.bulkAgePlus(20);

		List<Member> members = memberRepository.findByUsername("member5");
		Member member5 = members.get(0);

		//then
    assertThat(resultCount).isEqualTo(3);
		assertThat(member5.getAge()).isEqualTo(40);
}
```

- 벌크 쿼리 `bulkAgePlus` 를 수행했지만 `member5`의 `age`는 여전히 40입니다.
- 같은 트랜잭션 내에서 벌크성 쿼리를 실행하고 다른 로직을 수행해야 한다면, 영속성 컨텍스트 초기화할 필요가 있습니다.
    - 다음과 같이 옵션을 사용합니다. `@Modifying(clearAutomatically = true)` (이 옵션의 기본값은 false)
    - 이 옵션 없이 회원을 `findById`로 다시 조회하면 영속성 컨텍스트에 과거 값이 남아서 문제가 될 수 있습니다. 만약 다시 조회해야 한다면 꼭 영속성 컨텍스트를 초기화합니다.
    - 혹은 JPA의 `EntityManager` 를 주입받아 `em.flush(); em.clear();` 를 수행합니다.

## Pagination

쿼리 메소드의 페이징 기능은 다른 페이지에서 정리하였으니 [링크](https://github.com/eastshine-high/til/blob/main/spring/spring-data/spring-data-jpa/pagination.md)를 참조해 주세요!

---

**참조**

[https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods)

<김영한, 자바 ORM 표준 JPA 프로그래밍, 에이콘>
