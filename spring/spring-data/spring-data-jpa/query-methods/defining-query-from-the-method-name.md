# 메소드 이름으로 쿼리 정의하기

쿼리 메소드 기능은 스프링 데이터 JPA가 제공하는 마법 같은 기능입니다. 대표적으로 메소드 이름만으로 쿼리를 생성하는 기능이 있는데 인터페이스에 메소드만 선언하면 해당 메소드의 이름으로 적절한 JPQL 쿼리를 생성해서 실행합니다.

이메일과 이름으로 회원을 조회하려면 다음과 같은 메소드를 정의하면 됩니다.

```java
public interface MemberRepository extends JpaRepository<Member, Long>{
    List<Member> findByEmailAndName(String email, String name);
}
```

인터페이스에 정의한 `findByEmailAndName` 메소드를 실행하면 스프링 데이터 JPA는 메소드 이름을 분석해서 JPQL을 생성하고 실행합니다. 물론 정해진 규칙에 따라서 메소드 이름을 지어야 합니다.

다음 예시는 다양한 쿼리들을 어떻게 만드는 지를 보여줍니다.

```java
interface PersonRepository extends Repository<Person, Long> {

  List<Person> findByEmailAddressAndLastname(EmailAddress emailAddress, String lastname);

  // Enables the distinct flag for the query
  List<Person> findDistinctPeopleByLastnameOrFirstname(String lastname, String firstname);
  List<Person> findPeopleDistinctByLastnameOrFirstname(String lastname, String firstname);

  // Enabling ignoring case for an individual property
  List<Person> findByLastnameIgnoreCase(String lastname);
  // Enabling ignoring case for all suitable properties
  List<Person> findByLastnameAndFirstnameAllIgnoreCase(String lastname, String firstname);

  // Enabling static ORDER BY for a query
  List<Person> findByLastnameOrderByFirstnameAsc(String lastname);
  List<Person> findByLastnameOrderByFirstnameDesc(String lastname);
}
```

먼저 쿼리 메소드 이름을 분석(파싱)하는 것은 주어부(subject)와 술어부(predicate)로 나뉩니다. 첫 번째 부분(`find…By`, `Existing…By`)은 쿼리의 주어를 정의하고, 주어부의 `By` 다음 부분이 술어를 만듭니다.

## 주어부(subject)

주어부(`xxx…By`)에는 추가 표현이 포함될 수 있습니다. `find`(또는 기타 도입 키워드)와 `By` 사이의 모든 텍스트는 생성할 쿼리에 고유 플래그를 설정하기 위한 `Distinct` 또는 쿼리 결과를 제한하기 위한 [Top/First와 같은 결과 제한 키워드](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.limit-query-result) 중 하나를 사용하지 않는 한 설명(식별하기 위한 내용, descriptive)으로 간주됩니다.

쿼리 주어부에는 다음의 키워드가 올 수 있습니다.

| Keyword | Description |
| --- | --- |
| find…By, read…By, get…By, query…By, search…By, stream…By | General query method returning typically the repository type, a Collection or Streamable subtype or a result wrapper such as Page, GeoResults or any other store-specific result wrapper. Can be used as findBy…, findMyDomainTypeBy… or in combination with additional keywords. |
| exists…By | Exists projection, returning typically a boolean result. |
| count…By | Count projection returning a numeric result. |
| delete…By, remove…By | Delete query method returning either no result (void) or the delete count. |
| …First<number>…, …Top<number>… | Limit the query results to the first <number> of results. This keyword can occur in any place of the subject between find (and the other keywords) and by. |
| …Distinct… | Use a distinct query to return only unique results. Consult the store-specific documentation whether that feature is supported. This keyword can occur in any place of the subject between find (and the other keywords) and by. |

## 술어부(predicate)

첫 번째 `By`는 술어부의 시작을 나타내는 구분 기호 역할을 합니다. 매우 기본적인 수준에서 엔터티 속성에 대한 조건들을 정의하고 이 조건들을 `And` 및 `Or`로 연결할 수 있습니다. 술어부를 작성하지 않으면 쿼리에 `WHERE` 절이 없이 검색이 됩니다.

메서드 구문 분석의 실제 결과는 쿼리를 만드는 영속성 저장소에 따라 다릅니다. 그러나 주의해야 할 몇 가지 일반적인 사항이 있습니다.

