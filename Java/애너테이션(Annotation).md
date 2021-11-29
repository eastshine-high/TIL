# 애너테이션

## 애너테이션 정의

각 애너테이션은 반드시 @interface 문법을 사용해 애너테이션 인터페이스로 선언해야 한다. 인터페이스의 메서드는 애너테이션의 요소에 대응한다. 예를 들어 JUnit의 @Test 애너테이션은 다음 인터페이스로 정의되어 있다.

```java
@Tartget(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Test {
    long timeout();
    ...
}
```

**@interface 선언은 실제 자바 인터페이스를 만들어 낸다.** 애너테이션을 처리하는 도구는 애너테이션 인터페이스를 구현하는 객체를 받는다. JUnit 테스트 수행 도구가 Test를 구현하는 객체를 받으면, timeout 메서드를 호출해 Test 애너테이션의 타임아웃 요소를 추출한다.

**애너테이션 인퍼테이스의 요소 선언은 실제로 메서드 선언이다.** 애너테이션 인터페이스의 메서드에는 매개변수와 throws 절을 포함할 수 없고 제네릭으로도 만들 수 없다.

요소의 기본 값을 지정하려면 해당 요소를 정의하는 메서드 뒤에 default 절을 추가해야 한다.

```java
//JPA 어노테이션에서 발췌
public @interface GeneratedValue {

    /**
     * (Optional) The primary key generation strategy
     * that the persistence provider must use to
     * generate the annotated entity primary key.
     */
    GenerationType strategy() default AUTO; //enum의 value 중에 AUTO를 default 값으로

    /**
     * (Optional) The name of the primary key generator
     * to use as specified in the {@link SequenceGenerator} 
     * or {@link TableGenerator} annotation.
     * <p> Defaults to the id generator supplied by persistence provider.
     */
    String generator() default "";
}
```

애너테이션의 요소는 다음 중 하나다. `기본 타입 값`, `String`, `Class 객체`, `enum 인스턴스`, `애너테이션`, `위 항목의 배열`(하지만 배열의 배열은 안 된다.)

다음은 배열의 기본 값(빈 배열)과 애너테이션의 기본 값을 지정하는 방법이다.

```java
public @interface BugReport{
    String[] reportedBy() default {};
    // 기본 값은 빈 배열이다.
    Reference ref() default @Reference(id=0);
    // 애너테이션의 기본 값을 지정한다.
    ...
}
```

**기본 값은 애너테이션과 함께 저장되지 않고 동적으로 계산된다.** 기본 값을 변경하고 애너테이션 클래스를 다시 컴파일하면, 심지어 기본 값을 변경하기 전에도 컴파일한 클래스 파일에서 모든 애너테이션 요소가 새로운 기본 값을 사용한다.

@Target 매타애너테이션은 애너테이션을 적용할 수 있는 위치를 나타낸다. 값은 ElementType 객체의 배열로 애터테이션을 적용할 수 있는 아이템을 나타낸다. 요소 타입은 중괄호로 감싸며 몇 개든 명시할 수 있다. 예를 들어 다음과 같다.

```java
@Target({ ElementType.TYPE, ElementType.FIELD })
public @interface MockBean
```

컴파일러는 허용된 위치에서 애너테이션을 사용했는지만 검사한다. 예를 들어 @MockBean을 메소드에 적용하면 컴파일 시간 오류가 일어난다.

@Target 제한이 없는 애너테이션은 어떤 선언에든 쓸 수 있지만, 타입 매개변수와 타입 사용에는 쓸 수 없다(처음으로 애너테이션을 지원한 자바 릴리즈에서는 선언 타깃만 사용할 수 있었다).

@Target 애너테이션용 요소 타입

- ElementType.ANNOTATION_TYPE : 애노테이션 타입 선언
- ElementType.CONSTRUCTOR
- ElementType.FIELD : 인스턴스 변수(enum 상수 포함)
- ElementType.LOCAL_VARIABLE
- ElementType.METHOD
- ElementType.PACKAGE
- ElementType.PARAMETER
- ElementType.TYPE : 클래스(enum 포함)와 인터페이스(애너테이션 타입 포함)
- ElementType.TYPE_PARAMETER
- ElementType.TYPE_USE : 타입 사용

