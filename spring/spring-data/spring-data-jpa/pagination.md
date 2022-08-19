# Pagination

데이터베이스에서 조회를 할 때, 조건에 해당하는 데이터를 모두 조회할 경우, 서비스에 과부화가 생길 수 있습니다. 페이징을 사용하면 이러한 서비스 과부화를 방지할 수 있습니다. Spring Data JPA의 페이징 기능에 대해서 살펴봅니다.

- 쿼리 메소드 - Pagination
- Web 확장 - Pagination

## 쿼리 메소드 - Pagination

### 특별한 파라미터

쿼리 메소드는 `Pageable` 과 `Sort` 같은 특수한 타입의 **파라미터**를 지원하여 페이징 또는 정렬을 지원합니다.

- `org.springframework.data.domain.Sort` : 정렬 기능
- `org.springframework.data.domain.Pageable` : 페이징 기능 (내부에 `Sort`를 포함)

### 특별한 반환 타입

또한 쿼리 메소드는 `Page` 와 `Slice` 같은 특별한 **반환 타입**을 지원합니다. 반환 타입에 따라서 조회 쿼리 이후에 추가로 `COUNT` 쿼리를 수행할지 여부를 결정합니다.

`org.springframework.data.domain.Slice`

```java
public interface Slice<T> extends Streamable<T> {
	  int getNumber(); //현재 페이지
		int getSize(); //페이지 크기
		int getNumberOfElements(); // 현재 페이지에 나올 데이터 수
		List<T> getContent(); // 조회된 데이터
		boolean hasContent(); // 조회된 데이터 존재 여부
		Sort getSort(); // 정렬 정보
		boolean isFirst(); // 현재 페이지가 첫 페이지 인지 여부
		boolean isLast(); // 현재 페이지가 마지막 페이지 인지 여부
		boolean hasNext(); // 다음 페이지 여부
		boolean hasPrevious(); // 이전 페이지 여부
		Pageable getPageable(); // 페이지 요청 정보
		Pageable nextPageable(); // 다음 페이지 객체
		Pageable previousPageable(); // 이전 페이지 객체
		<U> Slice<U> map(Function<? super T, ? extends U> converter); //변환기
}
```

- 조회 쿼리 이후에 추가 `COUNT` 쿼리를 수행하지 않습니다.
- 주로 모바일 앱의 화면에서 자주 보는 더보기 용으로 사용합니다.
- 내부적으로 `LIMIT` + 1을 조회합니다. 이는 더 조회할 데이터가 있는지 확인하기 위함입니다.

`org.springframework.data.domain.Page`

```java
public interface Page<T> extends Slice<T> {
		int getTotalPages(); //전체 페이지 수
		long getTotalElements(); //전체 데이터 수
		<U> Page<U> map(Function<? super T, ? extends U> converter); //변환기
}
```

- `Page`는 `Slice` 를 상속하는 인터페이스입니다.
- 조회 쿼리 이후에 추가 `COUNT` 쿼리를 수행한 결과를 포함하는 반환 타입입니다(추가 `COUNT` 쿼리를 사용하는 이유는 전체 페이지 수와 데이터 수를  반환하기 위함입니다).
- 주로 일반 페이징 용으로 사용합니다.

### 페이징과 정렬 사용하기

Page 사용 예제 정의 코드

```java
public interface MemberRepository extends Repository<Member, Long> {
		Page<Member> findByUsername(String name, Pageable pageable); //count 쿼리 사용 

		Slice<Member> findByUsername(String name, Pageable pageable); //count 쿼리 사용 안함
		// slice의 경우 limit + 1을 조회한다. 왜냐면 더보기를 노출할 지 말지를 결정하기 때문.

		Page<Member> findByAge(int age, Pageable pageable);

		List<Member> findByUsername(String name, Sort sort);
}
```

Page 사용 예제 실행 코드

```java
//페이징 조건과 정렬 조건 설정
@Test
public void page() throws Exception {
		//given
    memberRepository.save(new Member("member1", 10));
    memberRepository.save(new Member("member2", 10));
    memberRepository.save(new Member("member3", 10));
    memberRepository.save(new Member("member4", 10));
    memberRepository.save(new Member("member5", 10));

		//when
    PageRequest pageRequest = PageRequest.of(0, 3, Sort.by(Sort.Direction.DESC, "username"));
    Page<Member> page = memberRepository.findByAge(10, pageRequest);

		//then
		List<Member> content = page.getContent(); //조회된 데이터
		assertThat(content.size()).isEqualTo(3); //조회된 데이터 수
		assertThat(page.getTotalElements()).isEqualTo(5); //전체 데이터 수 
		assertThat(page.getNumber()).isEqualTo(0); //페이지 번호
		assertThat(page.getTotalPages()).isEqualTo(2); //전체 페이지 번호 
		assertThat(page.isFirst()).isTrue(); //첫번째 항목인가?
		assertThat(page.hasNext()).isTrue(); //다음 페이지가 있는가?
}
```

