# 대괄호 [ ] 안에 문자 목록을 제공할 수 있다

대괄호 "[]" 안에 문자 목록을 제공할 수 있다. **이 문자 중 하나라도 발견되면 표현식이 일치한다. 문자 순서는 중요하지 않다.**(Case 3)

Inside square brackets "[]" a list of characters can be provided. The expression matches if any of these characters is found. The order of characters is insignificant.(*Case 3*)

<br>

### Source

How do you do?

<br>

### Case 1

Regular expression: **[oyu]**

First match : H`o`w do you do?

All matches : H`o`w d`o` `you` d`o`?

<br>

### Case 2

Regular expression: **[dH].**

First match : `Ho`w do you do?

All matches : `Ho`w `do` you `do`?

<br>

### Case 3

Regular expression: **[owy][yow]**

First match : H`ow` do you do?

All matches : H`ow` do `yo`u do?

<br>

### Reference

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_7](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_7)
