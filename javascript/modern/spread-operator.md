# Spread operator

스프레드(`...`) 연산자는 0개 이상의 아규먼트들(함수에서) 또는 엘러먼트들(배열 리터럴 `[]` 에서)이 기대되는 위치에 배열 또는 문자열과 같은 **이터러블**이 확장될 수 있도록 합니다. 객체 리터럴 `{}` 에서 스프레드 연산자는 객체의 속성을 열거하고 생성 중인 객체에 키-값 쌍을 추가합니다.

## 함수 아규먼트에서의 활용

전달 받은 아규먼트들 중에서 가장 큰  값을 반환하는 메서드인 `Math.max` 를 예로 들어보겠습니다.

```
max(… values: number[]): number
Numeric expressions to be evaluated.
Returns the larger of a set of supplied numeric expressions
```

이 메소드는 배열을 통해 동작시킬 수는 없습니다.

```jsx
Math.max([1, 2, 3])
> NaN
```

따라서 아규먼트를 하나하나 전달을 해야합니다. 이때 스프레드 연산자를 사용하여 배열 요소들을 전개하여 아규먼트로서 전달할 수 있습니다.

```jsx
Math.max(...[1, 2, 3])
> 3
```

## 두 개의 Object를 하나의 Object로 합치기

```jsx
const item = {type: 'a', size: 'M'};
const detail = {price: 20, national: 'Korea', gender : 'W'};
```

먼저 가장 안 좋은 방법은 아래와 같이 객체의 속성값을 하나하나 할당하는 방식입니다.

```jsx
// way 1
item['price'] = detail.price;
item['national'] = detail.national;
...

// way 2
const newObject = new Object();
newObject['type'] = item.type;
newObject['size'] = item.size;
...
```

스프레드 연산자를 이용해 두 객체를 합칠 수 있습니다.

```jsx
const shirt0 = {...item, ...detail, price: 40};
```

- `detail` 객체의 `price`의 값을 덮어쓰고 싶다면 뒤에 원하는 키-값을 추가하여 업데이트 할 수도 있습니다.

또한 기존 방식인 `Object.assign` 를 이용하여 객체를 합칠 수도 있습니다.

```jsx
const shirt = Object.assign(item, detail);
```

## 배열에서의 Spread 연산자 활용

```jsx
let fruits = ['apple', 'orange', 'banana'];
```

기존 배열에 요소를 추가하고 싶다면 `push`를 이용할 수 있습니다.

```jsx
fruits.push('melon');
```

기존의 배열을 활용하는 것이 아니라 새로운 버전의 배열을 만들고 싶다면 Spread Syntax를 활용합니다.

```jsx
//push
fruits = [...fruits, 'melon'];

//unshift
fruits = ['melon', ...fruits];
```

---

**참조**

[https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)

<드림코딩, [자바스크립트 프로처럼 쓰는 팁](https://www.youtube.com/watch?v=BUAhpB3FmS4)>
