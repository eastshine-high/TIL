# Data type 자료형

## 동적 타입 언어(dynamic typed language)

실행할 때 변수에 저장된 데이터 타입을 동적으로 바꿀 수 있는 언어를 가리켜 **동적 타입 언어(dynamic typed language)**라고 부릅니다. 자바스크립트는 동적 타입 언어이므로 프로그램을 실행할 때 발생하는 타입 변환에 주의하여 변수에 어떤 타입의 데이터가 저장되는지 잘 확인해야 합니다.

반대로 C나 Java 등의 프로그래밍 언어에는 정수 타입 변수, 부동소수점 타입 변수 등이 있어서 그 변수의 타입과 일치하는 데이터만 저장할 수 있습니다. 이처럼 변수에 타입이 있는 언어를 가리켜 **정적 타입 언어(static typed language)**라고 부릅니다.

<br>

## 데이터 타입의 분류

자바스크립트가 처리할 수 있는 데이터 타입은 크게 두 가지로 나눌 수 있습니다. 바로 원시 타입(primitive type)과 객체 타입입니다.

**원시 타입에 속하는 값에는 `숫자`, `문자열`, `논리값`이 있습니다. 특수한 값(`undefined`, `null`)과 `Symbol`도 원시 타입에 속합니다.** Symbol은 ECMAScript 6부터 새로 추가된 값입니다. 원시 타입 데이터는 데이터를 구성하는 가장 기본적인 요소로 불변 값(값을 바꿀 수 없는 데이터)으로 정의되어 있습니다. 원시 타입의 값은 원시 값이라고 합니다. 원시 값을 변수에 대입하면 변수에 그 값이 저장됩니다.

원시 타입에 속하지 않는 자스스크립트의 값은 객체라고 합니다. 객체는 변수 여러 개가 모여서 만들어진 복합 데이터 타입입니다. 객체 안에 저장된 값은 바꿀 수 있습니다. 객체는 참조타입입니다. 따라서 객체 타입의 값을 변수에 대입하면 변수에는 그 객체에 대한 참조(메모리에서의 위치 정보)가 할당됩니다. **자바스크립트에서는 `배열`, `함수`, `정규 표현식`과 같은 다양한 요소가 객체입니다.**

<br>

### 숫자

대다수의 프로그래밍 언어에는 정수 타입과 부동소수점 타입이 따로 있지만 자바스크립트에는 타입이 없으므로 숫자를 모두 64비트 부동소수점으로 표현합니다. 이는 C나 Java에서 사용하는 double 타입에 해당합니다. 

표현할 수 있는 최댓값은 다음과 같습니다.

1.7976931348623157 X <!-- $10^{308}$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=10%5E%7B308%7D">

표현할 수 있는 최솟값은 다음과 같습니다.

4.940656458412465 X <!-- $10^{-324}$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=10%5E%7B-324%7D">

정수 값은 <!-- $-2^{53}$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=-2%5E%7B53%7D">= -9007199254740992 ~ <!-- $2^{53}$ --> <img style="transform: translateY(0.1em); background: white;" src="https://render.githubusercontent.com/render/math?math=2%5E%7B53%7D">=9007199254740992 범위의 값을 정확하게 처리할 수 있습니다. 단, 배열 인덱스와 비트 연산만큼은 32비트 정수로 처리합니다.

<br>

### 템플릿 리터럴

템플릿 리터럴은 ECMAScript 6부터 추가된 문자열 표현 구문입니다. 템플릿이란 일부만 변경해서 반복하거나 재사용할 수 있는 틀을 말합니다. 템플릿 리터럴을 사용하면 표현식의 값을 문자열에 추가하거나 여러 줄의 문자열을 표현할 수 있습니다.

