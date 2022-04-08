# 위치 찾기(Position matching)

위치 찾기는 텍스트 문자열 안에서 반드시 일치해야 하는 위치를 지정할 때 사용한다. 위치 찾기가 왜 필요한지 이해하기 위해 다음 예제를 살펴보자.

### Source

The cat scattered his food all over the room.

### Case

Regular Excpression : cat

All matches : The `cat` s`cat`tered his food all over the room.

`cat` 패턴은 cat이 있는 부분과 모두 일치한다. 심지어 scattered라는 단어 사이에 있는 cat과도 일치한다. 이런 결과를 바랐을 수도 있지만 그렇지 않을 가능성이 더 크다. 만약 cat을 모두 dog로 치환하려고 검색했다면 말도 안 되는 결과를 얻게 된다.

## 단어 경계 지정하기

`\b`는 단어 경계와 일치한다. 단어 경계(`\b`)는 **한 쪽에 `\w`가 있고 다른 쪽에 (어느 순서로든) `\W`가 있는 두 문자 사이의 지점으로 정의된**다.

`\b` matches a word boundary. A word boundary (`\b`) is defined as a spot between two characters that has a `\w` on one side of it and a `\W` on the other side of it (in either order).

### Source

Ere iron was found or tree was hewn,    When young was mountain under moon.

### Case 1

Regular Excpression : \b.

All matches : `E`re `i`ron `w`as `f`ound `o`r `t`ree `w`as `h`ewn`,`    `W`hen `y`oung `w`as `m`ountain `u`nder `m`oon`.`

- 다음 부분을 확인하자.
- `h`ewn`,`
- `m`oon`.`

### Case 2

Regular Excpression : .\b

All matches : Er`e` iro`n` wa`s` foun`d` o`r` tre`e` wa`s` hew`n`,    Whe`n` youn`g` wa`s` mountai`n` unde`r` moo`n`.

- 다음 부분을 확인하자.
- hew`n`,
- moo`n`.

`**\B`는 비단어 경계와 일치한다.**

`\B` matches a non word boundary.

### Case 3

Regular Excpression : \B.

All matches : E`re` i`ron` w`as` f`ound` o`r` t`ree` w`as` h`ewn`,    W`hen` y`oung` w`as` m`ountain` u`nder` m`oon`.

### Case 4

Regular Excpression : .\B

All matches : `Er`e `iro`n `wa`s `foun`d `o`r `tre`e `wa`s `hew`n`,`    `Whe`n `youn`g `wa`s `mountai`n `unde`r `moo`n`.`

### 주의

> 정규식에서의 '단어(`\w`)'는 한글과 같은 2바이트 문자를 포함하지 않기 때문에, 한글의 경계는 \b로 처리할 수 없다는 것이다. 한글의 경계를 판단하려면, 전후방탐색으로 한글이 아닌 문자와의 경계를 판단하는 것이 좋다. `/(?<=[^가-힣])대상문자(?=[^가-힣])/` (자음과 모음까지 문자로 인식하려고 한다면, `[^ㄱ-ㅎㅏ-ㅣ가-힣]` 를 사용하면 된다) 자바스크립트와 같이 후방 탐색을 제공하지 않는 정규식 엔진이라면, 정규식으로만 걸러내는 것보다는, 해당 케이스에 맞는 별도의 로직을 작성하는 것이 좋다.
> 

## 문자열 경계 지정하기

일부 문자(`^`, `$`)에는 특별한 의미가 있다. 문자 `^`는 줄의 시작과 일치하고(Case 1) 달러 기호 `$`는 줄의 끝(Case 2)과 일치한다.

Some characters have special meanings. Character `^` matches the beginning of the line (*Case 1*) while dollar sign `$` the end of the line (*Case 2)*

### Source

who is who

### Case 1

Regular Expession : **^who**

First match : `who` is who

All matches : `who` is who

### Case 2

Regular Expession : **who$**

First match : who is `who`

All matches : who is `who`

### 다중행 모드(multiline mode)

대개 캐럿(`^`)은 문자열의 시작과 일치하고, 달러 기호(`$`)는 문자열의 마지막과 일치한다. 다중행 모드로 변경하면 정규 표현식 엔진이 줄바꿈 문자를 문자열 구분자로 강제로 인식한다. 캐럿(`^`)은 문자열의 시작이나 줄바꿈 다음(새로운 행)에 나오는 문자열의 시작과 일치하고, 달러 기호(`$`)는 문자열의 마지막이나 줄바꿈 다음에 나오는 문자열의 마지막과 일치한다.

예를 들어, 자바스크립트에서는 `m`플래그로 다중행 모드를 표현한다.

```jsx
let str = `1st place: Winnie
2nd place: Piglet
3rd place: Eeyore`;

alert( str.match(/^\d/gm) ); // 1, 2, 3
```

- `\d/gm` 패턴으로 각 행이 시작하는 위치에 있는 숫자를 찾을 수 있다.

다음은 `m` 플래그 없이 검색했을 때의 예이다.

```jsx
let str = `1st place: Winnie
2nd place: Piglet
3rd place: Eeyore`;

alert( str.match(/^\d/g) ); // 1
```

## 참조

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_3](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_3)

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_22](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_22)

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_23](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_23)

[https://ohgyun.com/392](https://ohgyun.com/392)

[https://ko.javascript.info/regexp-multiline-mode](https://ko.javascript.info/regexp-multiline-mode)
