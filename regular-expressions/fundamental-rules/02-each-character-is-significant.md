# 검색 패턴 내의 각 문자는 공백 문자까지도 중요하다

검색 패턴 내의 각 문자는 공백 문자(공백, 탭, 새 줄)를 포함하여 중요하다.

Each character inside the search pattern is significant including whitespace characters (space, tab, new line)

<br>

### Source

Hello, world!

<br>

### Case 1

Regular Expession : **Hello, world**

First match : `Hello, world`

All matches : `Hello, world`

<br>

### Case 2

Regular Expession : **Hello,&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;world**

First match : Hello, world!

All matches : Hello, world!

<br>

### Reference

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_2](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_2)
