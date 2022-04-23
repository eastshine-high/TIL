### 자바가 지원하는 예외 처리 방법에 대해서 알아보자.

- 예외 던지기(`throw`)
- 예외 잡기(`catch`)
- try-with-resources
- `finally`

## 예외 던지기

메서드가 해야 할 일을 할 수 없는 상황에 빠질 수도 있다. 어쩌면 필요한 리소스가 없거나 부적합한 매개변수를 받아서 그럴지도 모른다. 이런 상황에서는 예외를 던지는 것이 최선이다.

두 경곗값 사이에 있는 임의의 정수를 돌려주는 메서드를 구현한다고 하자.

```java
public static int randInt(int low, int high) {
	return low + (int) (Math.randow() * (high - low + 1));
}
```

누군가 randInt(10, 5)를 호출한다면 어떻게 해야 할까? 이 문제를 고치려는 생각은 좋지 않다. 호출하는 방법이 두 가지 이상이면 호출하는 쪽에서 혼란을 겪을 수 있기 때문이다. 그 대신 다음과 같이 적절한 예외를 던지자.

```java
If(low > high)
	throw new IllegalArgumentException(
		String.format("low should be <= high but low is %d and high is %d", low, high))
```

코드에서 볼 수 있듯이 `throw` 문으로 예외 클래스(이 코드에서는 `IllegalArgumentException`의 객체를 ‘던진다.’ 여기서는 디버깅 메시지를 생성 인수로 전달해 예외 객체를 생성했다.

`throw` 문이 실행되면 정상적인 실행 흐름이 즉시 중단된다. 이 예제에서는 `randInt` 메서드가 실행을 멈추고 , 호출하는 쪽에 값을 반환하지 않는다. 제어는 예외 핸들러로 전달된다.

## 예외 잡기

예외를 잡으려면 try 블록을 사용해야 한다. 다음은 try 블록을 사용한 가장 간단한 형태다.

```java
try {
	statements
} catch (ExceptionClass ex) {
	handler
}
```

try 블록에 들어 있는 문장(statements)을 실행하다가 주어진 예외 클래스(ExceptionClass)의 예외가 일어나면 제어가 핸들러(handler)로 이동한다. 예외 변수(이 예제에서는 ex)는 예외 객체를 참조하며, 핸들러는 필요하면 해당 예외 객체를 조사할 수 있다.

## try-with-resources 문

예외 처리가 필요한 문제 중 하나는 리소스 관리다. 다음과 같이 파일에 쓰기를 수행하고, 수행을 완료하면 파일을 닫는다고 하자.

```java
ArrayList<String> lines = ...;
PrintWriter out = new PrintWriter("output.txt");
for(String line : lines) {
	out.println(line.toLowerCase());
}
out.close();
```

이 코드에서 위험 요소가 숨어 있다. 어떤 메서드든 예외를 던지면 out.close()가 호출되지 않는다. 이런 상황은 좋지 않다. 출력을 유실할 수도 있고, 예외가 여러 번 일어나면 시스템이 파일 핸들(file handle)을 소진할 수도 있기 때문이다.

이런 위험 요소는 특별한 형태의 try 문으로 해결할 수 있다. try 문 헤더에 리소스(resource)를 지정할 수 있다. **리소스는 반드시 `AuthCloseable` 인터페이스를 구현하는 클래스에 속해야 한다.** 다음과 같이 try 블록 헤더에 변수를 선언하면 된다.

```java
ArrayList<String> lines = ...;
try(PrintWriter out = new PrintWriter("output.txt")){ // 변수 선언
	for(String line : lines) {
		out.println(line.toLowerCase());
	}
}
```

또는 이전에 선언된 사실상 최종 변수를 헤더에 넣어도 된다.

```java
PrintWriter out = new PrintWriter("output.txt")
try(out){ // 사실상 최종 변수
	for(String line : lines) {
		out.println(line.toLowerCase());
	}
}
```

`AutoCloseable` 인터페이스에는 `close` 메서드 하나만 선언되어 있다.

```java
public void close() throws Exception
```

> `Closeable`이라는 인터페이스도 있다. `Closeable`은 `AutoCloseable`의 서브인터페이스로 역시 `close` 메서드가 호출된다. 예를 들어 다음과 같다.
> 

정상적으로 try 블록의 끝에 이르렀든 예외가 일어났든 간에 try 블록이 끝날 때 리소스 객체의 close 메서드가 호출된다. 예를 들어 다음과 같다.

```java
try(PrintWriter out = new PrintWriter("output.txt")){ // 변수 선언
	for(String line : lines) {
		out.println(line.toLowerCase());
	}
} // out.close()가 호출된다.
```

여러 리소스를 세미콜론으로 구분해 선언할 수 있다. 다음은 리소스를 두 개 선언하는 예다.

```java
try(Scanner in = new Scanner(Paths.get("/usr/share/dict/words"));
			PrintWriter out = new PrintWriter("output.txt")){ // 변수 선언
	while(in.hasNext()) {
			out.println(in.next().toLowerCase());
	}
}
```

리소스는 초기화 순서의 역순으로 닫는다. 즉, out.close()가 in.close()보다 먼저 호출된다. PrintWriter 생성자에서 예외를 던진다고 하자. 이 시점에 in은 이미 초기화되었지만 out은 그렇지 않다. try 문은 이 상황을 제대로 처리한다. 즉, in.close()를 호출하고 예외를 전파한다.

일부 close 메서드는 예외를 던질 수 있다. try 블록이 정상적으로 끝난 후 이런 메서드에서 예외가 일어나면 호출하는 쪽으로 해당 예외를 던진다. 하지만 또 다른 예외가 일어나서 리소스들의 close 메서드가 호출되고, 그중 하나가 예외를 던지면 그 예외는 원래 일어난 예외보다 덜 중요하기 마련이다.

이런 상황에서는 원래 일어난 예외를 다시 던지고, close 호출로 일어난 예외를 잡아서 ‘억누른(suppressed)예외로 첨부한다. 이것은 아주 유용한 메커니즘이지만 손수 구현하기에는 번거롭다. 주 예외(primary exception)를 잡을 때 getSuppressed 메서드를 호출하면 부 예외(secondary exception)를 추출할 수 있다.

```java
try{
	...
} catch (IOException ex){
	Throwable[] secondaryExceptions = ex.getSuppressed();
}
```

이런 상황이 발생하지 않으면 좋겠지만, try-with-resources 문을 사용할 수 없는 상황에서 이런 메커니즘을 직접 구현하려면 ex.addSuppressed(secondaryException)을 호출해야 한다.

try-with-resources 문에도 필요에 따라 그 안에서 일어나는 예외를 잡는 catch 절을 붙일 수 있다.

## finally 절

앞에서 살펴보았듯이 try-with-resources 문은 예외 발생 여부와 상관없이 자동으로 리소스를 닫는다. 가끔은 AutoCloseable이 아닌 무언가를 정리해야 할 때도 있다. 이때는 finally 절을 사용하면 된다.

```java
try{
	... logic
} finally {
	// 정리 작업을 한다.
}
```

**finally 절은 정상적으로든 예외가 일어나서든 try 블록이 끝날 때 실행된다.** 이런 패턴은 잠금을 획득 - 해제하거나 카운터를 증가 - 감소시킬 때, 스택에 무언가를 넣었다가 작업을 마치고 꺼낼 때 사용한다. 어떤 예외가 일어나든 이런 동작을 하도록 해야 하기 때문이다.

**finally 절에서는 예외를 던지지 말아야 한다.** try 블록의 바디가 예외로 종료되더라도 finally 절에서 일어난 예외로 가려지기 때문이다. 앞에서 살펴본 예외 억제 메커니즘은 try-with-resources 문에서만 작동한다.

**이와 비슷한 이유로 finally 절에 return 문을 작성하면 안 된다.** try 문을 구성할 수도 있다. 하지만 finally 절에서 일어날 수 있는 예외에 주의해야 한다. 다음 try 블록을 살펴보자.

```java
BufferedReader in = null;
try {
		in = Files.newBufferedReader(path, StandardCharsets.UTF_8);
} catch (IOException ex) {
		System.err.println("Caught IOException: " + ex.getMessage());
} finally {
		if(in != null) {
				in.close(); // 주의 - 예외를 던질 수도 있다.
		}
}
```

프로그래머는 `Files.newBufferedReader` 메서드에서 예외를 던지는 상황을 분명히 고려했다. 이 코드는 모든 입출력 예외를 잡아서 출력할 것처럼 보인다. 하지만 실제로는 `in.close()`에서 던질 수도 있는 예외 하나를 놓친다. **보통은 복잡한 try/catch/finally 문을 try-with-resources 문이나 try/catch 문 안에 try/finally를 중첩하는 방법으로 다시 작성하는 것이 낫다.**

### 참조

<가방 빨리 만나는 코어 자바9, 카이 호스트만, 길벗>
