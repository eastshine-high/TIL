# 테스트 작성하기

## **Annotations**

- *JUnit Jupiter*는 테스트를 구성하고 이 프레임워크를 확장하기 위한 어노테이션(Annotations)을 지원한다.
- 달리 명시되지 않는 한, 모든 핵심 어노테이션들은 `junit-jupiter-api`의 [org.junit.jupiter.api](https://junit.org/junit5/docs/current/api/org.junit.jupiter.api/org/junit/jupiter/api/package-summary.html) 패키지에 있다.
- 어노테이션들과 그 사용법은 아래 링크를 참조하자.
    - [https://junit.org/junit5/docs/current/user-guide/#writing-tests-annotations](https://junit.org/junit5/docs/current/user-guide/#writing-tests-annotations)

## Test Classes and Methods

### Test Class

- 적어도 하나의 *test method*를 포함하고 있는 최상위 클래스, `static` 멤버 클래스, 또는 `[@Nested`](https://junit.org/junit5/docs/current/user-guide/#writing-tests-nested) 클래스이다.
- ***Test class*는 반드시 `abstract`이 아니어야 하며 반드시 하나의 생성자를 가져야 한다.**

### Test Method

- `@Test`, `@RepeatedTest`, `@ParameterizedTest`, `@TestFactory`, or `@TestTemplate` 으로 어노테이션 또는 메타 어노테이션 처리된 메소드.

### Lifecycle Method

- `@BeforeAll`, `@AfterAll`, `@BeforeEach`, or `@AfterEach`으로 어노테이션 또는 메타 어노테이션 처리된 메소드.

*Test methods* 및 *Lifecycle methods*는 현재 테스트 클래스 내에서 로컬로 선언되거나 슈퍼클래스에서 상속되거나 인터페이스에서 상속될 수 있다([Test Interfaces and Default Methods](https://junit.org/junit5/docs/current/user-guide/#writing-tests-test-interfaces-and-default-methods) 참조). 또한 *Test methods* 및 *lifecycle methods*는 추상(`abstract`)이 아니어야 하며 값을 반환하지 않아야 한다(값을 반환하는 데 필요한 `@TestFactory` 메서드 제외).

> *클래스와 메소드의 가시성(visibility)*
*Test classes, test methods* 및 lifecycle methods는 `public`일 필요는 없지만 `private`일 수는 없다.
테스트 클래스가 다른 패키지의 테스트 클래스에 의해 확장되는 경우와 같이 기술적인 이유가 없는 한 일반적으로 *Test classes, test methods* 및 lifecycle methods에 대해 `public` 한정자를 생략하는 것이 좋다. 클래스와 메소드들을 공개(`public`)하는 또 다른 기술적 이유는 모듈 경로(Java Module System을 사용할 때)에서의 테스트를 단순화하기 위함이다.
> 

아래의 테스트 클래스는 `@Test` 메소드들과 지원되는 모든 lifecycle methods의 사용을 보여준다. 런타임에 대한 추가 정보는 다음을 참조한다([Test Execution Order](https://junit.org/junit5/docs/current/user-guide/#writing-tests-test-execution-order) and [Wrapping Behavior of Callbacks](https://junit.org/junit5/docs/current/user-guide/#extensions-execution-order-wrapping-behavior)).

```java
import org.junit.jupiter.api.AfterAll;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

public class TestFlow {

    @BeforeAll
    static void beforeAll() {
        System.out.println("before all");
    }

    @BeforeEach
    void beforeEach() {
        System.out.println("before each");
    }

    @Test
    void each() {
        System.out.println("test1");
    }

    @Test
    void each2() {
        System.out.println("test2");
    }

    @AfterEach
    void afterEach() {
        System.out.println("after each");
    }

    @AfterAll
    static void afterAll() {
        System.out.println("after all");
    }
}
```

실행 결과

```
before all
before each
test1
after each
before each
test2
after each
after all
```

### `@Test`

- 메소드가 테스트 메소드임을 나타낸다.
- JUnit 4의 `@Test` 어노테이션과 달리 이 어노테이션은은 속성(attributes)들을 선언하지 않는다. JUnit Jupiter의 테스트 익스텐션들은 자체 전용 어노테이션을 기반으로 작동한다.
- 이러한 메서드는 재정의(*override*)되지 않는 한 상속된다.

### `@BeforeAll`, `@AfterAll`

- 이 어노테이션이 달린 메서드는 현재 클래스의 모든 `@Test`, `@RepeatedTest`, `@ParameterizedTest`, `@TestFactory` 메서드 전이나 후에 실행되어야 함을 나타낸다. JUnit 4의 `@BeforeClass`, `@AfterClass`와 유사하다.
- 이러한 메서드는 상속되며(숨겨지거나 재정의되지 않는 한) `static`이어야 한다(’클래스 별(per-class)’ [테스트 인스턴스 생명 주기](https://junit.org/junit5/docs/current/user-guide/#writing-tests-test-instance-lifecycle)가 사용되지 않는 한).

### `@BeforeEach`, `@AfterEach`

- 이 어노테이션이 달린 메서드는 현재 클래스의 각 `@Test`, `@RepeatedTest`, `@ParameterizedTest` 또는 `@TestFactory` 메서드 전후에 실행되어야 함을 나타낸다. JUnit 4의 `@Before`와 유사하다.
- 이러한 메서드는 재정의(*override*)되지 않는 한 상속된다.

## 참조

https://junit.org/junit5/docs/current/user-guide/#writing-tests
