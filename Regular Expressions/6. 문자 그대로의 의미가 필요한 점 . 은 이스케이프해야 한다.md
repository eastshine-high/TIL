# 문자 그대로의 의미가 필요한 점 . 은 이스케이프해야 한다

문자 그대로의 의미가 필요한 점(.)은 이스케이프해야 한다

The point must be escaped if literal meaning is required.

### Source

O.K.

<br>

### Case 1

Regular Expression : **.**

First match : `O`.K.

All matches : `O.K.`

<br>

### Case 2

Regular Expression : \.

First match : O`.`K.

All matches : O`.`K`.`

<br>

### Case 3

Regular Expression : \..\.

First match : O`.K.`

All matches : O`.K.`

<br>

### Referecne

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_6](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_6)