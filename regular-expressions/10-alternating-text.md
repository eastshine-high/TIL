# 대체 텍스트는 괄호()로 묶고 대체 텍스트는 |로 구분할 수 있다

대체 텍스트는 괄호로 묶고 대체 텍스트는 |로 구분할 수 있다.

Alternating text can be enclosed in parentheses and alternatives separated with |.

<br>

### Source

Monday Tuesday Friday

<br>

### Case 1

Regular Expression : **(on|ues|rida)**

First match : M**`on`**day Tuesday Friday

All matches : M**`on`**day T**`ues`**day F**`rida`**y

<br>

### Case 2

Regular Expression : **(Mon|Tues|Fri)day**

First match : `Monday` Tuesday Friday

All matches : `Monday` `Tuesday` `Friday`

<br>

### Case 3

Regular Expression : **..(id|esd|nd)ay**

First match : `Monday` Tuesday Friday

All matches : `Monday` `Tuesday` `Friday`

<br>

### Reference

[https://zvon.org/comp/r/tut-Regexp.html#Pages~Page_10](https://zvon.org/comp/r/tut-Regexp.html#Pages~Page_10)
