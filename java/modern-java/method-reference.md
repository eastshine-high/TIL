# 메서드 참조(method reference)

람다식이 하나의 메서드만 호출하는 경우에는 **메서드 참조(method reference)** 라는 방법으로 람다식을 간략히 할 수 있다. **메서드 참조는 특정 메서드만을 호출하는 람다의 축약형**이라고 생각할 수 있다.

메서드 참조를 이용하면 기존의 메서드 정의를 재활용해서 람다처럼 전달할 수 있다. 때로는 람다 표현식보다 메서드 참조를 사용하는 것이 더 가독성이 좋으며 자연스러울 수 있다.

예를 들어 문자열을 정수로 변환하는 람다식은 아래와 같이 작성할 수 있다.

```java
Function<String, Integer> f = (String s) -> Integer.parseInt(s);
```

보통은 이렇게 람다식을 작성하는데, 이 람다식을 메서드로 표현하면 아래와 같다.

```java
Integer wrapper(String s) { // 이 메서드의 이름은 의미없다.
    return Integer.parseInt(s);
}
```

이 wrapper메서드는 별로 하는 일이 없다. 그저 값을 받아서 `Integer.parseInt()`에게 넘겨주는 일만 할  뿐이다. **차라리 이 거추장스러운 메서드를 벗겨내고 `Integer.parseInt()`를 직접 호출하는 것이 낫지 않을까?**

```java
Function<String, Integer> f = Integer::parseInt;
```

이 메서드 참조에서 람다식의 일부가 생략되었지만, 컴파일러는 생략된 부분을 우변의 parseInt메서드의 선언부로부터, 또는 좌변의 Function인터페이스에 지정된 지네릭 타입으로부터 쉽게 알아낼 수 있다.

## 메소드 참조를 만드는 방법

```
 '클래스이름::메서드이름' 또는 참조변수::메서드이름'
```

메소드 참조는 세 가지 유형으로 나눌 수 있다.

1. 정적 메서드 참조

3. 기존 객체의 인스턴스 메서드 참조(한정적 참조)

2. 다양한 형식의 인스턴스 메서드 참조(비한정적 참조)

### 정적 메서드 참조

가장 흔한 유형이다. 클래스의 static 메소드를 참조한다.

```java
// 람다
(args) -> ClassName.staticMethod(args)

// 메서드 참조
ClassName::staticMethod
```

```java
// 람다
map.merge(key, 1, (count, incr) -> count + incr);

// 메서드 참조
map.merge(key, 1, Integer::sum)
```

### 기존 객체의 인스턴스 메서드 참조(한정적 참조)

인스턴스 메서드를 참조하는 유형은 두 가지이다. 그중 하나는 수신 객체(receiving object, 참조 대상 인스턴스)를 한정(bound)하는 인스턴스 메서드 참조이다. **한정적(bound) 참조**는 근본적으로 정적 참조와 비슷하다. **즉, 함수 객체가 받는 인수와 참조되는 메서드가 받는 인수가 똑같다.**

```java
// 람다
(args) → expr.instanceMethod(args)

// 메서드 참조
expr::instanceMethod
```

한정적 참조는 람다 표현식에서 현존하는 외부 객체의 메서드를 호출할 때 사용된다.

```java
// 람다
() → expensiveTransaction.getValue()

// 메서드 참조
expensiveTransaction::getValue
```

한정적 참조는 비공개 헬퍼 메서드를 정의한 상황에서 유용하게 활용할 수 있다. 예를 들어 다음처럼 isValidName이라는 헬퍼 메서드를 정의했다고 가정하자.

```java
private boolean isValidName(String string) {
		return Character.isUpperCase(string.charAt(0));
}
```

이제 Predicate<String>를 필요로 하는 적당한 상황에서 메서드 참조를 사용할 수 있다.

```java
filter(words, this::isValidName)
```

### 다양한 형식의 인스턴스 메서드 참조(비한정적 참조)

수신 객체를 한정하지 않는 **비한정적(unbound) 참조**에서는 **함수 객체를 적용하는 시점에 수신 객체를 알려준다.** 이를 위해 수신 객체 전달용 매개변수가 매개변수 목록의 첫 번째로 추가되며, 그 뒤로는 참조되는 메서드 선언에 정의된 매개변수들이 뒤따른다. 비한정적 참조는 주로 스트림 파이프라인에서의 매핑과 필터 함수에 쓰인다.

```java
// 람다
(arg0, rest) → arg0.instanceMethod(rest) //arg0은 ClassName 형식

// 메서드 참조
ClassName::instanceMethod
```

```java
// 람다
BiPredicate<List<String>, String> biPredicate = 
                (list, element) -> list.contains(element);

// 메서드 참조
BiPredicate<List<String>, String> biPredicate = 
                List::contains;
```
## 람다가 메서드 참조보다 간결한 경우

람다가 메서드 참조보다 간결할 때가 있다. 주로 메서드와 람다가 같은 클래스에 있을 때 그렇다. 예를 들어 다음 코드가 `GoshThisClassNameIsHumongous` 클래스 안에 있다고 해보자.

```java
service.execute(GoshThisClassNameIsHumongous::action);
```

이를 람다로 대체하면 다음처럼 된다.

```java
service.execute(() → action());
```

메서드 참조 쪽은 더 짧지도, 더 명확하지도 않다. 따라서 람다 쪽이 낫다. 같은 선상에서 `java.util.function` 패키지가 제공하는 제네릭 정적 팩터리 메서드인 `Function.identity()`를 사용하기보다는 똑같은 기능의 람다`(x → x)`를 직접 사용하는 편이 코드도 짧고 명확하다.

---

## 참조

<자바의 정석, 남궁성, 도우출판>

<조슈아 블로크, 이펙티브 자바 3th, 인사이트>

<라울-게이브리얼 우르마 외, 모던 자바 인 액션, 한빛미디어>
