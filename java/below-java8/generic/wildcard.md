# Wildcard

## 와일드카드는 왜 사용할까?

지네릭의 매개변수화 타입은 불공변(invariant)이다. 하지만 때론 불공변 방식보다 유연한 무언가가 필요하다.

Stack 클래스를 떠올려보자. 아래는 `Stack`의 public API이다.

```java
public class Stack<E> {
    public Stack();
    public void push(E e);
    public E pop();
    public boolean isEmpty();
}
```

여기에 일련의 원소를 스택에 넣는 메서드를 추가해야 한다고 해보자.

```java
public void pushAll(Iterable<E> src) {
    for (E e : src)
        push(e);
}
```

이 메서드는 깨끗이 컴파일되지만 완벽하진 않다. `Iterable` `src`의 원소 타입이 스택의 원소 타입과 일치하면 잘 작동한다. 하지만 `Stack<Number>`로 선언한 후 `pushAll(intVal)`을 호출하면 어떻게 될까? 여기서 `IntVal`은 `Integer`타입이다.

`Integer`는 `Number`의 하위 타입이니 잘 동작한다. 아니 논리적으로는 잘 동작해야할 것 같다.

```java
Stack<Number> numberStack = new Stack<>();
Iterable<Integer> integers = ...;
numberStack.pushAll(integers);
```

하지만 실제로는 다음의 오류 메시지가 뜬다. 매개변수화 타입이 불공변이기 때문이다.

```java
StackTest.java7: error: incompatible types: Iterable<Integer>
cannot be converted to Iterable<Number>
    numberStack.pushAll(integers);
```

자바는 이런 상황에 대처할 수 있는 **한정적 와일드카드 타입**이라는 특별한 매개변수화 타입을 지원한다. **`pushAll`의 입력 매개변수 타입은 ‘`E`의 `Iterable`'이 아니라 ‘`E`의 서브 타입의 `Iterable`’이어야 하며, 와일드카드 타입 `Iterable<? extends E>`가 정확히 이런 뜻이다.** 와일드카드 타입을 사용하도록 `pushAll` 메서드를 수정해보자.

```java
public void pushAll(Iterable<? extends E> src) {
    for (E e : src)
        push(e);
}
```

이번 수정으로 `Stack`은 물론 이를 사용하는 클라이언트 코드도 말끔히 컴파일된다. `Stack`과 클라이언트 모두 깔끔히 컴파일되었다는 건 모든 것이 타입 안전하다는 뜻이다.

## 한정적 와일드카드

### 서브타입 와일드카드(Upper Bounded Wildcards)

메서드에서 **배열 리스트에 쓰기를 전혀 수행하지 않아 인수로 전달된 배열 리스트를 변경하지 않는다**고 하자. 다음과 같이 와일드카드로 이런 사실을 나타낼 수 있다.

```java
public static void printNames(ArrayList<? extends Employee> staff) {
    for (int i = 0; i < staff.size(); i++) {
        Employee e = staff.get(i);
        System.out.println(e.getName());
    }
}
```

와일드카드 타입 `<? extends Employee>`은 미지의 `Employee` 서브타입을 가리킨다. 그래서 `ArrayList<Manager>`나 `ArrayList<Employee>` 같은 서브타입의 배열 리스트로 `printNames` 메서드를 호출할 수 있다.

만약 `ArrayList<? extends Employee>`타입의 변수 `staff`에 요소를 저장하면 어떻게 될까? 예를 들어 이 메서드에 `Employee`의 하위 타입인 `Manager` 객체를 전달하면 이를 컴파일러가 거부한다.

```java
staff.add(manager1);
```

`add` 메서드의 매개변수 타입은 `? extends Employee`이므로 **이 메서드에 전달할 수 있는 객체는 없다**(물론 `null`을 전달할 수 있지만, `null`은 객체가 아니다.). **요약하자면 `? extends Employee`를 `Employee`로 변환할 수 있지만, 그 어떤 것도 `? extends Employee`로는 변환할 수 없다. 바로 이 점이 `ArrayList<? extends Employee>`에서 읽을 수는 있지만, 쓸 수는 없는 이유다.**

### 슈퍼타입 와일드카드(Lower Bounded Wildcards)

위 ‘와일드카드는 어디에 사용할까’의 `Stack` 예시를 이어가서 `pushAll`과 짝을 이루는 `popAll` 메서드를 작성해 보자. `popAll` 메서드는 `Stack` 안의 모든 원소를 주어진 컬렉션으로 옮겨 담는다. 

```java
public void popAll(Collection<E> dst){
	while(!isEmpty())
		dst.add(pop());
}
```

이번에도 주어진 컬렉션의 원소 타입이 스택의 원소 타입과 일치한다면 말끔히 컴파일되고 문제없이 동작한다. 하지만 이번에도 역시나 완벽하진 않다. **Stack<Number>의 원소를 Object용 컬렉션으로 옮기려 한다고 해보자.** 컴파일과 동작 모두 문제없을 것 같다. 정말 그럴까?

```java
Stack<Number> numberStack = new Stack<>();
Collection<Object> objects = ...;
numberStack.popAll(objects);
```

이 클라이언트 코드를 앞의 `popAll` 코드와 함께 컴파일하면 “Collection<Object>는 Collection<Number>의 하위 타입이 아니다”라는 오류가 발생한다. **모든 제네릭 타입과 마찬가지로 `Collection`은 불변**이므로 `Collection<Object>`과 `Collection<Number>` 사이에는 아무런 관계가 없다. 

