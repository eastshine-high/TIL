# Generic

제네릭은 런타임이 아닌 컴파일 타임에 타입을 체크함으로써 코드의 안정성을 높여주는 기능이다.

제네릭(generic)은 자바 5부터 사용할 수 있다. 제네릭을 지원하기 전에는 컬렉션에서 객체를 꺼낼 때마다 형변환을 해야 했다. 그래서 누군가 **실수로 엉뚱한 타입의 객체를 넣어두면 런타임에 형변환 오류가 나곤 했다.** 반면, 제네릭을 사용하면 컬렉션이 담을 수 있는 타입을 컴파일러에 알려주게 된다. 그래서 컴파일러는 알아서 형변환 코드를 추가할 수 있게 되고, 엉뚱한 타입의 객체를 넣으려는 시도를 컴파일 과정에서 차단하여 더 안전하고 명확한 프로그램을 만들어 준다. 꼭 컬렉션이 아니더라도 이러한 이점을 누릴 수 있으나, 코드가 복잡해진다는 단점이 따라온다.

다양한 타입에서 작동하는 메서드와 클래스를 구현해야 할 때가 종종 있다. 예를 들어 `ArrayList<T>`는 임의의 `T` 클래스를 요소로 저장한다. 여기서 `ArrayList` 클래스를 제네릭(generic)이라고 하며, `T`를 타입 매개변수라고 한다.

## 제네릭(Generic) 프로그래밍

- **클래스에서 사용하는 변수의 자료형이 여러개 일수 있고, 그 기능(메서드)은 동일한 경우** 클래스의 자료형을 특정하지 않고 추후 해당 클래스를 사용할 때 지정 할 수 있도록 선언하는 방식이다.
- 실제 사용되는 자료형의 변환은 컴파일러에 의해 검증되므로 안정적인 프로그래밍 방식이다.
- 컬렉션 프레임워크에서 많이 사용되고 있다.

## 제네릭 클래스(Generic class)

제네릭 클래스(generic class)는 **타입 매개변수(type parameter)가 한 개 이상 있는 클래스**다.

```java
public class Entry<K, V> {
    private K key;
    pirvate V value;

    public Entry(K key, V value) {
        this.key = key;
        this.value = value;
    }

    public K getKey() { return key; }
    public V getValue() { return value; }
}
```

클래스 이름(Entry) 뒤에 오는 `<>` 안에 타입 매개변수 `K`와 `V`를 명시했다. **이 타입 매개변수들은 클래스 멤버를 정의할 때 인스턴스 변수, 메서드 매개변수, 반환 값의 타입으로 사용된다.**

프로그래머는 타입 변수를 원하는 타입으로 교체해 제네릭 클래스를 인스턴스화(instantiate)한다. 예를 들어 `Entry<String, Integer>`는 `String getKey()`와 `Integer getValue()`메서드를 가진 평범한 클래스다.

제네릭 클래스의 객체를 생성(construct)할 때 생성자의 타입 매개변수를 생략할 수 있다.

```java
Entry<String, Integer> entry = new Entry<>("Joe", 35);
```

이때도 **생성 인수 앞에 빈 `<>`를 작성해야** 한다. 이 빈 `<>`를 다이아몬드(diamond)라고도 한다. **다이아몬드 문법을 사용하면 생성자의 타입 매개변수가 추론된다.**

## 제네릭 메서드(Generic method)

제네릭 클래스가 타입 매개변수를 받는 클래스인 것처럼 **제네릭 메서드(Generic method)는 타입 매개변수를 받는 메서드다.** 제네릭 메서드는 일반 클래스나 제네릭 클래스에 속할 수 있다. 

다음은 제네릭이 아닌 클래스에 속한 제네릭 메서드의 예다. **배열에 들어 있는 요소의 타입이 기본 타입만 아니라면 swap 메서드로 임의의 배열에 들어 있는 요소를 교환할 수 있다.**

```java
public class Arrays{
    public static <T> void swap(T[] array, int i, int j){
        T temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }
}

// call generic method
String[] friends = ...;
Arrays.<String>swap(friends, 0 ,1);
```

- 제네릭 메서드를 선언할 때는 타입 매개변수를 **제어자(public이나 static 같은)와 반환 타입 사이에** 두어야 한다.
- 제네릭 메서드를 호출할 때는 타입 매개변수를 명시하지 않아도 된다. 컴파일러가 메서드 매개변수와 반환 타입에서 타입 매개변수를 추론하기 때문이다.
- 타입을 명시적으로 지정하면 문제가 발생했을 때 더 자세한 오류 메세지를 얻을 수 있다.

## 타입 경계(type bound)

**제네릭 클래스나 제네릭 메서드가 받는 타입 매개변수의 타입을 제한해야 할 때**도 있다. 이때 타입 경계(type bound)를 지정하면 타입이 특정 클래스를 확장하거나 특정 인터페이스를 구현한 클래스로 제한할 수 있다.

예를 들어 `AutoCloseable` 인터페이스를 구현하는 클래스의 객체로 구성된 `ArrayList`가 있고, 이 리스트에 들어 있는 객체를 모두 닫는다고 하자.

```java
public static <T extends AutoCloseable> void closeAll(**ArrayList<T>** elems)
        throws Exception {
    for (T elem : elems) elem.close();
}
```

- 타입 경계 `**extends**` `AutoCloseable`은 요소 타입이 `AutoCloseable`의 '서브타입'임을 보장한다.

다음과 같이 타입 매개변수에 다중 경계를 지정할 수도 있다.

```java
T extends Runnable **&** AutoCloseable
```

- 인터페이스 경계는 원하는 개수만큼 지정할 수 있지만, **클래스 경계는 한 개만 지정할 수 있다.**
- **클래스를 경계로 지정할 때는 경계 목록에서 첫 번째에** 두어야 한다.

## 참조

<가방 빨리 만나는 코어 자바9, 카이 호스트만, 길벗>
