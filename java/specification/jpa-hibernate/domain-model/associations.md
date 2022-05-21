# Associations

연관(Associations)은 **둘 이상의 엔티티들이 데이터베이스의 조인 문법을 기반으로 어떻게 관계를 만드는 지를 표현**한다(describe).

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

## `@ManyToOne`

`@ManyToOne`은 가장 일반적인 연관(Association)이다. 관계형 데이터베이스(e.g. 외래키)에도 직접 상응(equivalent)하며, **자식 엔터티와 부모 엔티티 간의 관계를 설정**한다.

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

- `ManyToOne` 연관이 설정되면, Hibernate는 데이터베이스의 연관된 컬럼에 외래 키를 설정한다.
- `@JoinColumn`을 이용하면 외래 키에 대한 설정을 할 수 있다(사용하지 않으면 기본 값으로 자동 설정된다).
    - `name` : 외래 키의 이름을 지정할 수 있다. 기본 값은 ‘필드명+_+참조하는 테이블의 기본 키 컬럼명’이다.
    - `referencedColumnName` : 외래 키가 참조하는 대상 테이블의 칼럼명
    - `foreignKey`(DDL) : 외래 키 제약조건을 직접 지정할 수 있다.
    - `unique`, `nullable`, `insertable`, `updatable`, `columnDefinition`, `table`은 `@Column`의 속성과 같다.

## `@OneToMany`

`@OneToMany`은 **부모 엔터티 하나를 하나 이상의 자식 엔터티와 연결**한다. 만약 `@OneToMany`의 자식 측에 미러링 `@ManyToOne` 연관이 없는 경우, `@OneToMany` 연결(Association)은 단방향(unidirectional)이다. 자식 측에 미러링 `@ManyToOne` 연관이 있는 경우, `@OneToMany` 연결은 양방향이며 애플리케이션 개발자는 양쪽 끝에서 이 관계를 탐색할 수 있다. 참고로 양방향보다는 단방향이 신경쓸 것이 적어서 관리하기가 쉽다.

### 단방향(unidirectional) **`@OneToMany`**

단방향 `@OneToMany` 연관을 사용할 때, Hibernate는 두 조인 엔티티 사이에 링크 테이블을 사용한다.

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

양방향(Bidirectional) `@OneToMany`를 알아보기 전에, 먼저 객체 연관관계와 테이블 연관관계의 차이에 대해 알아보자. 

### 객체 연관관계와 테이블 연관관계의 차이

**객체는 참조(주소)를 사용해서 관계를 맺고 테이블은 외래 키를 사용해서 관계를 맺는다.**

**참조를 통한 연관관계는 언제나 단방향이다.** 객체간에 연관관계를 양방향으로 만들고 싶으면 반대쪽에도 필드를 추가해서 참조를 보관해야 한다. 결국 연관관계를 하나 더 만들어야 한다. 이렇게 양쪽에서 서로 참조하는 것을 양방향 연관관계라 한다. 하지만 정확히 이야기하면 이것은 **양방향 관계가 아니라 서로 다른 단향향 관계 2개다**. 즉 객체에서의 방향(Direction)은 객체의 참조로 볼 수 있다.

다음은 객체의 단방향 연관관계다.

```java
class A {
	B b;
}
class B {}
```

다음은 객체의 양방향 연관관계다.

```java
class A {
	B b;
}
class B {
	A a;
}
```

반면에 테이블은 외래 키로 연관관계를 맺는다. 테이블은 외래 키 하나로 양방향으로 조인할 수 있다. 이 둘은 비슷해 보이지만 매우 다른 특징을 가진다. 연관된 데이터를 조회할 때 객체는 참조(`a.getB()`)를 사용하지만 테이블은 조인(`JOIN`)을 사용한다.

참조를 사용하는 객체의 연관관계는 단방향이다.

- `A → B (a.b)`

외래 키를 사용하는 테이블의 연관관계는 양방향이다.

- `A JOIN B`가 가능하면 반대로 `B JOIN A`도 가능하다.

객체를 양방향으로 참조하려면 단방향 연관관계를 2개 만들어야 한다.

- `A → B(a.b)`
- `B → A(b.a)`

### 양방향(Bidirectional) **`@OneToMany`**

양방향 `@OneToMany` 연관은 자식 측에서도 `@ManyToOne` 연관이 필요하다. 도메인 모델은 이 연관을 탐색(navigate)하기 위해 양 측면을 노출하지만, 배후인 관계형 데이터베이스는 이 관계를 위해 하나의 외래키만 가진다.

모든 양방향 연관에는 하나의 소유(owning)측(자식 측)만 있어야 하고 다른 쪽은 역(inverse)측(또는 `mappedBy`이라고 한다(`mappedBy`는 주인에 의해 매핑되어 있다는 의미로 해석해 볼 수 있겠다). 

**왜 소유(owning)측이 있어야 할까?**

객체는 사실상 참조를 통한 단방향 연관이기 때문에 양쪽 모두에서 값의 변화가 생긴다면 데이터의 일관성(Consistency)이 깨진다. 따라서 한 쪽의 변화만을 인정해야 한다. 데이터의 관리 주체가 양쪽 모두가 될 수 없기 때문이다. 이것이 소유(owning)측이 있어야 하는 이유다.

**누구를 소유(owning)측으로 할까?**

외래키가 있는 쪽(manyToOne)을 소유 측으로 하자. 데이터 변경의 관점 등 여러 면에서 **자식 엔터티를 소유 측으로 지정하는 것이 유리**하다.

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

**소유 관계의 특징**

- 연관관계의 주인만이 외래 키를 관리(등록, 수정)할 수 있다.
- 주인이 아닌 쪽은 읽기만 가능(수정을 해도 반영되지 않는다)

## `@OneToOne`

`@OneToOne` 연관은 단방향 또는 양방향일 수 있다.

### 단방향 `@OneToOne`

- `@ManyToOne` 연관과 유사하다.
- 단방향 연관은 관계형 데이터베이스의 외래 키 의미 체계를 따르며 클라이언트 측에서 관계를 소유한다.
- 외래 키에 데이터베이스에 유니크(UNI) 제약조건이 추가된다.

### **양방향** `@OneToOne`

- **외래 키가 있는 곳을 연관관계의 주인**으로 설정한다(반대편은 `mappedBy`를 적용).
- 연관관계를 어느 쪽(부모, 자식)에서 소유하느냐에 따라서 장점과 단점이 다르다.

**부모 측에서 소유할 경우**

- 주 객체가 대상 객체의 참조를 가지는 것 처럼 **주 테이블에 외래 키를 두고** 대상 테이블을 찾는다.
- 객체지향 개발자 선호한다.
- JPA 매핑 편리하다.
- 장점: 주 테이블만 조회해도 대상 테이블에 데이터가 있는지 확인 가능하다.
- 단점: 값이 없으면 외래 키에 null 허용

**자식 측에서 소유할 경우**

- 자식 테이블에 외래 키가 존재한다.
- 전통적인 데이터베이스 개발자 선호.
- 장점: 부모 테이블과 자식 테이블을 일대일에서 일대다 관계로 변경할 때 테이블 구조 유지할 수 있다.
- 단점: 프록시 기능의 한계로 지연 로딩으로 설정해도 항상 즉시 로딩된다.

## 참조

[https://docs.jboss.org/hibernate/orm/5.6/userguide/html_single/Hibernate_User_Guide.html#associations](https://docs.jboss.org/hibernate/orm/5.6/userguide/html_single/Hibernate_User_Guide.html#associations)

<김영한, 자바 ORM 표준 JPA 프로그래밍, 에이콘>
