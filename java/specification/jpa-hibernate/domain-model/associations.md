# Associations

연관(Associations)은 **둘 이상의 엔티티들이 데이터베이스의 조인 문법을 기반으로 어떻게 관계를 만드는 지를 표현**(describe)합니다.

---

### 목차

- `@ManyToOne`
- `@OneToMany`
    - 단방향(unidirectional) **`@OneToMany`**
    - 객체 연관관계와 테이블 연관관계의 차이
    - 양방향(Bidirectional) **`@OneToMany`**
- `@OneToOne`
    - 단방향 `@OneToOne`
    - 양방향 `@OneToOne`
- `@ManyToMany`

## `@ManyToOne`

`@ManyToOne`은 가장 일반적인 연관(Association)입니다. 관계형 데이터베이스(e.g. 외래키)에도 직접 상응(equivalent)하며, **자식 엔터티와 부모 엔티티 간의 관계를 설정**합니다.

```java
@Entity(name = "Person")
public static class Person {

	@Id
	@GeneratedValue
	private Long id;

	//Getters and setters are omitted for brevity

}

@Entity(name = "Phone")
public static class Phone {

	@Id
	@GeneratedValue
	private Long id;

	@Column(name = "`number`")
	private String number;

	@ManyToOne
	@JoinColumn(name = "person_id",
			foreignKey = @ForeignKey(name = "PERSON_ID_FK")
	)
	private Person person;

	//Getters and setters are omitted for brevity

}
```

```sql
CREATE TABLE Person (
    id BIGINT NOT NULL ,
    PRIMARY KEY ( id )
)

CREATE TABLE Phone (
    id BIGINT NOT NULL ,
    number VARCHAR(255) ,
    person_id BIGINT ,
    PRIMARY KEY ( id )
 )

ALTER TABLE Phone
ADD CONSTRAINT PERSON_ID_FK
FOREIGN KEY (person_id) REFERENCES Person
```

- `ManyToOne` 연관이 설정되면, Hibernate는 데이터베이스의 연관된 컬럼에 외래 키를 설정합니다.
- `@JoinColumn`을 이용하면 외래 키에 대한 설정을 할 수 있습니다(사용하지 않으면 기본 값으로 자동 설정됩니다).
    - `name` : 외래 키의 이름을 지정할 수 있습니다. 기본 값은 ‘필드명+_+참조하는 테이블의 기본 키 컬럼명’입니다.
    - `referencedColumnName` : 외래 키가 참조하는 대상 테이블의 칼럼명
    - `foreignKey`(DDL) : 외래 키 제약조건을 직접 지정할 수 있습니다.
    - `unique`, `nullable`, `insertable`, `updatable`, `columnDefinition`, `table`은 `@Column` 어노테이션의 속성과 같습니다.

## `@OneToMany`

`@OneToMany`은 **부모 엔터티 하나를 하나 이상의 자식 엔터티와 연결**합니다. 만약 `@OneToMany`의 자식 측에 미러링 `@ManyToOne` 연관이 없는 경우, `@OneToMany` 연결(Association)은 단방향(unidirectional)입니다. 자식 측에 미러링 `@ManyToOne` 연관이 있는 경우, `@OneToMany` 연결은 양방향이며 애플리케이션 개발자는 양쪽 끝에서 이 관계를 탐색할 수 있습니다.

### 단방향(unidirectional) `@OneToMany`

단방향 `@OneToMany` 연관을 사용할 때, Hibernate는 두 조인 엔티티 사이에 링크 테이블을 사용합니다.

```java
@Entity(name = "Person")
public static class Person {

	@Id
	@GeneratedValue
	private Long id;

	@OneToMany(cascade = CascadeType.ALL, orphanRemoval = true)
	private List<Phone> phones = new ArrayList<>();

	//Getters and setters are omitted for brevity

}

@Entity(name = "Phone")
public static class Phone {

	@Id
	@GeneratedValue
	private Long id;

	@Column(name = "`number`")
	private String number;

	//Getters and setters are omitted for brevity

}
```

