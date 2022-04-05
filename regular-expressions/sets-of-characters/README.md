# 문자 집합으로 찾기(Matching Sets of Characters)

- 대괄호 `[]` 안에 문자 목록을 제공할 수 있다.
- `[ - ]` 구문을 사용하여 문자 범위를 지정할 수 있다.
- 문자 클래스가 `^`로 시작하면 지정된 문자가 선택되지 않는다.

## 대괄호 `[]` 안에 문자 목록을 제공할 수 있다.

대괄호 `[]` 안에 문자 목록을 제공할 수 있다. 이 문자 중 하나라도 발견되면 표현식이 일치한다. **문자 순서는 중요하지 않다.**(Case 3)

Inside square brackets "[]" a list of characters can be provided. The expression matches if any of these characters is found. The order of characters is insignificant.(*Case 3*)

### Source

How do you do?

### Case 1

Regular expression: **[oyu]**

First match : H`o`w do you do?

All matches : H`o`w d`o` `you` d`o`?

### Case 2

Regular expression: **[dH].**

First match : `Ho`w do you do?

All matches : `Ho`w `do` you `do`?

### Case 3

Regular expression: **[owy][yow]**

First match : H`ow` do you do?

All matches : H`ow` do `yo`u do?

## `[ - ]` 구문을 사용하여 문자 범위를 지정할 수 있다.

[ - ] 구문을 사용하여 문자 범위를 지정할 수 있다. CASE 1과 CASE 2는 동일하다. 하나의 표현식에 여러 범위를 지정할 수 있다(Case 5).

### Source

ABCDEFGHIJKLMNOPQRSTUVWXYZ abcdefghijklmnopqrstuvwxyz 0123456789

### Case 1

Regular expression: **[C-K]**

First match : AB`C`DEFGHIJKLMNOPQRSTUVWXYZ abcdefghijklmnopqrstuvwxyz 0123456789

All matches : AB`CDEFGHIJK`LMNOPQRSTUVWXYZ abcdefghijklmnopqrstuvwxyz 0123456789

### Case 2

Regular expression: **[CDEFGHIJK]**

First match : AB`C`DEFGHIJKLMNOPQRSTUVWXYZ abcdefghijklmnopqrstuvwxyz 0123456789

All matches : AB`CDEFGHIJK`LMNOPQRSTUVWXYZ abcdefghijklmnopqrstuvwxyz 0123456789

### Case 3

Regular expression: **[a-d]**

First match : ABCDEFGHIJKLMNOPQRSTUVWXYZ `a`bcdefghijklmnopqrstuvwxyz 0123456789

All matches : ABCDEFGHIJKLMNOPQRSTUVWXYZ `abcd`efghijklmnopqrstuvwxyz 0123456789

### Case 4

Regular expression: **[2-6]**

First match : ABCDEFGHIJKLMNOPQRSTUVWXYZ abcdefghijklmnopqrstuvwxyz 01`2`3456789

All matches : ABCDEFGHIJKLMNOPQRSTUVWXYZ abcdefghijklmnopqrstuvwxyz 01`23456`789

### Case 5

Regular expression: **[C-Ka-d2-6]**

First match : AB`C`DEFGHIJKLMNOPQRSTUVWXYZ abcdefghijklmnopqrstuvwxyz 0123456789

All matches : AB`CDEFGHIJK`LMNOPQRSTUVWXYZ `abcd`efghijklmnopqrstuvwxyz 01`23456`789

## 문자 클래스가 `^`로 시작하면 지정된 문자가 선택되지 않는다.

문자 클래스가 `^`로 시작하면 지정된 문자가 선택되지 않는다.

If a character class starts with `^`, then specified characters will not be selected

### Source

ABCDEFGHIJKLMNOPQRSTUVWXYZ abcdefghijklmnopqrstuvwxyz 0123456789

### Case 1

Regular expression: **[^CDghi45]**

First match : `A`BCDEFGHIJKLMNOPQRSTUVWXYZ abcdefghijklmnopqrstuvwxyz 0123456789

All matches : `AB`CD`EFGHIJKLMNOPQRSTUVWXYZ abcdef`ghi`jklmnopqrstuvwxyz 0123`45`6789`

### Case 2

Regular expression: **[^W-Z]**

First match : `A`BCDEFGHIJKLMNOPQRSTUVWXYZ abcdefghijklmnopqrstuvwxyz 0123456789

All matches : `ABCDEFGHIJKLMNOPQRSTUV`WXYZ `abcdefghijklmnopqrstuvwxyz 0123456789`

## 참조

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_7](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_7)

[https://zvon.org/comp/r/tut-Regexp.html#Pages~Page_8](https://zvon.org/comp/r/tut-Regexp.html#Pages~Page_8)

[https://zvon.org/comp/r/tut-Regexp.html#Pages~Page_9](https://zvon.org/comp/r/tut-Regexp.html#Pages~Page_9)