- 표현식은 일반적으로 연결할 수 있는 연산자들과 결합된 속성 순회입니다. 속성 표현식들은 `AND` 및 `OR`과 결합할 수 있습니다. 또한 속성 식에 대해 `Between`, `LessThan`, `GreaterThan` 및 `Like`와 같은 연산자들을 지원합니다. 지원되는 연산자들는 데이터 저장소에 따라 다를 수 있으므로 참조 문서의 해당 부분을 참조합니다.
- 메서드 파서는 개별 속성(예: `findByLastnameIgnoreCase(…)`)들 또는 대소문자 무시를 지원하는 유형의 모든 속성들(보통 `String` 인스턴스 - 예: `findByLastnameAndFirstnameAllIgnoreCase(…)`)에 대해 `IgnoreCase` 플래그 설정을 지원합니다. 대소문자 무시 지원 여부는 저장소 별로 상이할 수 있으므로 해당 저장소별 조회 방법은 참고 문서의 관련 섹션을 참고합니다.
- 속성을 참조하는 쿼리 메서드에 `OrderBy` 절을 추가하고 정렬 방향(`Asc` 또는 `Desc`)을 제공하여 정적 순서를 적용할 수 있습니다. 동적 정렬을 지원하는 쿼리 메소드를 생성하려면 "[Special parameter handling](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.special-parameters)"를 참조합니다.

쿼리 술어부에는 다음의 키워드가 올 수 있습니다.

| Keyword | Sample | JPQL snippet |
| --- | --- | --- |
| And | findByLastnameAndFirstname | … where x.lastname = ?1 and x.firstname = ?2 |
| Or | findByLastnameOrFirstname | … where x.lastname = ?1 or x.firstname = ?2 |
| Is,Equals | findByFirstname,findByFirstnameIs,findByFirstnameEquals | … where x.firstname = 1? |
| Between | findByStartDateBetween | … where x.startDate between 1? and ?2 |
| LessThan | findByAgeLessThan | … where x.age < ?1 |
| LessThanEqual | findByAgeLessThanEqual | … where x.age ⇐ ?1 |
| GreaterThan | findByAgeGreaterThan | … where x.age > ?1 |
| GreaterThanEqual | findByAgeGreaterThanEqual | … where x.age >= ?1 |
| After | findByStartDateAfter | … where x.startDate > ?1 |
| Before | findByStartDateBefore | … where x.startDate < ?1 |
| IsNull | findByAgeIsNull | … where x.age is null |
| IsNotNull,NotNull | findByAge(Is)NotNull | … where x.age not null |
| Like | findByFirstnameLike | … where x.firstname like ?1 |
| NotLike | findByFirstnameNotLike | … where x.firstname not like ?1 |
| StartingWith | findByFirstnameStartingWith | … where x.firstname like ?1 (parameter bound with appended %) |
| EndingWith | findByFirstnameEndingWith | … where x.firstname like ?1 (parameter bound with prepended %) |
| Containing | findByFirstnameContaining | … where x.firstname like ?1 (parameter bound wrapped in %) |
| OrderBy | findByAgeOrderByLastnameDesc | … where x.age = ?1 order by x.lastname desc |
| Not | findByLastnameNot | … where x.lastname <> ?1 |
| In | findByAgeIn(Collection<Age> ages) | … where x.age in ?1 |
| NotIn | findByAgeNotIn(Collection<Age> age) | … where x.age not in ?1 |
| True | findByActiveTrue() | … where x.active = true |
| False | findByActiveFalse() | … where x.active = false |
| IgnoreCase | findByFirstnameIgnoreCase | … where UPPER(x.firstame) = UPPER(?1) |

[https://docs.spring.io/spring-data/jpa/docs/1.8.0.RELEASE/reference/html/#jpa.query-methods.query-creation](https://docs.spring.io/spring-data/jpa/docs/1.8.0.RELEASE/reference/html/#jpa.query-methods.query-creation)

> 쿼리 메소드는 단순한 쿼리를 작성할 때 좋습니다. 조건 복잡하고 정적인 쿼리라면 `@Query` 어노테이션을 이용해서 리포지토리 인터페이스에 JPQL 쿼리를 작성하는 것이 좋습니다. 또한 조건이 복잡하고 유동적이라면 Querydsl을 사용하는 것이 좋습니다.
> 

## 반환 타입(Supported Query Return Types)

Spring Data JPA는 Hibernate보다 유연한 반환 타입을 지원합니다. 지원되는 반환 타입은 [링크](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#appendix.query.return.types)를 통해 확인할 수 있습니다.

```java
List<Member> findByUsername(String name); //컬렉션 
Member findByUsername(String name); //단건 
Optional<Member> findByUsername(String name); //단건 Optional
```

- 유일함(Unique)이 보장되지 않는다면 컬렉션 타입을 이용할 수 있습니다. 컬렉션은 데이터가 조회되지 않더라도 `null`을 반환하지 않는 장점이 있습니다.
- 조회하는 데이터가 유일(unique)하고 `null`이 아님이 보장된다면 단건 타입을 사용할 수 있니다.
- 조회하는 데이터가 유일(unique)하고 `null`일 가능성이 있다면 단건 Optional 타입을 사용할 수 있습니다.

---

**참조**

[https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.query-methods.details](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.query-methods.details)
