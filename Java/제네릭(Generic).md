# 제네릭(Generic)

### 제네릭(Generic) 프로그래밍

- 클래스에서 사용하는 변수의 자료형이 여러개 일수 있고, 그 기능(메서드)은 동일한 경우 클래스의 자료형을 특정하지 않고 추후 해당 클래스를 사용할 때 지정 할 수 있도록 선언하는 방식이다.
- 실제 사용되는 자료형의 변환은 컴파일러에 의해 검증되므로 안정적인 프로그래밍 방식이다.
- 컬렉션 프레임워크에서 많이 사용되고 있다.

<br>

### 제네릭 클래스(Generic class)
제네릭 클래스(generic class)는 타입 매개변수(type parameter)가 한 개 이상 있는 클래스다.

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

클래스 이름(Entry) 뒤에 오는 <> 안에 타입 매개변수 K와 V를 명시했다. 이 타입 매개변수들은 클래스 멤버를 정의할 때 **인스턴스 변수**, **메서드 매개변수**, **반환 값의 타입**으로 사용된다.

프로그래머는 타입 변수를 원하는 타입으로 교체해 제네릭 클래스를 인스턴스화(instantiate)한다. 예를 들어 `Entry<String, Integer>`는 `String getKey()`와 `Integer getValue()`메서드를 가진 평범한 클래스다.

제네릭 클래스의 객체를 생성(construct)할 때 생성자의 타입 매개변수를 생략할 수 있다.

```java
Entry<String, Integer> entry = new Entry<>("Joe", 35);
```

이때도 생성 인수 앞에 빈 <>를 작성해야 한다. 이 빈 <>를 다이아몬드(diamond)라고도 한다. 다이아몬드 문법을 사용하면 생성자의 타입 매개변수가 추론된다.

<br>

### 제네릭 메서드(Generic method)
제네릭 클래스가 타입 매개변수를 받는 클래스인 것처럼 제네릭 메서드(Generic method)는 타입 매개변수를 받는 메서드다. 제네릭 메서드는 일반 클래스나 제네릭 클래스에 속할 수 있다. 다음은 제네릭이 아닌 클래스에 속한 제네릭 메서드의 예다.

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

배열에 들어 있는 요소의 타입이 기본 타입만 아니라면 swap 메서드로 임의의 배열에 들어 있는 요소를 교환할 수 있다.

- 제네릭 메서드를 선언할 때는 타입 매개변수를 제어자(public이나 static 같은)와 반환 타입 사이에 두어야 한다.
- 제네릭 메서드를 호출할 때는 타입 매개변수를 명시하지 않안도 된다. 컴파일러가 메서드 매개변수와 반환 타입에서 타입 매개변수를 추론하기 때문이다.
- 타입을 명시적으로 지정하면 문제가 발생했을 때 더 자세한 오류 메세지를 얻을 수 있다.

<br>

### 타입 경계(type bound)
제네릭 클래스나 제네릭 메서드가 받는 타입 매개변수의 타입을 제한해야 할 때도 있다. 이때 타입 경계(type bound)를 지정하면 타입이 특정 클래스를 확장하거나 특정 인터페이스를 구현한 클래스로 제한할 수 있다.

예를 들어 AutoCloseable 인터페이스를 구현하는 클래스의 객체로 구성된 ArrayList가 있고, 이 리스트에 들어 있는 객체를 모두 닫는다고 하자.

```java
public static <T extends AutoCloseable> void closeAll(ArrayList<T> elems)
        throws Exception {
    for (T elem : elems) elem.close();
}
```

- 타입 경계 extends AutoCloseable은 요소 타입이 AutoCloseable의 서브타입임을 보장한다.

다음과 같이 타입 매개변수에 다중 경계를 지정할 수도 있다. `T extends Runnable & AutoCloseable`

- 인터페이스 경계는 원하는 개수만큼 지정할 수 있지만, 클래스 경계는 한 개만 지정할 수 있다.
- 클래스를 경계로 지정할 때는 경계 목록에서 첫 번째에 두어야 한다.