# `?` 수량자의 여러 예

Several examples of `?` quantifier

<br>

### Source

--XX-@-XX-@@-XX-@@@-XX-@@@@-XX-@@-@@-

<br>

### Case 1

Regular Expression : X?XX?X

First match : --`XX`-@-XX-@@-XX-@@@-XX-@@@@-XX-@@-@@-

All matches : --`XX`-@-`XX`-@@-`XX`-@@@-`XX`-@@@@-`XX`-@@-@@-

<br>

### Case 2

Regular Expression : -**@?@?@?-**

First match : `--`XX-@-XX-@@-XX-@@@-XX-@@@@-XX-@@-@@-

All matches : `--`XX`-@-`XX`-@@-`XX`-@@@-`XX-@@@@-XX`-@@-`@@-

<br>

### Case 3

Regular Expression : [^@]@?@

First match : --XX`-@`-XX-@@-XX-@@@-XX-@@@@-XX-@@-@@-

All matches : --XX`-@`-XX`-@@`-XX`-@@`@-XX`-@@`@@-XX`-@@-@@`-

<br>

### Reference

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_14](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_14)
