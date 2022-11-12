# 쿼리 결과 처리하기(Result handling)

Querydsl은 쿼리 결과를 커스텀하여 반환하는 두 가지 방식을 제공합니다. 하나는 행 기반의 결과를 위한 `FactoryExpressions`이고 다른 하나는 집계 기반의 결과를 위한 `ResultTransformer`입니다.

## 행 기반의 결과 다루기

`com.querydsl.core.types.FactoryExpression` 인터페이스는 Bean 생성, 생성자 호출 및 더 복잡한 객체 생성에 사용됩니다. Querydsl의 `FactoryExpression` 인터페이스의 구현 기능은 `com.querydsl.core.types.Projections` 클래스를 통해 사용할 수 있습니다.

### 다중 칼럼 반환하기(Returning multiple columns)

Querydsl(3.0 이상)에서 제공하는 다중 칼럼 결과의 기본 유형은 `com.querydsl.core.Tuple`입니다. `Tuple`은 `Tuple` 행 개체에서 열 데이터에 접근하기 위한 인터페이스와 같은 형식 안전 맵을 제공합니다.

```java
List<Tuple> result = query.select(employee.firstName, employee.lastName)
                          .from(employee).fetch();
for (Tuple row : result) {
     System.out.println("firstName " + row.get(employee.firstName));
     System.out.println("lastName " + row.get(employee.lastName));
}}
```

`selectFrom` 으로 조회할 경우, `Tuple` 이 아닌 도메인 엔터티를 반환합니다.

```java
Order order = query.selectFrom(QOrder.order)
                .join(QOrder.order.orderLines, orderLine).fetchJoin()
                .where(QOrder.order.id.eq(orderId)
                        .and(QOrder.order.userId.eq(userId)))
                .fetchOne();
```

### 단일 칼럼 반환하기

칼럼 하나를 반환할 경우, 반환 타입을 명확하게 지정할 수 있습니다. 아래 예제를 보면, `Tuple`이 아닌 `String` 타입을 명시한 것을 확인할 수 있습니다.

```java
List<String> result = queryFactory
            .select(member.username)
            .from(member)
            .fetch();
```

### 빈을 이용한 반환(Bean population)

빈 프로젝션(Bean projections)을 이용하여 빈들에 쿼리 결과를 담을 수도 있습니다. 

`Projections.bean`는 Bean의 setter 메소드를 이용해 프로퍼티에 값을 주입합니다. 기본 생성자와 setter 메소드가 미리 구현되어 있어야 합니다.

```java
List<UserDTO> dtos = query.select(
    Projections.bean(UserDTO.class, user.firstName, user.lastName)).fetch();
```

`Projections.fields`는 setter 메소드를 사용하기 않고 필드에 직접 접근하는 방식입니다. 따라서 값을 주입하려는 Bean에 setter메소드를 구현하지 않아도 됩니다.

```java
List<UserDTO> dtos = query.select(
    Projections.fields(UserDTO.class, user.firstName, user.lastName)).fetch();
```

### Cf. 칼럼명과 필드명이 일치하지 않을 경우

빈을 생성하여 결과를 반환할 때, Bean의 필드명 혹은 setter 메서드의 이름과 엔티티의 칼럼명이 일치하지 않을 경우 값이 제대로 채워지지 않습니다. 이때는 별칭`as`을 이용해 이름을 맞춰 주어야 합니다.

```java
List<UserDto> fetch = queryFactory
    .select(Projections.fields(UserDto.class,
            member.username.as("name"),
            ExpressionUtils.as(
     )
		).from(member)
		.fetch();
```

위의 예시에서 칼럼 `username`의 값을 반환하는 Bean의 필드명이 `name`일 경우, 다음과 같이 `as("name")` 별칭을 이용해 이름을 맞추어 줍니다.

서브 쿼리에 별칭 적용할 경우 `ExpressionUtils.as(source,alias)`을 사용합니다.

```java
List<UserDto> fetch = queryFactory
    .select(Projections.fields(UserDto.class,
            member.username.as("name"),
            ExpressionUtils.as(
     )
		).from(member)
		.fetch();
```

### 생성자를 이용한 변환(Constructor usage)

`Projections.constructor`는 생성자를 호출하여 결과를 반환하는 방법입니다.

```java
List<UserDTO> dtos = query.select(
    Projections.constructor(UserDTO.class, user.firstName, user.lastName)).fetch();
```

`@QueryProjection`

제네릭 생성자 표현의 대안으로 생성자에 `QueryProjection` 어노테이션을 추가할 수도 있습니다.

```java
class CustomerDTO {

	  @QueryProjection
	  public CustomerDTO(long id, String name) {
	     ...
	  }
}
```

위와 같이 어노테이션을 적용한 후, compileQuerydsl 빌드(gradle) 테스크를 실행하면 `QCustomerDTO`가 생성됩니다. 그 다음 생성된 Q타입 객체를 다음과 같이 사용할 수 있습니다.

```java
QCustomer customer = QCustomer.customer;
JPQLQuery query = new HibernateQuery(session);
List<CustomerDTO> dtos = query.select(new QCustomerDTO(customer.id, customer.name))
                              .from(customer).fetch();
```

이 방식의 장점은 제네릭과 같이 타입  안정성을 제공한다는 점입니다. 따라서 컴파일 타임에 타입 체크를 할 수 있습니다. 단점으로는 순수한 DTO 객체에 Querydsl의 라이브러리 의존성이 추가되며 QType을 생성해주어야 한다는 점입니다. 이 장단점을 참고하여 `@QueryProjection`을 사용 여부를 결정하시면 될 것 같습니다.

## 집계 기반의 결과 다루기(Result aggregation)

`com.querydsl.core.ResultTransformer` 인터페이스의 경우, `GroupBy`가 주요 구현입니다. `com.querydsl.core.group.GroupBy` 클래스는 메모리에서 쿼리 결과를 집계할 수 있는 집계 기능을 제공합니다.

### 부모 - 자식 관계의 집계 결과 반환

```java
import static com.querydsl.core.group.GroupBy.*;

Map<Integer, List<Comment>> results = query.from(post, comment)
    .where(comment.post.id.eq(post.id))
    .transform(groupBy(post.id).as(list(comment)));
```

위 예시는 관련 댓글에 대한 게시물 ID 맵이 반환됩니다.

### 다중 칼럼 반환

```java
Map<Integer, Group> results = query.from(post, comment)
    .where(comment.post.id.eq(post.id))
    .transform(groupBy(post.id).as(post.name, set(comment.id)));
```

위 예시는 게시물 이름과 댓글 ID에 접근할 수 있는 `Group` 인스턴스에 대한 게시물 ID 맵이 반환됩니다. `Group`은 `Tuple` 인터페이스에 해당하는 GroupBy입니다.

---

**참조**

[http://querydsl.com/static/querydsl/4.4.0/reference/html_single/#result_handling](http://querydsl.com/static/querydsl/4.4.0/reference/html_single/#result_handling)

<김영한, (강의)실전! Querydsl, 인프런>
