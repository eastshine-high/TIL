# 수량자는 문자가 발생할 수 있는 횟수를 지정합니다.

수량자는 문자가 발생할 수 있는 횟수를 지정합니다. 별표 `*`(Case 1)는 0번 이상, 더하기 `+`(Case 2)는 한 번 이상 그리고 물음표 `?` (Case 3) 0 또는 1번.

Quantifiers specify how many times a character can occur. Star * (Case 1) matches zero or more times, plus + (Case 2) once or more times and question mark ? (Case 3) zero or once.

<br>

### Source

aabc abc bc

<br>

### Case 1

Regular Expression : **a*b**

First match : `aab`c abc bc

All matches : `aab`c `ab`c `b`c

<br>

### Case 2

Regular Expression : **a+b**

First match : `aab`c abc bc

All matches : `aab`c `ab`c bc

<br>

### Case 3

Regular Expression : **a?b**

First match : a`ab`c abc bc

All matches : a`ab`c `ab`c `b`c

<br>

### Reference

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_11](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_11)
