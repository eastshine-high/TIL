# Parameterized Tests

파라미터화된 테스트는 각기 다른 인자(argument)들로 여러 번 테스트하는 것을 가능하게 해준다. 파라미터화된 테스트는 `@Test` 메소드와 비슷하게 선언하지만 [`@ParameterizedTest`](https://junit.org/junit5/docs/current/api/org.junit.jupiter.params/org/junit/jupiter/params/ParameterizedTest.html) 어노테이션을 사용하여 선언한다. 그리고 테스트 메소드에서 인자들을 발생시키고 제공하기 위한 **소스(**S**ource)**를 적어도 하나 선언해야 한다.

아래의 예는 `@ValueSource` 어노테이션을 사용하여 `String` 배열을 아규먼트 **소스(Source)**로 지정하여 파라미터화된 테스트하는 것을 보여준다.

```java
@ParameterizedTest
@ValueSource(strings = { "racecar", "radar", "able was I ere I saw elba" })
void palindromes(String candidate) {
    assertTrue(StringUtils.isPalindrome(candidate));
}
```

위의 파라미터화된 테스트가 실행되면 각 발생(invocation)마다 개별로 리포트된다. 예를 들어 `ConsoleLauncher`는 아래와 유사하게 출력을 할 것이다.

```
palindromes(String) ✔
├─ [1] candidate=racecar ✔
├─ [2] candidate=radar ✔
└─ [3] candidate=able was I ere I saw elba ✔
```

## Required Setup

parameterized tests를 위해서는 `junit-jupiter-params` artifact 의존성이 필요하다. 자세한 내용은 [Dependency Metadata](https://junit.org/junit5/docs/current/user-guide/#dependency-metadata)을 참조한다.

## **Sources of Arguments**

JUnit Jupiter는 몇가지 **[소스(Source)** 어노테이션](https://junit.org/junit5/docs/current/api/org.junit.jupiter.params/org/junit/jupiter/params/provider/package-summary.html)을 제공한다.

### `@ValueSource`

- 간단한 소스(Source) 중의 하나이다.
- 리터럴 값의 단일 배열을 지정할 수 있다.
- parameterized test 호출당 하나의 인수를 제공하는 데만 사용할 수 있다.

### `@NullAndEmptySource`, `@EnumSource`**,** `@MethodSource`

- 하나의 아규먼트만 받는 *파라미터화된 테스트*에서  빈 값 혹은 `null`의 소스를 제공한다.

### **`@EnumSource`**

`Enum` 상수를 사용하는 편리한 방법을 제공한다.

### `@MethodSource`

테스트 클래스 또는 외부 클래스에서 하나 이상의 팩터리 메서드를 참조할 수 있다.

### `@CsvSource`

아규먼트 목록을 쉼표로 구분된 값(즉, CSV 문자열 리터럴)들로 표현할 수 있다.

### `@CsvFileSource`

클래스 경로 또는 로컬 파일 시스템에서 쉼표로 구분된 값(CSV) 파일을 사용할 수 있다.

### `@ArgumentsSource`

재사용 가능한 커스텀 `ArgumentsProvider`를 지정하는 데 사용할 수 있다.

> 소스 어노테이션들의 구체적 사용법은 아래를 참조하자.
[https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests-sources](https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests-sources)
> 

## Consuming Arguments

Parameterized test의 메소드는 일반적으로 argument source index 와 method parameter index([@CsvSource](https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests-sources-CsvSource) 예시 참조) 사이의 일대일 대응 관계를 통해 구성된 **소스(source)**(see [Sources of Arguments](https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests-sources))로 부터 오는 인자들을 사용한다.

```java
@ParameterizedTest
@CsvSource({
    "apple,         1",
    "banana,        2",
    "'lemon, lime', 0xF1",
    "strawberry,    700_000"
})
void testWithCsvSource(String fruit, int rank) {
    assertNotNull(fruit);
    assertNotEquals(0, rank);
}
```

소스(source)로 부터 오는 인자들이 많아질 경우 메서드 시그니처는 커질 수 있다. 이 경우 복수의 파라미터 대신에 [ArgumentsAccessor](https://junit.org/junit5/docs/current/api/org.junit.jupiter.params/org/junit/jupiter/params/aggregator/ArgumentsAccessor.html)를 사용하여 소스(source)로 부터 오는 인자들을 하나의 집합된 객체로 전달할 수 있다.

```java
@ParameterizedTest
@CsvSource({
    "Jane, Doe, F, 1990-05-20",
    "John, Doe, M, 1990-10-22"
})
void testWithArgumentsAccessor(ArgumentsAccessor arguments) {
    Person person = new Person(arguments.getString(0),
                               arguments.getString(1),
                               arguments.get(2, Gender.class),
                               arguments.get(3, LocalDate.class));

    if (person.getFirstName().equals("Jane")) {
        assertEquals(Gender.F, person.getGender());
    }
    else {
        assertEquals(Gender.M, person.getGender());
    }
    assertEquals("Doe", person.getLastName());
    assertEquals(1990, person.getDateOfBirth().getYear());
}
```

`ParameterResolver`에서 추가 인수를 제공할 수도 있다(예: `TestInfo`, `TestReporter` 등의 인스턴스를 얻기 위해). 특히 Parameterized test 메소드는 다음 규칙에 따라 파라미터를 선언해야 한다.

- 0개 이상의 익덱스된 아큐먼트를 먼저 선언해야 한다.
- 0개 이상의 집합체(*aggregator*)들을 먼저 선언해야 한다.
- `ParameterResolver`에서 제공하는 0개 이상의 인수는 마지막에 선언해야 한다.

```java
@BeforeEach
void beforeEach(TestInfo testInfo) {
    // ...
}

@ParameterizedTest
@ValueSource(strings = "apple")
void testWithRegularParameterResolver(String argument, TestReporter testReporter) {
    testReporter.publishEntry("argument", argument);
}

@AfterEach
void afterEach(TestInfo testInfo) {
    // ...
}
```

## 참조

https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests
