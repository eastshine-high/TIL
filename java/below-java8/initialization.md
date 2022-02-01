# 생성자와 변수의 초기화

## 생성자

- 인스턴스가 생성될 때 호출되는 '**인스턴스 초기화 메서드**'이다. 따라서 인스턴스 변수의 초기화 작업에 주로 사용되며, 인스턴스 생성 시에 실행되어야 할 작업을 위해서 사용된다.
- 구조는 메서드와 비슷하지만 리턴값이 없다는 점이 다르다.
- 생성자의 이름은 클래스의 이름과 같다.
- 생성자도 오버로딩이 가능하므로 하나의 클래스에 여러 개의 생성자가 존재할 수 있다.

## 변수의 초기화

변수를 선언하고 처음으로 값을 저장하는 것을 '변수의 초기화'라고 한다.

- 멤버변수는 초기화를 하지 않아도 자동적으로 변수의 자료형에 맞는 기본값으로 초기화가 이루어진다.
- 숫자는 0, 불 값은 false, 객체 참조는 null이 기본 값이다.
- 지역변수는 사용하기 전에 반드시 초기화해야 한다.

### 멤버변수의 초기화

멤버변수의 초기화는 지역변수와 달리 여러 가지 방법이 있다.

1. 명시적 초기화  2. 초기화 블럭 3. 생성자

**명시적 초기화**

변수를 선언과 동시에 초기화하는 것을 명시적 초기화라고 한다.

```java
class Card{
	int number = 1; // 기본형 변수의 초기화
	Position p = new Position(10, 20); // 참조형 변수의 초기화
}
```

명시적 초기화가 간단하고 명료하긴 하지만, 보다 복잡한 초기화 작업이 필요한 때는 '초기화 블럭(initialization block)' 또는 생성자를 사용해야 한다.

**초기화 블럭**

클래스 초기화 블럭 - 클래스변수의 복잡한 초기화에 사용된다.

인스턴스 초기화 블럭 - 인스턴스변수의 복잡한 초기화에 사용된다.

초기화 블록은 자주 사용하는 기능은 아니다. 대부분의 개발자는 긴 초기화 코드를 [헬퍼 메서드](https://www.quora.com/How-do-I-create-helper-methods-in-Java) 안에 두고, 생성자에서 헬퍼 메서드를 호출한다.

### 초기화가 수행되는 시기와 순서

클래스 변수의 초기화 시점 : 클래스가 처음 로딩될 때 단 한번 초기화 된다.

인스턴스 변수의 초기화 시점 : 인스턴스가 생성될 때마다 각 인스턴스별로 초기화가 이루어진다.

클래스변수의 초기화 순서 : 기본값 → 명시적 초기화 → 클래스 초기화 블럭

인스턴스변수의 초기화 순서 : 기본값 → 명시적초기화 → 인스턴스 초기화 블럭 → 생성자

```java
class BlockTest{
	static int staticValue = 1; // 기본값
				 int instanceValue = 1;
	static{
		System.out.println("static { }"); // 클래스 초기화 블럭
		staticValue = 2;
	}

	{
		System.out.println("{ }"); // 인스턴스 초기화 블럭
		++staticValue;
		instanceValue = staticValue;
	}

	public BlockTest() {
		System.out.println("생성자");
	}

	public static void main(String args[]) {
		System.out.println("BlockTest bt = new BlockTest();");
		BlockTest bt = new BlockTest();

		System.out.println("BlockTest bt2 = new BlockTest();");
		BlockTest bt2 = new BlockTest();
	}
}
```

실행 결과

```
static { }
BlockTest bt = new BlockTest();
{ }
생성자
BlockTest bt2 = new BlockTest();
{ }
생성자

값
bt.instanceValue = 3
bt2.instanceValue = 4
```

### 참조

<남궁성, 자바의 정석, 도우출판>

<카이 호스트만, 가장 빨리 만나는 코어 자바 9, 길벗>

<style>
h1, h2, h3{
    padding-top:7px;
}
</style>
