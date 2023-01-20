# Nullish coalescing operator

이번 페이지에서는 Nullish coalescing operator(null 병합 연산자)를 알아보겠습니다. 그리고 이와 비슷한 default parameter와 Logical OR operator를 함께 비교해 보도록 하겠습니다.

## Nullish coalescing operator(null 병합 연산자)

`null` 혹은 `undefined` 인 경우에 rightExpr을 실행합니다.

```bash
leftExpr(null, undefined) ?? rightExpr
```

- Expr이기 때문에 값을 직접적으로 할당할 때 사용하기도 하지만 Expr을 실행할 경우에도 이용을 할 수 있습니다.

- ES2020에서 사용할 수 있습니다. 바벨 같은 컴파일러를 사용하면 버전 7부터 기본적으로 지원합니다.

다음 코드는 Nullish coalescing operator를 적용하기 전의 코드입니다.

```jsx
function printMessage(text){
  let message = text;
  if(text == null || text == undefined){
    message = 'Nothing to display';
  }
  console.log(text);
}
```

위의 코드를 Nullish coalescing 연산자를 이용하여 개선합니다.

```jsx
function printMessage(text){
  let message = text ?? 'Noting to display';
  console.log(text);
}

printMessage('Hello');
printMessage(null);
printMessage(undefined);

실행결과
Hello
Nothing to display
Nothing to display
```

## default 파라미터

default 파라미터는 `undefined` 일 경우에만 적용할 수 있습니다. 값이 `null` 로 명확하게 지정되어있는 경우에는 default 파라미터에 해당되지 않습니다. 

```jsx
function printMessage(text = 'Nothing to display'){
  console.log(text);
}

printMessage('Hello');
printMessage(undefined);
printMessage(null);

실행결과
Hello
Nothing to display
null
```

## Logical OR operator

`falsy`에 해당할 경우 rightExpr을 실행합니다.

```jsx
leftExpr(falsy) || rightExpr
```

- `false`, `0`, `-0`, `NaN`, `undefined`, `null`, `""`, `''` 가 `falsy`에 해당합니다.
- Expr이기 때문에 값을 직접적으로 할당할 때 사용하기도 하지만 Expr을 실행할 경우에도 이용을 할 수 있습니다.

예제 코드

```jsx
function printMessage(text){
  let message = text || 'Noting to display';
  console.log(text);
}

printMessage('Hello');
printMessage(undefined);
printMessage(null);
printMessage(0);
printMessage('');

실행결과
Hello
Nothing to display
Nothing to display
Nothing to display
Nothing to display
```

---

참조

<드림코딩, [자바스크립트 프로처럼 쓰는 팁](https://www.youtube.com/watch?v=BUAhpB3FmS4)>
