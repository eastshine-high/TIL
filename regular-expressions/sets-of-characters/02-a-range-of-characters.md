# [ - ] 구문을 사용하여 문자 범위를 지정할 수 있다

[ - ] 구문을 사용하여 문자 범위를 지정할 수 있다. CASE 1과 CASE 2는 동일하다. 하나의 표현식에 여러 범위를 지정할 수 있다(Case 5).

A range of characters can be specified with [ - ] syntax. Case 1 and Case 2 are equivalent. Several ranges can be given in one expression (Case 5).

<br>

### Source

ABCDEFGHIJKLMNOPQRSTUVWXYZ abcdefghijklmnopqrstuvwxyz 0123456789

<br>

### Case 1

Regular expression : **[C-K]**

First match : AB`C`DEFGHIJKLMNOPQRSTUVWXYZ abcdefghijklmnopqrstuvwxyz 0123456789

All matches : AB`CDEFGHIJK`LMNOPQRSTUVWXYZ abcdefghijklmnopqrstuvwxyz 0123456789

<br>

### Case 2

Regular expression: **[CDEFGHIJK]**

First match : AB`C`DEFGHIJKLMNOPQRSTUVWXYZ abcdefghijklmnopqrstuvwxyz 0123456789

All matches : AB`CDEFGHIJK`LMNOPQRSTUVWXYZ abcdefghijklmnopqrstuvwxyz 0123456789

<br>

### Case 3

Regular expression: **[a-d]**

First match : ABCDEFGHIJKLMNOPQRSTUVWXYZ `a`bcdefghijklmnopqrstuvwxyz 0123456789

All matches : ABCDEFGHIJKLMNOPQRSTUVWXYZ `abcd`efghijklmnopqrstuvwxyz 0123456789

<br>

### Case 4

Regular expression: **[2-6]**

First match : ABCDEFGHIJKLMNOPQRSTUVWXYZ abcdefghijklmnopqrstuvwxyz 01`2`3456789

All matches : ABCDEFGHIJKLMNOPQRSTUVWXYZ abcdefghijklmnopqrstuvwxyz 01`23456`789

<br>

### Case 5

Regular expression: **[C-Ka-d2-6]**

First match : AB`C`DEFGHIJKLMNOPQRSTUVWXYZ abcdefghijklmnopqrstuvwxyz 0123456789

All matches : AB`CDEFGHIJK`LMNOPQRSTUVWXYZ `abcd`efghijklmnopqrstuvwxyz 01`23456`789

<br>

### Reference

[https://zvon.org/comp/r/tut-Regexp.html#Pages~Page_8](https://zvon.org/comp/r/tut-Regexp.html#Pages~Page_8)
