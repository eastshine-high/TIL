# Auditing

Spring Data는 엔티티를 생성하거나 변경한 사람과 변경이 발생한 시기를 투명하게 추적하기 위한 정교한 지원을 제공합니다. 이 기능을 이용하려면 어노테이션을 사용하거나 인터페이스를 구현하여 Auditing 메타데이터를 엔터티 클래스에 장착해야 합니다. 또한 필요한 인프라 컴포넌트들을 등록하려면 어노테이션 Configuration 또는 XML Configuration을 통해 Auditing이 활성화되어 있어야 합니다.

## Auditing 적용을 위한 구성

### Auditing 활성화

Spring Data JPA 1.5 버전 부터는 구성(Configuration) 클래스에 `@EnableJpaAuditing` 어노테이션을 선언하여 감지(auditing)를 활성화할 수 있습니다.

```java
@EnableJpaAuditing
@Configuration
public class JpaConfig {
}
```

### `AuditingEntityListener` 적용

Spring Data JPA는 감시 정보 캡처링을 트리거하는 데 사용할 수 있는 엔터티 리스너(`AuditingEntityListener`)를 제공합니다. `AuditingEntityListener`는 영속성 컨텍스트에 있는 모든 엔티티에 적용하는 방법과 개별 엔티티에 적용하는 방법이 있습니다.

**개별 엔티티에 `AuditingEntityListener` 적용**

`@EntityListeners` 어노테이션을 사용합니다. 이 어노테이션은 *`entity` 혹은* `mapped superclass`에 적용할 수 있습니다.

```java
@Entity
@EntityListeners(AuditingEntityListener.class)
public class MyEntity {

}
```

**모든 엔티티에 `AuditingEntityListener` 적용**

`META-INF/orm.xml`에 등록합니다.

```xml
<?xml version=“1.0” encoding="UTF-8”?>
    <entity-mappings xmlns=“http://xmlns.jcp.org/xml/ns/persistence/orm”
                     xmlns:xsi=“http://www.w3.org/2001/XMLSchema-instance”
                     xsi:schemaLocation=“http://xmlns.jcp.org/xml/ns/persistence/
    orm http://xmlns.jcp.org/xml/ns/persistence/orm_2_2.xsd”
                     version=“2.2">
        <persistence-unit-metadata>
            <persistence-unit-defaults>
                <entity-listeners>
                    <entity-listener
    class="org.springframework.data.jpa.domain.support.AuditingEntityListener”/>
                </entity-listeners>
            </persistence-unit-defaults>
        </persistence-unit-metadata>
    </entity-mappings>
```

## Auditing 적용하기

### 등록일, 수정일

Auditing을 적용할 때는 주로 `Mapped superclass` 에 엔티티 리스너를 선언하고 이를 상속하여 사용합니다. 먼저 `Mapped superclass` 를 정의하겠습니다.

```java
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public class BaseTimeEntity {

    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    @Column(name = "created_at", updatable = false)
    @CreatedDate
    private LocalDateTime createdAt;

    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    @Column(name = "updated_at")
    @LastModifiedDate
    private LocalDateTime updatedAt;
}
```

- Spring Data JPA에서는 `@CreatedDate`, `@LastModifiedDate`, `@CreatedBy`, `@LastModifiedBy` 같은 감지 메타데이터 어노테이션을 지원합니다.
- 변화의 시점을 감지하는 어노테이션( `@CreatedDate`, `@LastModifiedDate` )은 Joda-Time, Java 레거시 `Date` , `Calendar` , JDK8 날짜 시간 유형, `long` 또는 `Long` 타입 속성에 사용될 수 있습니다.

위의 `BaseTimeEntity`를 순수 JPA 코드로 표현하면 다음과 같습니다.

```java
@MappedSuperclass
@Getter
public class BaseTimeEntity {

    @Column(updatable = false)
    private LocalDateTime createdDate;
    private LocalDateTime updatedDate;

    @PrePersist
    public void prePersist() {
        LocalDateTime now = LocalDateTime.now();
        createdDate = now;
        updatedDate = now;
    }

    @PreUpdate
    public void preUpdate() {
        updatedDate = LocalDateTime.now();
    }
}
```

Auditing을 적용할 엔터티는 위에서 정의한 감지 엔터티( `BaseTimeEntity` )를 상속하여 적용합니다.

```java
@Entity
public class Member extends BaseTimeEntity {

    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "member_id")
    private Long id;
}
```

### 등록자, 수정자

`@CreatedBy` 또는 `@LastModifiedBy`를 사용하는 경우, Auditing 인프라스트럭쳐는 현재 보안 주체를 어떻게든 인식해야 합니다. 이를 위해 Spring Data JPA는 `AuditorAware<T>` SPI(Service Provider Interface) 인터페이스를 제공합니다. 이 인터페이스를 구현을 통해 현재 애플리케이션과 상호작용하는 사용자 또는 시스템이 누구인지 인프라에 알릴 수 있습니다. 지네릭 타입 `T`는 `@CreatedBy` 또는 `@LastModifiedBy` 어노테이션이 달린 속성의 유형을 정의합니다.

다음은 스프링 시큐리티를 기반으로 `AuditorAware`을 구현한 예시입니다.

```java
@EnableJpaAuditing
@Configuration
public class JpaConfig {

    @Bean
    public AuditorAware<Long> auditorProvider() {
        return () -> Optional.ofNullable(SecurityContextHolder.getContext())
                .map(SecurityContext::getAuthentication)
                .filter(Authentication::isAuthenticated)
                .map(Authentication::getPrincipal)
                .map(UserInfo.class::cast)
                .map(UserInfo::getId);
    }
}
```

등록자, 수정자 속성이 있는 `Mapped superclass`를 정의합니다.

```java
@MappedSuperclass
public class BaseEntity extends BaseTimeEntity{

    @CreatedBy
    @Column(updatable = false)
    private Long createdBy;

    @LastModifiedBy
    private Long lastModifiedBy;
}
```

- 위의 등록일, 수정일 예제의 `BaseTimeEntity` 를 상속합니다. 이를 통해 변경 시점과 변경한 주체를 모두 감지할 수 있습니다. 변경 시점만 트래킹하는 경우가 있기 때문에, 위와 같은 방식으로 구현하는 것이 일반적입니다.
- 상속을 사용할 때, `@EntityListeners(AuditingEntityListener.class)` 는 상속을 통해 재사용 가능하지만 `@MappedSuperclass`는 재사용할 수 없습니다.

위에서 정의한 감지 엔티티( `BaseEntity` )를 상속하여 변경 시점과 주체를 모두 감지하는 Auditing을 적용할 수 있습니다.

```java
@Entity
@NoArgsConstructor
public class Product extends BaseEntity {

    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "product_id")
    private Long id;

		private int price;
}
```

---

**참조**

[https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#auditing](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#auditing)
