# 중괄호 표기법의 특수한 경우

수량자 `*`, `+` , `?`은 중괄호 표기법의 특수한 경우입니다.

- `*`는 `{0,}` 와 같습니다(Case 1, Case 2).
- `+`는 `{1,}`와 같습니다(Case 3, Case 4).
- `?`는 `{0,1}`와 같습니다(Case 5, Case 6).

Quantifiers `*`*, `+`, and `?` are special cases of the bracket notation.*

- *`*`* is equivalent to {0,} (Case 1, Case 2)
- `+` to {1,} (Case 3, Case 4)
- `?` to {0,1} (Case 5, Case 6).

### Source

AA ABA ABBA ABBBA

### Case 1

Regular Expression : **AB*A**

First match : `AA` ABA ABBA ABBBA

All matches : `AA` `ABA` `ABBA` `ABBBA`

### Case 2

Regular Expression : **AB{0,}A**

First match : `AA` ABA ABBA ABBBA

All matches : `AA` `ABA` `ABBA` `ABBBA`

### Case 3

Regular Expression : **AB+A**

First match : AA `ABA` ABBA ABBBA

All matches : AA `ABA` `ABBA` `ABBBA`

### Case 4

Regular Expression : **AB{1,}A**

First match : AA `ABA` ABBA ABBBA

All matches : AA `ABA` `ABBA` `ABBBA`

### Case 5

Regular Expression : **AB?A**

First match : `AA` ABA ABBA ABBBA

All matches : `AA` `ABA` ABBA ABBBA

### Case 6

Regular Expression : **AB{0,1}A**

First match : `AA` ABA ABBA ABBBA

All matches : `AA` `ABA` ABBA ABBBA

### 참조

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_16](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_16)
