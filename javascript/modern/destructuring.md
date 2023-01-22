# Destructuring

Destructuring 할당 문법은 배열의 값 또는 객체의 속성을 풀어서 별개의 변수로 쓸 수 있게 해주는 자바스크립트 표현식입니다.

### 객체 디스트럭처링

먼저 객체 디스트럭처링을 사용하기 전의 코드입니다.

```jsx
const person = {
		name: 'Julia',
		age: 20,
		phone: '01077777777'
}

function displayPerson(person) {
		displayName(person.name);
		displayPhone(person.phone);
		displayProfile(person.name, person.age);
}
```

- 위의 함수를 보면 `person.` 가 계속해서 중복되고 있습니다.

중복되는 `person.` 을 제거하기 위해 객체 Destructuring을 사용합니다.

```jsx
const { name, phone, age } = person;
displayName(person.name);
displayPhone(person.phone);
displayProfile(person.name, person.age);
```

- `const { name, phone, age } = person` : `person` 객체의 키 값을 변수에 할당합니다.

### 배열 디스트럭처링

배열을 디스트럭처링할 때는 객체의 디스트럭처링과는 달리 `{}` 가 아닌 `[]` 를 사용합니다.

```jsx
const person = ["Alberto", "Montalesi", 25];
const [name, surname, age] = person;
```

나머지 모든 값을 얻고 싶다면 레스트 연산자(rest operator)를 사용하면 된다.

```jsx
const person = ["Alberto", "Montalesi", 25, "Italy", "Venice"];
const [name, surname, ...native] = person;
console.log(native);
// ["Italy", "Venice"]
```

여기서 `…` 가 레스트 연산자를 의미합니다.

---

**참조**

<알베르토 몬탈레시, 모던 자바스크립트 핵심 가이드, 한빛미디어>

<드림코딩, [자바스크립트 프로처럼 쓰는 팁](https://www.youtube.com/watch?v=BUAhpB3FmS4)>
