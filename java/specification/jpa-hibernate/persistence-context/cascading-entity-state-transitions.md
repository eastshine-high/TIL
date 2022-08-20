# 영속성 전이, 고아 객체 제거

이 장은 먼저 [엔터티의 연관 관계(Associations)](https://github.com/eastshine-high/til/blob/main/java/specification/jpa-hibernate/domain-model/associations.md)에 대한 이해를 전제로 설명을 시작합니다.

JPA를 사용하면 부모 엔터티에서 자식 엔터티로 **영속 상태의 변화(transition)를 전파(propagate**)할 수 있습니다. 이를 위해 JPA 은 다양한 캐스케이드 유형(`javax.persistence.CascadeType`)을 정의합니다.

- ALL: 모두 적용
- PERSIST: 영속
- REMOVE: 삭제
- MERGE: 병합
- REFRESH: 리프레쉬
- DETACH: 분리

주로 `PERSIST` , `REMOVE` , `ALL` 옵션을 사용합니다. 

이번 페이지에서는 `CascadeType.PERSIST`, `CascadeType.MERGE`, `CascadeType.REMOVE`, 고아 객체 제거(`orphanRemoval`), `@OnDelete` 에 대해서 알아보겠습니다.

### 예시를 위한 엔터티 정의

먼저 예시를 위한 부모 엔터티와 자식 엔터티를 정의하겠습니다.

```java
@Entity
public class Person {

    @Id
    private Long id;

    private String name;

    @OneToMany(mappedBy = "owner")
    private List<Phone> phones = new ArrayList<>();

    //Getters and setters are omitted for brevity

    public void addPhone(Phone phone) {
        this.phones.add( phone );
        phone.setOwner( this );
    }
}

@Entity
public class Phone {

    @Id
    private Long id;

    @Column(name = "`number`")
    private String number;

    @ManyToOne(fetch = FetchType.LAZY)
    private Person owner;

    //Getters and setters are omitted for brevity
}
```

### `CascadeType.PERSIST`

`CascadeType.PERSIST` 는 부모 엔터티의 영속성 상태가 PERSIST로 변화했을 때, 이를 자식 엔터티에도 전파합니다.

먼저 `CascadeType.PERSIST` 를 사용하지 않은 엔터티의 영속화를 살펴보겠습니다. `CascadeType.PERSIST` 을 적용하지 않고 영속화를 하기 위해서는 생성된 엔터티 마다 영속화 메서드(`persist`)를 호출해야 합니다.

```java
Person person = new Person();
person.setId( 1L );
person.setName( "John Doe" );

Phone phone = new Phone();
phone.setId( 1L );
phone.setNumber( "123-456-7890" );

person.addPhone( phone );

em.persist(person);
em.persist(phone1);
```

만약 `em.persist(phone1)` 메서드를 명시적으로 호출하지 않았다면, JPA는 `person` 객체에 대해서만 영속화를 수행합니다.

이번에는 `CascadeType.PERSIST` 를 사용한 영속화를 살펴보겠습니다.

```java
@OneToMany(mappedBy = "owner", cascade = CascadeType.ALL)
private List<Phone> phones = new ArrayList<>();
```

```java
Person person = new Person();
person.setId( 1L );
person.setName( "John Doe" );

Phone phone = new Phone();
phone.setId( 1L );
phone.setNumber( "123-456-7890" );

person.addPhone( phone );

em.persist( person );
```

```java
INSERT INTO Person ( name, id )
VALUES ( 'John Doe', 1 )

INSERT INTO Phone ( `number`, person_id, id )
VALUE ( '123-456-7890', 1, 1 )
```

자식 엔터티(`phone`)에 영속화 메소드(`em.persist`)를 호출하지 않아도 부모 엔터티가 영속화될 때, 자식 엔터티 또한 영속화가 되는 것을 확인할 수 있습니다.

### `CascadeType.MERGE`

`CascadeType.MERGE`를 사용하면 부모 엔터티과 함께 자식 엔터티를 병합할 수 있습니다.

```java
Phone phone = entityManager.find( Phone.class, 1L );
Person person = phone.getOwner();

person.setName( "John Doe Jr." );
phone.setNumber( "987-654-3210" );

entityManager.clear();

entityManager.merge( person );
```

```sql
SELECT
    p.id as id1_0_1_,
    p.name as name2_0_1_,
    ph.owner_id as owner_id3_1_3_,
    ph.id as id1_1_3_,
    ph.id as id1_1_0_,
    ph."number" as number2_1_0_,
    ph.owner_id as owner_id3_1_0_
FROM
    Person p
LEFT OUTER JOIN
    Phone ph
        on p.id=ph.owner_id
WHERE
    p.id = 1
```

병합을 수행하면, 엔터티의 현재 상태가 데이터베이스에서 방금 가져온 엔터티의 버전으로 복사됩니다. 이것이 Hibernate가 `Person` 엔터티와 자식 엔터티들을 모두 가져오는 `SELECT` 문을 실행한 이유입니다.

### `CascadeType.REMOVE`

`CascadeType.REMOVE`를 사용하면 부모 엔터티와 함께 자식 엔터티를 제거할 수 있습니다. 전통적으로 Hibernate는 이 작업을 삭제라고 불렀습니다. 이것이 `org.hibernate.annotations.CascadeType`이 `DELETE` 캐스케이드 옵션을 제공하는 이유입니다. `CascadeType.REMOVE`와 `org.hibernate.annotations.CascadeType.DELETE`는 완전히 동일합니다.

```java
Person person = entityManager.find( Person.class, 1L );

entityManager.remove( person );
```

```sql
DELETE FROM Phone WHERE id = 1

DELETE FROM Person WHERE id = 1
```

### `CascadeType` 사용시 주의할 점

사용시 주의할 점은 부모 객체만 자식을 관리(소유)할 때 사용해야 합니다(부모와 자식의 라이프 사이클이 유사할 때). 예를 들어 게시판에 대한 첨부 파일 테이블이 자식 객체일 때는 사용해도 괜찮습니다. 그러나 첨부 파일 테이블이 여러 테이블과 연관되어 있을 경우에, 다른 쪽에서 자식 데이터에 영향을 줄 수도 있어 사용에 위험합니다. 특히 서비스 운영에 어려움을 겪을 수도 있습니다. 특히 `CascadeType.REMOVE` 는 데이터가 삭제되는 옵션이므로 `CascadeType.PERSIST` 보다 더 주의해서 사용해야 합니다.

### 고아 객체 제거(orphanRemoval)

고아 객체 제거는 부모 엔티티와 연관관계가 끊어진 자식 엔티티를 자동으로 삭제합니다.

다음과 같이 `orphanRemoval` 속성을 사용하여 선언할 수 있습니다(`@OneToOne` 관계에서도 사용 가능합니다).

```java
@OneToMany(mappedBy = "owner", cascade = CascadeType.PERSIST, orphanRemoval = true)
private List<Phone> phones = new ArrayList<>();
```

```java
Person person = entityManager.find( Person.class, 1L );
person.getPhones().remove(0);
```

```
DELETE FROM Phone WHERE id = 1
```

고아객체 역시 데이터를 자동 삭제하는 기능인 만큼 사용에 주의해야 합니다. `CascadeType` 사용시 주의할 점과 마찬가지로 부모 객체만 자식을 관리(소유)할 때 사용해야 합니다.

### `@OnDelete` cascade

위에서 살펴본 캐스케이드 타입들은 엔터티의 상태를 전파하는 편리함을 제공할 뿐, 연관관계를 매핑하는 것과는 관련이 없습니다. `@OnDelete` 캐스케이드는 DDL 레벨의 외래키를 통해 부모 행(row)이 삭제되었을 경우, 자식 레코드를 연쇄 삭제할 지를 지정합니다.

따라서 `@ManyToOne` 연관에 `@OnDelete( action = OnDeleteAction.CASCADE )` 어노테이션을 같이 선언하면, 자동 스키마 생성기는 다음 예제와 같이 ON DELETE CASCADE SQL 지시어를 외래키 선언에 적용합니다.

```java
@Entity(name = "Person")
public static class Person {

	@Id
	private Long id;

	private String name;

	//Getters and setters are omitted for brevity

}
```

```java
@Entity(name = "Phone")
public static class Phone {

	@Id
	private Long id;

	@Column(name = "`number`")
	private String number;

	@ManyToOne(fetch = FetchType.LAZY)
	@OnDelete( action = OnDeleteAction.CASCADE )
	private Person owner;

	//Getters and setters are omitted for brevity

}
```

```sql
create table Person (
    id bigint not null,
    name varchar(255),
    primary key (id)
)

create table Phone (
    id bigint not null,
    "number" varchar(255),
    owner_id bigint,
    primary key (id)
)

alter table Phone
    add constraint FK82m836qc1ss2niru7eogfndhl
    foreign key (owner_id)
    references Person
    on delete cascade
```

---

**참조**

[https://docs.jboss.org/hibernate/orm/5.6/userguide/html_single/Hibernate_User_Guide.html#pc-cascade](https://docs.jboss.org/hibernate/orm/5.6/userguide/html_single/Hibernate_User_Guide.html#pc-cascade)
