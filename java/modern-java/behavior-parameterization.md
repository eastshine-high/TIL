# 동작 파라미터화

우리가 어떤 상황에서 일을 하든 소비자 요구사항은 항상 바뀐다. 동작 파라미터화(behavior parameterization)을 이용하면 자주 바뀌는 요구사항에 효과적으로 대응할 수 있다. **동작 파라미터화란 아직은 어떻게 실행할 것인지 결정하지 않은 코드 블록을 의미한다.** 이 코드 블록은 나중에 프로그램에서 호출한다. 즉, 코드 블록의 실행은 나중으로 미뤄진다. 예를 들어 나중에 실행될 메서드의 인수로 코드 블록을 전달할 수 있다. **결과적으로 코드 블록에 따라 메서드의 동작이 파라미터화된다.** 

예를 들어 컬렉션을 처리할 때 다음과 같은 메서드를 구현한다고 가정하자.

- 리스트의 모든 요소에 대해서 어떤 동작을 수행할 수 있음
- 리스트 관련 작업을 끝낸 다음에 어떤 다른 동작을 수행할 수 있음
- 에러가 발생하면 '정해진 어떤 다른 동작'을 수행할 수 있음

동작 파라미터화로 이처럼 다양한 기능을 수행할 수 있다. 이해가 잘 가지 않을 수 있다. 변화하는 요구사항에 유연하게 대응할 수 있게 코드를 구현하는 과정을 예제를 이용해서 살펴보며 이해해 보자.

<br>

기존의 농장 재고목록 애플리케이션에 리스트에서 녹색 사과만 필터링하는 기능을 추가한다고 가정하자. 비교적 간단한 작업이라는 생각이 들 것이다.

```java
pulbic static List<Apple> filterGreenApples(List<Apple> inventory) {
    List<Apple> result = new ArrayList<>();
    for(Apple apple: inventory){
        if(GREEN.equals(apple.getColor()){
            result.add(apple);
        }
    }
}
```

그런데 갑자기 농부가 변심하여 녹색 사과 말고 빨간 사과도 필터링하고 싶어졌다. 어떻게 해야 `filterGreenApples`의 코드를 반복 사용하지 않고 `filterRedApples`를 구현할 수 있을까? 색을 파라미터화할 수 있도록 메서드에 파라미터를 추가하면 변화하는 요구사항에 좀 더 유연하게 대응하는 코드를 만들 수 있다.

```java
pulbic static List<Apple> filterApplesByColor(List<Apple> inventory, Color color) // 구현부 생략
```

그런데 갑자기 농부가 색 이외에도 가벼운 사과와 무거운 사과로 구분할 수 있다면 좋겠다고 요구한다. 그래서 다양한 무게에 대응할 수 있는 무게 정보 파라미터가 있는 메서드를 만들었다.

```java
pulbic static List<Apple> filterApplesByWeight(List<Apple> inventory, int weight) // 구현부 생략
```

하지만 이 메서드들은 목록을 검색하고 각 사과에 필터링 조건을 적용하는 부분의 코드가 중복된다. 이는 소프트웨어 공학의 DRY 원칙을 어기는 것이다. 탐색 과정을 고쳐서 성능을 개선하려면 한 줄이 아니라 메서드 전체 구현을 고쳐야 한다.

색과 무게를 filter라는 메서드로 합치는 방법도 있다. 그러면 어떤 기준으로 사과를 필터링할 지 구분하는 또 다른 방법이 필요하다. 따라서 색이나 무게 중 어떤 것을 기준으로 필터링할지 가리키는 플래그를 추가할 수 있다. 

```java
pulbic static List<Apple> filterApplesByWeight(
    List<Apple> inventory,
    Color coler,
    int weight,
    boolean flag
)
// 구현부 생략
```

형편없는 코드다. 대체 true와 false는 뭘 의미하는 걸까? 게다가 앞으로 요구사항이 바뀌었을 때 유연하게 대응할 수도 없다. 문제가 잘 정의되어 있는 상호아에서는 이 방법이 잘 동작할 수 있다. 하지만 filterApples에 어떤 기준으로 사과를 필터링할 것인지 효과적으로 전달할 수 있다면 더 좋을 것이다. **동작 파라미터화**를 이용해서 유연성을 얻어보자.

우리의 선택 조건을 다음처럼 결정할 수 있다. 사과의 어떤 속성에 기초해서 불리언값을 반환(예를 들어 사과가 녹색인가? 150그램 이상인가?)하는 방법이 있다. 참 또는 거짓을 반환하는 함수를 프레디케이트라고 한다. 선택 조건을 결정하는 인터페이스를 정의하자.

```java
public interface ApplePredicate{
    boolean test(Apple apple)
}
```

다음 예제처럼 다양한 선택 조건을 대표하는 여러 버전의 ApplePredicate를 정의할 수 있다.

```java
public class AppleHeavyWeightPredicate implements ApplePredicate {
    pulbic boolean test(Apple apple){
        return apple.getWeight() > 150;
    }
}
```

위 조건에 따라 filter 메서드가 다르게 동작할 것이라고 예상할 수 있다. 이를 전략 디자인 패턴(strategy pattern)이라고 부른다. 전략 디자인 패턴은 각 알고리즘(전략이라 불리는)을 캡슐화하는 알고리즘 패밀리를 정의해둔 다음에 런타임에 알고리즘을 선택하는 기법이다. 위 예제에서는 ApplePredicate가 알고리즘 패밀리고 AppleHeavyWeightPredicated와 AppleGreenColorPredicate가 전략이다.

그런데 ApplePredicate는 어떻게 다양한 동작을 수행할 수 있을까? **filterApples에서 ApplePredicate 객체를 받아 애플의 조건을 검사하도록 메서드를 고쳐야 한다. 이렇게 동작 파라미터화, 즉 메서드가 다양한 동작(또는 전략)을 받아서 내부적으로 다양한 동작을 수행할 수 있다.** 이제 filterApples 메서드가 ApplePredicate 객체를 인수로 받도록 고치자. 

```java
public static List<Apple> filterApple(List<Apple> inventory, ApplePredicate p){
    List<Apple> result = new ArrayList<>();
    for(Apple apple : inventory) {
        if(p.test(apple)){
            result.add(apple);
        }
    }
}
```

이렇게 하면 filterAplles 메서드 내부에서 **컬렉션을 반복하는 로직과 컬렉션의 각 요소에 적용할 동작(프레디케이트)을 분리**할 수 있다는 점에서 소프트웨어 엔지니어링적으로 큰 이득을 얻는다. 유연한 API를 만들 때 동작 파라미터화가 중요한 역할을 한다.

이제 필요한 대로 다양한 ApplePredicate를 만들어서 filterApples 메서드로 전달할 수 있다. **우리가 전달한 ApplePredicate 객체에 의해 filterApples 메서드의 동작이 결정된다. 즉, filterApples 메서드의 동작을 파라미터화한 것이다.**

filterApple 메서드를 사용할 때, ApplePredicate 객체(코드 블록)를 여러 방법으로 전달할 수 있다. 첫 번째, 클래스를 선언하여 생성한 인스턴스를 전달할 수 있다. 두 번째, 익명클래스를 이용하여 클래스를 선언과 인스턴스화를 동시에 하여 객체를 전달할 수 있다. 세 번째, [람다](https://github.com/eastshine-high/til/blob/main/java/modern-java/lambda.md) 표현식을 사용하여 코드 블록(객체)를 전달할 수 있다.

<br>

### Reference
<모던 자바 인 액션, 라울-게이브리얼 우르마 외, 한빛미디어>
