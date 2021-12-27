# Lambda

### 람다란 무엇인가?

**람다 표현식은 메서드로 전달할 수 있는 익명 함수를 단순화한 것**이라고 할 수 있다. 람다 표현식에는 이름은 없지만, 파라미터 리스트, 바디, 반환 형식, 발생할 수 있는 예외 리스트는 가질 수 있다. 람다의 특징을 하나씩 자세히 살펴보자.

- 익명 : 보통의 메서드와 달리 이름이 없으므로 익명이라 표현한다. 구현해야 할 코드에 대한 걱정거리가 줄어든다.
- 함수 : 람다는 메서드처럼 특정 클래스에 종속되지 않으므로 함수라고 부른다. 하지만 메서드처럼 파라미터 리스트, 바디, 반환 형식, 가능한 예외 리스트를 포함한다.
- 전달 : 람다 표현식을 메서드 인수로 전달하거나 변수로 저장할 수 있다.
- 간결성 : 익명 클래스처럼 많은 자질구레한 코드를 구현할 필요가 없다.

하지만 자바는 거의 모든 것이 객체인 객체 지향 언어다. **자바에는 함수 타입이 없다. 그 대신 객체(특정 인터페이스를 구현하는 클래스의 인스턴스)로 함수를 표현**한다. 람다 표현식은 이런 인스턴스를 생성(함수형 인터페이스 구현)하는 아주 편리한 문법을 제공한다.

람다가 기술적으로 자바 8 이전의 자바로 할 수 없었던 일을 제공하는 것은 아니다. 다만 [동작 파라미터](https://github.com/eastshine-high/til/blob/main/java/modern-java/behavior-parameterization.md)를 이용할 때처럼 익명 클래스 등 판에 박힌 코드를 구현할 필요가 없다. 람다 표현식을 이용하면 [동작 파라미터](https://github.com/eastshine-high/til/blob/main/java/modern-java/behavior-parameterization.md) 형식의 코드를 더 쉽게 구현할 수 있다. 결과적으로 코드가 간결하고 유연해진다. 예를 들어 커스텀 Comparator 객체를 기존보다 간단하게 구현할 수 있다.

다음은 기존 코드다.

```java
Comparator<Apple> byWeight = new Comparator<Apple>() {
    public int compare(Apple a1, Apple a2) {
        return a1.getWeight().compareTo(a2.getWeight());
    }
}
```

다음은 람다를 이용한 코드다.

```java
Comparator<Apple> byWeight =
    (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
```

코드가 훨씬 간단해졌다. 람다 표현식은 천천히 설명할 것이므로 (코드가 이해가지 않더라도) 자세한 코드까지 신경 쓰지 말자. 중요한 것은 사과 두 개의 무게를 비교하는 데 필요한 코드를 전달할 수 있다는 점이다. 람다 표현식을 이용하면 compare 메서드의 바디를 직접 전달하는 것처럼 코드를 전달할 수 있다.

<br>

### 람다 표현식 구조

람다 표현식은 세 부분으로 이루어진다.

![https://www.journaldev.com/wp-content/uploads/2017/11/Lamdba.png](https://www.journaldev.com/wp-content/uploads/2017/11/Lamdba.png)

[https://www.journaldev.com/wp-content/uploads/2017/11/Lamdba.png](https://www.journaldev.com/wp-content/uploads/2017/11/Lamdba.png)

- 파라미터 리스트 : Compare 메서드 파라미터(사과 두 개)
- 화살표 : 화살표 (→)는 람다의 파라미터 리스트와 바디를 구분한다.
- 람다 바디 : 두 사과의 무게를 비교한다. 람다의 반환값에 해당하는 표현식이다.

<br>

### 람다 표현식 문법

자바 설계자는 이미 C#이나 스칼라 같은 비슷한 기능을 가진 다른 언어와 비슷한 문법을 자바에 적용하기로 했다. 다음은 표현식 스타일(expression style)람다라고 알려진 람다의 기본 문법이다. 

> `(parameters) → expression`
> 

또는 블록 스타일(Block style)로 표현할 수 있다.

> `(parameters) → { statements }`
> 

Cf. [Expression vs Statement](https://shoark7.github.io/programming/knowledge/expression-vs-statement)

- **반환값이 있는 메서드의 경우, return문 대신 식(expression)으로 대신 할 수 있다**(return문이 함축되어 있다)**.** 식의 연산 결과가 자동적으로 반환값이 된다. 이때는 **문장(statement)이 아닌 식이므로 끝에 `;`을 붙이지 않는다.**

```java
(int a, int b) -> a > b ? a : b
```

- `**return`은 '흐름 제어문'이다.** 괄호`{}`안의 문장이 return문일 경우 괄호를 생략할 수 없다.
- 람다 표현식의 바디에서 **표현식 하나로는 표현할 수 없는 계산을 수행한다면** 메서드를 쓸 때처럼 작성하면 된다. 즉, 다음과 같이 `{}` 로 감싸고 명시적인 `return` 문을 사용한다.

```java
(String, first, String second) -> {
    int difference = fisrt.length() - second.length();
    if (difference < 0) return -1;
    else if (difference > 0) return 1;
    else return 0;
}
```

- 람다 표현식에 매개변수가 없으면 매개변수가 없는 메서드처럼 빈 괄호`()`를 붙여야 한다.

```java
Runnable task = () -> { for (int i = 0; i < 1000; i++) doWork(); }
```

- 람다 표현식의 매개변수 타입을 추론할 수 있다면 다음과 같이 매개변수 타입을 생략할 수 있다.

```java
Comparator<String> comp = 
    (first, second) -> first.length() - second.length()
// (String, first, String second)
    
```

- 메서드에 매개변수가 한 개만 있고, 이 매개변수의 타입을 추론할 수 있다면 괄호`()`도 생략할 수 있다.
- 괄호`{}` 안의 문장이 하나일 때는 괄호를 생략할 수 있다. 이 때 문장의 끝에 `;`을 붙이지 않아야 한다는 것에 주의하자.

```java
EventHandler<ActionEvent> listener = event ->
    System.out.println("Oh noes!")
```

- 람다 표현식의 결과 타입은 명시하지 않는다. 하지만 컴파일러는 람다 표현식 바디에서 결과 타입을 추론한 후 기대하는 타임과 일치하는지 검사한다.

```java
(String first, String second) -> first.length() - second.length()
```

이 표현식은 기대하는 결과가 Int 타입(또는 Integer, long double 같은 호환 타입)인 문맥에 사용할 수 있다.

<br>

---

### Reference

<모던 자바 인 액션, 라울-게이브리얼 우르마 외, 한빛미디어>

<자바의 정석, 남궁성, 도우출판>
