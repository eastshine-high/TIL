# Optional chaining

아래의 객체 `bob` 과 `anna` 의 차이는 `job`의 유무입니다.

```jsx
const bob = {
    name: 'Julia',
    age: 20,
};

const anna = {
    name: 'Julia',
    age: 20,
    job: {
        title: 'Software Engineer',
    },
};
```

`job` 속성을 출력하는 함수를 구현한다고 했을 때, 기존에는 다음과 같이 `&&` 연산자를 연결하여 속성의 유무를 판단하여 출력을 실행하였습니다.

```jsx
function displayJobTitle(person){
    if(person.job && person.job.title){
        console.log(person.job.title);
    }
}
```

이를 **Optional chaining** 연산자인 `?.` 를 이용하여 간결하게 코드를 작성할 수 있습니다.

```jsx
function displayJobTitle(person){
    if(person.job?.title){ // 
        console.log(person.job.title);
    }
}
```

- `person.job?.title`
    - `job` 이 있다면 `job` 안에 있는 `title` 이 있는지 검사합니다.
    - `job` 이 비어있다면 `undefined` 가 되므로 if문이 실행되지 않습니다.

Optional chaining과 Nullish coalescing을 조합해서 사용할 수도 있다.

```jsx
function displayJobTitle(person){
    const title = person.job?.title ?? 'No Job Yet';
    console.log(title);
}
```

- `job` 또는 `job.title` 이 없다면 `‘No Job Yet'` 을 출력합니다.

---

**참조**

<드림코딩, [자바스크립트 프로처럼 쓰는 팁](https://www.youtube.com/watch?v=BUAhpB3FmS4)>
