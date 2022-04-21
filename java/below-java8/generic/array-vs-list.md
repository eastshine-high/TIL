# Array vs List

배열과 제네릭 타입에는 중요한 차이가 두 가지 있다. 1 배열은 공변(covariant)이고 제네릭은 불변(invariant)이다. 2 배열은 실체화(reify)되지만 제네릭은 아니다.

### 공변(covariant)과 불변(invariant)

`Employee` 클래스의 서브클래스 객체로 구성된 배열을 처리하는 메서드를 구현한다고 하자. 간단히 매개변수를 `Employee[]` 타입으로 선언하면 된다.

```java
public static void process(Employee[] staff) { ... }
```

`**Manager`가 `Employee`의 서브클래스면 `Manager[]`도 `Employee[]`의 서브타입이므로 `Manager[]` 배열을 이 메서드에 전달할 수 있다.** 이런 동작을 ***공변(***covariant***)***이라고 한다. 즉, 배열은 요소 타입과 같은 방식으로 변한다.

이번에는 배열 리스트를 처리한다고 하자. **제네릭은 불변(invariant)이다. 즉 서로 다른 타입 `Manager`와 `Employee`가 있을 때 `ArrayList<Manager>`는 `ArrayList<Employee>`의 하위 타입 혹은 상위 타입이 아니다.** 따라서 다음 문장은 작성하도록 컴파일러는 허락하지 않는다.

```java
ArrayList<Manager> bosses = new ArrayList<>();
ArrayList<Employee> empls = bosses; // 규칙에 어긋난다.
```

ArrayList<Manager>를 ArrayList<Employee> 타입 변수에 할당할 수 있게 하면, 관리자가 아닌 직원을 저장할 때 배열 리스트에 손상을 줄 수 있다. 물론 자바는 `ArrayList<Manager>`를 `ArrayList<Employee>`로 변환하는 것은 허용하지 않으므로 이 오류는 일어날 수 없다.

이런 제약에는 이유가 있다. 다음은 문법상 허용되는 코드다.

```java
Object[] objectArray = new Long[1];
objectArray[0] = "타입이 달라 넣을 수 없다."; // ArrayStoreException을 던진다
```

하지만 다음 코드는 문법에 맞지 않는다.

```java
List<Object> ol = new ArrayList<Long>();
ol.add("타입이 달라 넣을 수 없다.");
```

어느 쪽이든 Long용 저장소에 String을 넣을 수는 없다. 다만 **배열에서는 그 실수를 런타임에야 알게 되지만, 리스트를 사용하면 컴파일할 때 바로 알 수 있다.** 여러분도 물론 컴파일 시에 알아채는 쪽을 선호할 것이다.

### 배열은 실체화(reify)된다

두 번째 주요 차이로, 배열은 실체화(reify)된다. 무슨 뜻인고 하니, **배열은 런타임에도 자신이 담기로 한 원소의 타입을 인지하고 확인한다.** 그래서 위의 코드에서 보듯 Long 배열에 String을 넣으려 하면 ArrayStoreException이 발생한다. 반면, 앞서 이야기했듯 **제네릭은 타입 정보가 런타임에는 소거(ensure)된다. 원소 타입을 컴파일 타임에만 검사하며 런타임에는 알수조차 없다는 뜻이다.** 소거는 제네릭이 지원되기 전의 레거시 코드와 제네릭 타입을 함께 사용할 수 있게 해주는 메커니즘으로, 자바 5가 제네릭으로 순조롭게 전환될 수 있도록 해줬다.

### 정리

배열과 제네릭에는 매우 다른 타입 규칙이 적용된다. 배열은 공변이고 실체화되는 반면, 제네릭은 불공변이고 타입 정보가 소거된다. 그 결과 배열은 런타임에는 타입 안전하지만 컴파일타임에는 그렇지 않다. 제네릭은 반대다. 그래서 둘을 섞어 쓰기란 쉽지 않다. 둘을 섞어 쓰다가 컴파일 오류나 경고를 만나면, 가장 먼저 배열을 리스트로 대체하는 방법을 적용해보자.

### 참조

<가장 빨리 만나는 코어 자바9, 카이 호스트만, 길벗>

<이펙티브 자바 3/E, 조슈아 블로크, 인사이트>