@Retention 메타에너테이션은 애너테이션에 접근할 수 있는 위치를 지정한다. 다음 세 가지 중 하나를 선택할 수 있다.

1. RetentionPolicy.SOURCE: 애너테이션이 소스 처리기에는 보이지만, 클래스 파일에는 포함되지 않는다.
2. RetentionPolicy.CLASS: 애너테이션이 클래스 파일에 포함되지만, 가상 머신은 해당 애너테이션을 로드하지 않는다. 기본 값이다.
3. RetentionPolicy.RUNTIME: 애너테이션을 실행 시간에 이용할 수 있고, 리플렉션 API로 접근할 수 있다.

애너테이션 인터페이스는 확장할 수 없으므로 애너테이션 인터페이스를 구현하는 클래스를 만들 수 없다. 그 대신 소스 처리 도구와 가상 머신은 필요할 때 프록시 클래스와 객체를 만들어 낸다.

<br>

## 애너테이션 사용

```java
public class CacheTest{

    ...

   @Test
    public void checkReandomInsertions()

}
```

 자바에서는 에너테이션을 public이나 static 같은 제어자처럼 사용한다. 각 에너테이션 이름 앞에는 @기호가 붙는다. @Test 에너테이션 자체로는 아무것도 하지 않으므로 이를 유용하게 사용하려면 도구가 있어야 한다. 예를 들어 JUnit 4 테스팅 도구(http://junit.org)는 클래스를 테스트할 때 @Test가 붙은 모든 메서드를 호출한다. 또 다른 도구는 클래스 파일에서 모든 테스트 메서드를 제거해 테스트를 마친 후에는 프로그램에 테스트 메서드가 포함되지 않게 한다.

<br>

### 에너테이션 요소

에너테이션은 요소(element)라고 하는 키/값 쌍을 포함할 수 있다.

```java
@Test(timeout=1000)
```

허용되는 요소 이름과 타입은 각 에너테이션에서 정의한다. 에너테이션의 요소는 해당 에너테이션을 읽는 도구로 처리한다.

애너테이션의 요소는 다음 중 하나다.(애너테이션 요소는 절대로 null 값을 가질 수 없다)

`기본 타입 값`, `String`, `Class 객체`, `enum 인스턴스`, `애너테이션`, `위 항목의 배열`(하지만 배열의 배열은 안 된다.)

예를 들어 다음과 같다.

```java
@BugReport(showStopper=true,
    assignedTo="Harry",
    testCase=CacheTest.class,
    status=BugReport.Status.CONFIRMED)
```

- 애너테이션 요소는 기본 값을 가질 수 있다.

예를 들어 JUnit @Test 애너테이션의 timeout 요소는 기본 값이 0L이다. 따라서 애너테이션 @Test와 @Test(timeout=0L)은 의미가 같다.

- 요소 이름이 value이고 이 요소만 지정할 때는 value=를 생략할 수 있다.

즉 @SuppressWarnings("unchecked)는 @SuppressWarnings(value="unchecked)와 같다.

- 요소 값이 배열이면 해당 배열의 구성 요소를 중괄호로 감싼다.

@BugReport(reportedBy={"Harry", "Fred"})

- 배열에 구성 요소가 하나만 있으면 중괄호를 생략해도 된다.

@BugReport(reportedBy="Harry") // {"Harry"}와 같다.

- 애너테이션 요소로 다른 애너테이션을 사용할 수 있다.

@BugReport(ref=@Reference(id=11235811), ...)

<br>

### 다중 애너테이션과 반복 애너테이션

- 아이템 하나에 여러 애너테이션을 붙일 수 있다.
- 애너테이션 작성자가 애너테이션을 반복 가능으로 선언했다면 같은 애너테이션을 여러 번 반복할 수 있다.

```java
@Test
@BugReport(showStopper=true, reportedBy="Joe")
@BugReport(reportedBy={"Harry", "Carl"})
public void testSomething()
```

---

### Reference

가장 빨리 만나는 코어 자바9, 카이 호스트만, 길벗