이번에도 와일드카드 타입으로 해결할 수 있다. 이번에는 `**popAll`의 입력 매개변수의 타입이 ‘`E`의 `Collection`’이 아니라 ‘`E`의 상위 타입의 `Collection`’이어야 한다(모든 타입은 자기 자신의 상위 타입이다)**. 와일드카드 타입을 사용한 `**Collection<? super E>`가 정확히 이런 의미**다. 이름 `popAll`에 적용해보자.

```java
public void popAll(Collection<? super E> dst){
	while(!isEmpty())
		dst.add(pop());
}
```

이제 Stack과 클라이언트 코드 모두 말끔히 컴파일 된다.

슈퍼타입 와일드카드는 보통 함수형 객체에서 매개변수로 유용하다. **제네릭 함수형 인터페이스를 메서드 매개변수로 받을 때는 `super 와일드카드`를 사용해야 한다. 모든 타입은 자기 자신의 상위 타입이기 때문이다.**

```java
public static void printAll(Employee[] staff, Predicate<? super Employee> filter){
    for(Employee e : staff)
        if(filter.test(e))
            System.out.println(e.getName());
}
```

다음과 같이 `Predicate<Object>`를 사용한다고 하자. **이렇게 해도 문제가 없어야 한다.**

```java
Predicate<Object> evenLength = e → e.toString().length() % 2 == 0;
printAll(employees, evenLength);
```

### 한정적 와일드카드 타입을 사용하는 기본 원칙

메시지는 분명하다. 유연성을 극대화하려면 원소의 생산자나 소비자용 입력 매개변수에 와일드카드 타입을 사용하라. 한편, 입력 매개변수가 생산자와 소비자 역할을 동시에 한다면 와일드카드 타입을 써도 좋을 게 없다. 타입을 정확히 지정해야 하는 상황으로, 이때는 와일드카드 타입을 쓰지 말아야 한다.

**다음 공식을 외워두면 어떤 와일드카드 타입을 써야 하는지 기억하는 데 도움이 될 것이다.**

> PECS : producer-extends, consumer-super
> 

즉, 매개변수화 타입 `T`가 생산자라면 `<? extends T>`를 사용하고, 소비자라면 `<? super T>`를 사용하라. `Stack` 예에서 `pushAll`의 `src` 매개변수는 `Stack`이 사용할 `E` 인스턴스를 생산하므로 src의 적절한 타입은 `Iterable<? extends E>`이다. 한편, `popAll`의 `dst` 매개변수는 `Stack`으로부터 `E` 인스턴스를 소비하므로 `dst`의 적절한 타입은 `Collection<? super E>`이다. **PECS** 공식은 와일드카드 타입을 사용하는 기본 원칙이다**.** 나프탈린(Naftalin)과 와들러(Wadler)는 이를 ‘Get and Put Principle’으로 부른다.

## 비한정적 와일드카드(Unbounded Wildcards)

**아주 일반적인 연산만 수행하는 상황에서는 경계 없는 와일드카드를 사용할 수 있다.** 예를 들어 다음 코드는 `ArrayList`에 `null` 요소가 들어 있는지 검사하는 메서드다.

```java
public static boolean hasNulls(ArrayList<?> elements) {
    for (Object e : elements) {
        if (e == null) return true;
    }
    return false;
}
```

**여기서 `ArrayList`의 타입 매개변수는 중요하지 않으므로 `ArrayList<?>`를 사용하는 것이 타당**하다. 물론 다음과 같이 `hasNulls`를 제네릭 메서드로 만들 수도 있다.

```java
public static boolean hasNulls(ArrayList<T> elements)
```

**하지만 와일드카드가 더 이해하기 쉬우므로 와일드카드를 이용한 방법이 더 적절하다.**

### 와일드카드 캡처(wildcard capture)

와일드카드를 이용해 swap 메서드를 정의해 보자.

```java
public static void swap(ArrayList<?> elements, int i, int j) {
    ? temp = elements.get(i); // 작동하지 않는다.
    elements.set(i, elements.get(j));
    elements.set(i, temp);
}
```

이렇게 작성하면 작동하지 않는다. `?`를 타입 인수로 사용할 수 있지만, 타입으로는 사용할 수 없다. 하지만 이 문제를 우회해서 해결하는 방법이 있다. 다음과 같이 헬퍼 메서드를 추가하면 된다.

```java
public static void swap(ArrayList<?> elements, int i, int j) {
    swapHelper(elements, i, j);
}
```

```java
public static void swapHelper(ArrayList<T> elements, int i, int j) {
    T temp = elements.get(i); // 작동하지 않는다.
    elements.set(i, elements.get(j));
    elements.set(i, temp);
}
```

**와일드카드 캡처(wildcard capture)**라는 특별한 규칙 덕분에 swapHelper 호출은 유효하다. 컴파일러는 `?`가 무엇인지 모르지만, `?`는 어떤 타입을 나타내므로 제네릭 메서드를 호출해도 된다. swapHelper 메서드의 `T`타입 매개변수는 와일드카드 타입을 '캡처(capture)'한다. swapHelper는 매개변수에 와일드카드가 포함된 메서드가 아니라 제네릭 메서드이므로 T타입 변수로 변수를 선언할 수 있다.

와일드카드 캡처로 얻을 수 있는 이점은 뭘까? **API 사용자가 제네릭 메서드 대신 이해하기 쉬운 `ArrayList<?>`를 볼 수 있다는 이점을 얻을 수 있다.**

---

### 참조

<카이 호스트만, 가장 빨리 만나는 코어 자바9, 길벗>

<조슈아 블로크, 이펙티브 자바 3/E, 인사이트>
