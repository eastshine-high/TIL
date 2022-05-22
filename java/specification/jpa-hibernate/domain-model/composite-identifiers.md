# 복합 식별자(Composite identifiers)

복합적인 식별자들은 하나 이상의 영속 속성들에 대응한다(Composite identifiers correspond to one or more persistent attributes). 다음은 JPA 표준에 정의된 복합 식별자를 관리하는 **규칙**이다.

- 복합 식별자는 ‘기본 키 클래스’로 표현되어야 한다. 기본 키 클래스는 `javax.persistence.EmbeddedId` 어노테이션을 사용하여 정의하거나, `javax.persistence.IdClass` 어노테이션을 사용하여 정의할 수 있다.
- 기본 키 클래스는 `public`이어야 하며 기본 생성자가 있어야 한다.
- 기본 키 클래스는 `Serializable` 인터페이스를 반드시 구현해야 한다.
- 기본 키 클래스는 `equals`, `hashCode` 메소드를 구현해야 한다.

> 복합 식별자들은 ‘기본 키 클래스’(예: `@EmbeddedId` 또는 `@IdClass`)로 표현되어야 하는 제한은 JPA에만 해당된다. **Hibernate는 다중 `@Id` 속성을 통해 "기본 키 클래스" 없이 복합 식별자를 정의할 수 있다.**
> 

복합 식별자를 구성하는 속성들은 기본(basic), 복합(composite), `@ManyToOne`일 수 있다. 컬렉션과 일대일은 적절하지 않은 점에 유의한다.

## **`@EmbeddedId`를 이용한 복합 식별자**

EmbeddedId를 사용하여 복합 식별자를 모델링한다는 것은 단순히 [내장 타입(embedable](https://docs.jboss.org/hibernate/orm/5.6/userguide/html_single/Hibernate_User_Guide.html#embeddables), 하나 이상의 속성을 조합)을 이용하여 식별자를 정의하는 것을 의미한다.

다음은 회원(`Member`) 엔터티의 식별자(`member_id`)와 권한 타입(`RoleType`)으로 권한(`Role`) 엔터티의 복합 식별자를 구성하는 예시이다.

`Member` 엔터티.

```java
@EqualsAndHashCode(of = "id")
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Entity
public class Member {

    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "member_id")
    private Long id;

		@OneToMany(mappedBy = "roleId.member")
    private List<Role> roles = new ArrayList<>();
		// ... 구현 생략
}
```

- `@OneToMany(mappedBy = "roleId.member")` : `mappedBy`를 선언할 경우, `Role`클래스의 `roldId` 속성에서부터 접근해야 한다.

`Role` 엔터티

```java
@NoArgsConstructor
@Entity
public class Role {

    @EmbeddedId
    private RoleId roleId;

    public Role(RoleId roleId) {
        this.roleId = roleId;
    }
}
```

`Role` 엔터티의 기본 키 클래스(`RoleId`)

```java
@EqualsAndHashCode(of = {"member", "roleType"})
@NoArgsConstructor
@Embeddable
public class RoleId implements Serializable {

    @ManyToOne(fetch = FetchType.LAZY)
    private Member member;

    @Enumerated(value = EnumType.STRING)
    private RoleType roleType;

    public RoleId(Member member, RoleType roleType) {
        this.member = member;
        this.roleType = roleType;
    }
}
```

위에서 언급한 대로 `@ManyToOne`는 복합 식별자를 구성하는 속성으로 사용할 수 있다.

## **`@IdClass`를 이용한 복합 식별자**

IdClass를 사용하여 복합 식별자를 모델링하는 것은 엔터티가 개별 속성들을 각각 정의한다는 점에서 EmbeddedId를 사용하는 것과 다르다. IdClass는 단순히 "그림자" 역할을 한다.

```java
@Entity(name = "SystemUser")
@IdClass( PK.class )
public static class SystemUser {

	@Id
	private String subsystem;

	@Id
	private String username;

	private String name;

	public PK getId() {
		return new PK(
			subsystem,
			username
		);
	}

	public void setId(PK id) {
		this.subsystem = id.getSubsystem();
		this.username = id.getUsername();
	}

	//Getters and setters are omitted for brevity
}

public static class PK implements Serializable {

	private String subsystem;

	private String username;

	public PK(String subsystem, String username) {
		this.subsystem = subsystem;
		this.username = username;
	}

	private PK() {
	}

	//Getters and setters are omitted for brevity

	@Override
	public boolean equals(Object o) {
		if ( this == o ) {
			return true;
		}
		if ( o == null || getClass() != o.getClass() ) {
			return false;
		}
		PK pk = (PK) o;
		return Objects.equals( subsystem, pk.subsystem ) &&
				Objects.equals( username, pk.username );
	}

	@Override
	public int hashCode() {
		return Objects.hash( subsystem, username );
	}
}
```

## 참조

[https://docs.jboss.org/hibernate/orm/5.6/userguide/html_single/Hibernate_User_Guide.html#identifiers-composite](https://docs.jboss.org/hibernate/orm/5.6/userguide/html_single/Hibernate_User_Guide.html#identifiers-composite)