- `Pagable`은 인터페이스고 실제 사용할 때는 `PageRequest` 객체를 사용합니다.
- API는 `Sort`와 `Pageable` 파라미터의 값에 대해서 `null`이 아닌 값이 전달되어야 합니다. 따라서 만약 정렬과 페이징을 원하지 않는 경우, `Sort.unsorted()`, `Pageable.unpaged()`을 사용합니다.

다음은 페이지를 유지하면서 엔티티를 DTO로 변환하는 예시입니다.

```java
Page<Member> page = memberRepository.findByAge(10, pageRequest);
Page<MemberDto> dtoPage = page.map(m -> new MemberDto());
```

### `COUNT` 쿼리를 분리

예를 들어 `LEFT OUTER JOIN`을 이용한 `COUNT` 쿼리와 `LEFT OUTER JOIN`을 사용하지 않은 `COUNT` 쿼리의 수행 결과가 같습니다. 이 경우에는 조회 쿼리와 `COUNT` 쿼리를 분리하여 쿼리 수행 속도를 향상시킬 수 있습니다.

```java
@Query(value = “select m from Member m left join m.team t”,
         countQuery = “select count(m.username) from Member m”)
Page<Member> findMemberAllCountBy(Pageable pageable);
```

## Web 확장 - Pagination

Spring Data는 [HandlerMethodArgumentResolver](https://github.com/eastshine-high/til/blob/main/spring/spring-framework/web-servlet/spring-mvc/dispatcher-servlet/special-bean-types/handler-adapter.md#handlermethodargumentresolverargumentresolver)를 구현하여 Spring MVC의 메소드 파라미터에서 `Pageable` 과 `Sort` 를 사용할 수 있도록 합니다.

```java
@RestController
public class ProductController {

		@GetMapping("/products")
    public Page<ProductDto> getProducts(
            @RequestParam String name,
            Pageable pageable
    ) {
        return productRepository.findByNameContaining(String name, Pageable pageable);
    }
}
```

`Pageable` 인스턴스를 위해 다음과 같은 요청 파라미터들를 사용할 수 있습니다.

| | |
| --- | --- |
| page | 조회할 페이지. 기본 값은 0입니다. |
| size | 한 페이지에 노출할 데이터의 건수. 기본 값은 20입니다. |
| sort | 다음의 형식으로 정렬 조건을 정의합니다. property,property(,ASC or DESC)(,IgnoreCase). 기본 정렬 방향은 대소문자 구분, 오름차순입니다. 정렬 방향이 다를 경우 sort 파라미터를 여러번 사용할 수 있습니다. 예를 들어 다음과 같이 요청할 수 있습니다. `?sort=firstname&sort=lastname,asc&sort=city,ignorecase`. `asc`는 생략 가능합니다. |

- 요청 파라미터 사용 예) `/products?name=hi&page=0&size=3&sort=id,desc&sort=username,desc`

- Pageable 은 인터페이스, 실제는 `org.springframework.data.domain.PageRequest` 객체 생성합니다.

### 요청 파라미터들의 기본값 설정

요청 파라미터들의 기본값은 글로벌 설정 혹은 개별 설정을 통해 조정할 수 있습니다.

스프링 부트의 글로벌 설정 - `application.property` 

```
spring.data.web.pageable.default-page-size=20 /# 기본 페이지 사이즈/ 
spring.data.web.pageable.max-page-size=2000 /# 최대 페이지 사이즈/
```

개별 설정 - `@PageableDefault` 어노테이션을 사용합니다.

```java
@RequestMapping(value = "/members_page", method = RequestMethod.GET)
	  public String list(@PageableDefault(size = 12, sort = “username”,
													direction = Sort.Direction.DESC) Pageable pageable) {
	  ... 
}
```

### 접두사

페이징 정보가 둘 이상이면 접두사로 구분할 수 있습니다.

```java
public String list(
      @Qualifier("member") Pageable memberPageable,
      @Qualifier("order") Pageable orderPageable, ...
```

- `@Qualifier` 에 접두사명 추가합니다.
- URL의 쿼리 파라미터에서는  `{접두사명}_속성` 형식으로 요청합니다.
    - 예시) `/members?member_page=0&order_page=1`

---

**참조**

[https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.special-parameters](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.special-parameters)

[https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#core.web.basic.paging-and-sorting](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#core.web.basic.paging-and-sorting)

<김영한, (강의) 실전! 스프링 데이터 JPA, 인프런>
