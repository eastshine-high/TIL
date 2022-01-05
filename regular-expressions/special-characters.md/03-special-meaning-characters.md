# 일부 문자(^ , $)에는 특별한 의미가 있다

일부 문자에는 특별한 의미가 있다. 문자 `^`는 줄의 시작과 일치하고(Case 1) 달러 기호 `$`는 줄의 끝(Case 2)과 일치한다.

Some characters have special meanings. Character ^ matches the beginning of the line (*Case 1*) while dollar sign $ the end of the line (*Case 2)*

<br>

### Source

who is who

<br>

### Case 1
Regular Expession : **^who**

First match : `who` is who

All matches : `who` is who

<br>

### Case 2
Regular Expession : **who$**

First match : who is `who`

All matches : who is `who`

<br>

### Reference
[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_3](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_3)