```sql
CREATE TABLE Person (
    id BIGINT NOT NULL ,
    PRIMARY KEY ( id )
)

CREATE TABLE Person_Phone (
    Person_id BIGINT NOT NULL ,
    phones_id BIGINT NOT NULL
)

CREATE TABLE Phone (
    id BIGINT NOT NULL ,
    number VARCHAR(255) ,
    PRIMARY KEY ( id )
)

ALTER TABLE Person_Phone
ADD CONSTRAINT UK_9uhc5itwc9h5gcng944pcaslf
UNIQUE (phones_id)

ALTER TABLE Person_Phone
ADD CONSTRAINT FKr38us2n8g5p9rj0b494sd3391
FOREIGN KEY (phones_id) REFERENCES Phone

ALTER TABLE Person_Phone
ADD CONSTRAINT FK2ex4e4p7w1cj310kg2woisjl2
FOREIGN KEY (Person_id) REFERENCES Person
```

- 단방향 `@OneToMany` 연관은 두 엔터티 사이에 링크 테이블을 사용하기 때문에, 데이터베이스의 관점에서 좋은 설계로 볼 수 없습니다. 따라서 링크 테이블을 생성하지 않는 양방향 `@OneToMany` 연관을 사용하는 것이 데이터베이스 관리에 더 유리해 보입니다.
- 위의 `@OneToMany` 속성에 사용된 `cascade = CascadeType.ALL` 와 `orphanRemoval = true` 에 대한 설명은 [영속성 전이](https://github.com/eastshine-high/til/blob/main/java/specification/jpa-hibernate/persistence-context/cascading-entity-state-transitions.md), [고아 객체 제거](https://github.com/eastshine-high/til/blob/main/java/specification/jpa-hibernate/persistence-context/cascading-entity-state-transitions.md#%EA%B3%A0%EC%95%84-%EA%B0%9D%EC%B2%B4-%EC%A0%9C%EA%B1%B0orphanremoval)에서 정리하였습니다.

이제 양방향(Bidirectional) `@OneToMany` 에 대해 알아 보겠습니다. 이를 이해하기 위해, 먼저 객체 연관관계와 테이블 연관관계의 차이에 대해 이해할 필요가 있습니다.

### 객체 연관관계와 테이블 연관관계의 차이

**객체는 참조(주소)를 사용해서 관계를 맺고 테이블은 외래 키를 사용해서 관계를 맺습니다.**

**참조를 통한 연관관계는 언제나 단방향입니다.** 객체간에 연관관계를 양방향으로 만들고 싶으면 반대쪽에도 필드를 추가해서 참조를 보관해야 합니다. 결국 연관관계를 하나 더 만들어야 합니다. 이렇게 양쪽에서 서로 참조하는 것을 양방향 연관관계라 합니다. 하지만 정확히 이야기하면 이것은 **양방향 관계가 아니라 서로 다른 단향향 관계 2개입니다**. 즉 객체에서의 방향(Direction)은 객체의 참조로 볼 수 있습니다.

다음은 객체의 단방향 연관관계입니다.

```java
class A {
	B b;
}
class B {}
```

다음은 객체의 양방향 연관관계입니다.

```java
class A {
	B b;
}
class B {
	A a;
}
```

반면에 테이블은 외래 키로 연관관계를 맺습니다. 테이블은 외래 키 하나로 양방향으로 조인할 수 있습니다. 이 둘은 비슷해 보이지만 매우 다른 특징을 가집니다. 연관된 데이터를 조회할 때 객체는 참조(`a.getB()`)를 사용하지만 테이블은 조인(`JOIN`)을 사용합니다.

참조를 사용하는 객체의 연관관계는 단방향입니다.

- `A → B (a.b)`

외래 키를 사용하는 테이블의 연관관계는 양방향입니다.

- `A JOIN B`가 가능하면 반대로 `B JOIN A`도 가능합니다.

객체를 양방향으로 참조하려면 단방향 연관관계를 2개 만들어야 합니다.

- `A → B(a.b)`
- `B → A(b.a)`

### 양방향(Bidirectional) `@OneToMany`

양방향 `@OneToMany` 연관은 자식 측에서도 `@ManyToOne` 연관이 필요합니다. 도메인 모델은 이 연관을 탐색(navigate)하기 위해 양 측면을 노출하지만, 배후인 관계형 데이터베이스는 이 관계를 위해 하나의 외래키만 가집니다.

모든 양방향 연관에는 하나의 소유(owning)측(자식 측)만 있어야 하고 다른 쪽은 역(inverse)측(또는 `mappedBy`이라고 한다(`mappedBy`는 주인에 의해 매핑되어 있다는 의미로 해석해 볼 수 있습니다). 

**왜 소유(owning)측이 있어야 할까?**

객체는 사실상 참조를 통한 단방향 연관이기 때문에 양쪽 모두에서 값의 변화가 생긴다면 데이터의 일관성(Consistency)이 깨집니다. 따라서 한 쪽의 변화만을 인정해야 합니다. 데이터의 관리 주체가 양쪽 모두가 될 수 없기 때문입니다. 이것이 소유(owning)측이 있어야 하는 이유입니다.

**누구를 소유(owning)측으로 할까?**

외래키가 있는 쪽(manyToOne)을 소유 측으로 합니다. 데이터 변경의 관점 등 여러 면에서 **자식 엔터티를 소유 측으로 지정하는 것이 유리**합니다.

**소유 관계의 특징**

연관관계의 주인만이 외래 키를 관리(등록, 수정)할 수 있습니다. 주인이 아닌 쪽은 읽기만 가능(수정을 해도 반영되지 않음)합니다.

```java
@Entity(name = "Person")
public static class Person {

	@Id
	@GeneratedValue
	private Long id;

	@OneToMany(mappedBy = "person", cascade = CascadeType.ALL, orphanRemoval = true)
	private List<Phone> phones = new ArrayList<>();

	//Getters and setters are omitted for brevity

	public void addPhone(Phone phone) {
		phones.add( phone );
		phone.setPerson( this );
	}

	public void removePhone(Phone phone) {
		phones.remove( phone );
		phone.setPerson( null );
	}
}

@Entity(name = "Phone")
public static class Phone {

	@Id
	@GeneratedValue
	private Long id;

	@NaturalId
	@Column(name = "`number`", unique = true)
	private String number;

	@ManyToOne
	private Person person;

	//Getters and setters are omitted for brevity

	@Override
	public boolean equals(Object o) {
		if ( this == o ) {
			return true;
		}
		if ( o == null || getClass() != o.getClass() ) {
			return false;
		}
		Phone phone = (Phone) o;
		return Objects.equals( number, phone.number );
	}

	@Override
	public int hashCode() {
		return Objects.hash( number );
	}
}
```

```sql
CREATE TABLE Person (
    id BIGINT NOT NULL ,
    PRIMARY KEY ( id )
)

CREATE TABLE Phone (
    id BIGINT NOT NULL ,
    number VARCHAR(255) ,
    person_id BIGINT ,
    PRIMARY KEY ( id )
)

ALTER TABLE Phone
ADD CONSTRAINT UK_l329ab0g4c1t78onljnxmbnp6
UNIQUE (number)

ALTER TABLE Phone
ADD CONSTRAINT FKmw13yfsjypiiq0i1osdkaeqpg
FOREIGN KEY (person_id) REFERENCES Person
```

- 양방향 연결에서 개발자는 항상 양쪽이 동기화되어 있는지 확인해야 합니다. `addPhone()` 과 `removePhone()`은 자식 요소가 추가되거나 제거될 때마다 양쪽을 동기화하는 유틸리티 메서드입니다.
- 단방향 `@OneToMany`와 달리 양방향 연결은 컬렉션의 영속 상태를 관리할 때 효율적입니다. 모든 엘러먼트 제거에는 단일 업데이트(외래 키 열이 `NULL`로 설정됨)만 필요합니다. 자식 엔터티 생명 주기가 이를 소유하고 있는 부모에 한정적이어서 자식 엔터티가 부모 없이는 존재할 수 없는 경우, 관계 어노테이션에 `orphanRemoval` 속성을 사용하고 자식을 분리하면 실제 자식 테이블 행에서도 삭제 문을 트리거합니다.(Cascade 참고).

## `@OneToOne`

`@OneToOne` 연관은 단방향 또는 양방향일 수 있습니다.

### 단방향 `@OneToOne`

- `@ManyToOne` 연관과 유사합니다.
- 단방향 연관은 관계형 데이터베이스의 외래키 의미 체계를 따르며 추가적으로 외래키에 유니크(UNI) 제약조건이 추가됩니다.

### 양방향 `@OneToOne`

- **외래 키가 있는 곳을 연관관계의 주인**으로 설정합니다(반대편은 `mappedBy`를 적용).
- 연관관계를 어느 쪽(부모, 자식)에서 소유하느냐에 따라서 장점과 단점이 다릅니다.

**부모 측에서 소유할 경우**

- 주 객체가 대상 객체의 참조를 가지는 것 처럼 **주 테이블에 외래 키를 두고** 대상 테이블을 찾습니다.
- 객체지향 개발자 선호합니다.
- JPA 매핑 편리합니다.
- 장점: 주 테이블만 조회해도 대상 테이블에 데이터가 있는지 확인 가능합니다.
- 단점: 값이 없으면 외래 키에 `null` 허용합니다.

![https://velog.velcdn.com/images/eastshine-high/post/5adc1937-7ce1-4ddc-b3af-2a4faf9b3a61/image.png](https://velog.velcdn.com/images/eastshine-high/post/5adc1937-7ce1-4ddc-b3af-2a4faf9b3a61/image.png)

![https://velog.velcdn.com/images/eastshine-high/post/9fd37c0b-727a-480b-b4ca-de208a144912/image.png](https://velog.velcdn.com/images/eastshine-high/post/9fd37c0b-727a-480b-b4ca-de208a144912/image.png)

> 위의 그림에서 `Member`가 부모, `Locker`가 자식입니다
> 

**자식 측에서 소유할 경우**

- 자식 테이블에 외래 키가 존재합니다.
- 전통적인 데이터베이스 개발자 선호.
- 장점: 부모 테이블과 자식 테이블을 일대일에서 일대다 관계로 변경할 때 테이블 구조 유지할 수 있습니다.
- 단점: 프록시 기능의 한계로 지연 로딩으로 설정해도 항상 즉시 로딩됩니다.

## `@ManyToMany`

관계형 데이터베이스는 정규화된 테이블 2개로 다대다(ManyToMany) 관계를 표현할 수 없습니다. 따라서 연결 테이블을 추가해서 일대다, 다대일 관계로 풀어내야합니다.

![http://dl.dropbox.com/s/4j4647swnaf53rt/many-to-many-relation.png](http://dl.dropbox.com/s/4j4647swnaf53rt/many-to-many-relation.png)

객체는 컬렉션을 사용하여 객체 2개의 다대다(ManyToMany) 연관 관계가 가능합니다(단방향, 양방향 연관 관계 모두 가능). 물론 관계형 데이터베이스에서는 이 연관 관계를 아래와 같이 연결 테이블을 추가하여 표현합니다.

![http://dl.dropbox.com/s/p52a21br30ykuoh/many-to-many-object.png](http://dl.dropbox.com/s/p52a21br30ykuoh/many-to-many-object.png)

편리해 보이지만 실무에서는 잘 사용하지 않습니다. 왜냐하면 연결 테이블이 단순히 연결만 하고 끝나지 않기 때문입니다. 예를 들어 주문시간, 수량과 같은 데이터가 추가로 들어올 수 있습니다.

![http://dl.dropbox.com/s/owsti771ulhnddj/many-to-many-solution.png](http://dl.dropbox.com/s/owsti771ulhnddj/many-to-many-solution.png)

따라서 `@manyToMany` 를 바로 사용하지 않고 `@OneToMany` 와 `@ManyToOne` 을 활용하여 풀어서 사용합니다.

---

**참조**

[https://docs.jboss.org/hibernate/orm/5.6/userguide/html_single/Hibernate_User_Guide.html#associations](https://docs.jboss.org/hibernate/orm/5.6/userguide/html_single/Hibernate_User_Guide.html#associations)

<김영한, 자바 ORM 표준 JPA 프로그래밍 - 기본편, 인프런>
