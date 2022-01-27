# \d는 모든 숫자와 일치합니다

`\d`는 모든 숫자와 일치하고 `\D`는 다른 모든 것과 일치합니다. Case 1과 Case 2를 비교하십시오. 프로그래밍 언어가 이 약어를 지원하지 않는 경우 `[0-9]`를 사용하십시오(Case 3).

`\d` matches any digit and `\D` anything else. Compare *Case 1* and *Case 2*. Use `[0-9]` if your programming language does not support this abbreviation (*Case 3*).

<br>

### Source

Page 123; published: 1234 id=12#24@112

<br>

### Case 1

Regular Expression : **\d**

First match : Page `1`23; published: 1234 id=12#24@112

All matches : Page `123`; published: `1234` id=`12`#`24`@`112`

<br>

### Case 2

Regular Expression : **\D**

First match : `P`age 123; published: 1234 id=12#24@112 

All matches : `Page` 123`; published:` 1234 `id=`12`#`24`@`112

<br>

### Case 3

Regular Expression : **[0-9]**

First match : Page `1`23; published: 1234 id=12#24@112

All matches : Page `123`; published: `1234` id=`12`#`24`@`112`

<br>

### Reference

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_21](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_21)