- 템플릿 리터럴은 역따옴표(```)로 묶은 문자열입니다.
- 문자열 리터럴에서 줄 바꿈 문자를 표현할 때는 이스케이프 시퀀스(`\n`)를 사용했지만, 템플릿 리터럴을 사용하면 일반적인 줄 바꿈 문자를 사용할 수 있습니다.

```jsx
var t = `Man errs as long as
he strives.`;
```

- 물론 템플릿 리터럴에서도 이스케이프 시퀀스를 사용할 수 있습니다.

```jsx
var t = `Man errs as long as\nhe strives.`;
```

- 이스케이프 시퀀스 문자를 그대로 출력하려면 템플릿 리터럴 앞에 String.raw를 붙입니다.
- 템플릿 리터럴 앞에 붙은 String.raw는 태그 함수라고 부릅니다.

```jsx
var t = String.raw`Man errs as long as\nhe strives.`;
```

- 이 문자열을 문자열 리터럴로 표현하면 다음과 같은 모습이 됩니다.

```jsx
var t = `Man errs as long as\\nhe strives.`;
```

- 탬플릿 리터럴 안에는 플레이스 홀더를 넣을 수 있습니다. **플레이스 홀더**는 `${...}` 로 표기합니다. 자바스크립트 엔진은 플레이스 홀더 안에 든 ... 부분을 표현식으로 간주하여 평가(evaluation)합니다. 이를 활용하여 문자열 안에 변수나 표현식의 결괏값을 삽입할 수 있습니다. 예를 살펴보면 다음과 같습니다. 이를 **보간 표현식**이라 합니다.

```jsx
var a = 2, b = 3;
console.log(`${a} + ${b} = ${a+b}`); // -> 2 + 3 = 5
var now = new Data();
console.log(`오늘은 ${now.getMonth() + 1} 월 ${now.getDate()} 일입니다.`);
// -> 오늘은 12월 1일입니다.
```

<br>

### 논리값

논리값에는 `true`와 `false`, 두 가지 종류가 있습니다.

<br>

### null, undefined

값이 없음을 표현하기 위한 특수한 값에는 null과 undefined가 있습니다.

`undefined`는 정의되지 않은 상태를 뜻하여 다음과 같은 값이 undefined가 됩니다.

- 값을 아직 할당하지 않은 변수의 값
- 없는 객체의 프로퍼티를 읽으려고 시도했을 때의 값
- 없는 배열의 요소를 읽으려고 시도했을 때의 값
- 아무것도 반환하지 않는 함수가 반환하는 값
- 함수를 호출했을 때 전달받지 못한 인수의 값

이때 **변수 값이 undefined가 되는 것은 값을 할당하지 않은 결과라는 사실을 기억하세요. 코드로 undefined를 대입한 것이 아니라 자바스크립트 엔진이 변수를 undefined로 초기화한 것입니다.** 또한 자바스크립트 전역 변수에도 똑같은 이름의 undefined가 있고, 그 초깃값은 undefined라는 원시 값입니다. ECMAScript 5에서는 전역 변수 undefined 값을 수정할 수 없습니다.

**`null`은 아무것도 없음을 값으로 표현한 리터럴입니다.** null은 주로 프로그램에서 무언가를 검색했지만 찾지 못했을 때 아무것도 없을을 전달하기 위한 값으로 사용됩니다. 따라서 값을 읽을 때 그 값이 null인지 아닌지 확인해야 하는 상황이 자주 발생합니다.

<br>

### Symbol

ECMAScript 6부터 새롭게 추가된 원시 값입니다. Symbol은 **자기 자신을 제외한 그 어떤 값과도 다른 유일무이한 값**입니다.

예를 들어 오셀로 게임을 만들 때 칸의 상태를 값으로 표현하는 코드를 작성한다고 가정해 보겠습니다.

지금까지는 다음 코드처럼 칸의 상태를 숫자와 같은 값으로 표현했습니다.

```java
var NONE = 0; // 칸에 돌이 놓여 있지 않은 상태
var BLACK = -1; // 칸에 검은 돌이 놓여 있는 상태
var WHITE = 1; // 칸에 흰 돌이 놓여 있는 상태
```

이 코드에서 숫자 자체는 특별한 의미가 없습니다. 칸의 상태를 cell 변수에 저장한다고 가정했을 때, cell 값을 확인하려면 cell == WHITE라고 작성해야 프로그램이 읽기 쉬워질 것입니다. 게다가 cell == 1이라고 작성해도 아무런 문제없이 동작합니다. 그러나 이러한 행위는 프로그램을 읽기 어렵게 만드므로 바람직하지 않습니다. Symbol을 활용하면 앞의 코드를 다음처럼 고칠 수 있습니다.

```java
var NONE = Symbol("none");
var BLACK = Symbol("black");
var WHITE = Symbol("white");
```

Symbol은 유일무이한 값입니다. 따라서 이렇게 수정하면 변수 cell 값을 확인할 때 NONE, BLACK, WHITE만 사용하도록 제한할 수 있습니다.

**Symbol's Reference**

[https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)

<br>

---

### Reference

<모던 자바스크립트 입문, 이소 히로시, 길벗>
