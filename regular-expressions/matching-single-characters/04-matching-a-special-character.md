# 특수 문자의 리터럴 값이 필요한 경우 백슬래시 \로 이스케이프해야 한다

특수 문자의 리터럴 값이 필요한 경우 백슬래시 \로 이스케이프해야 한다. Case 1은 두 문자 모두 특수 문자이므로 아무 것도 일치하지 않으며 Case 2는 모든 $와 일치하며 Case 3은 첫 번째 문자인 경우에만 $와 일치하고 Case 4는 마지막 문자인 경우에만 일치한다. 백슬래시는 특별한 의미를 가지며 문자 그대로 사용하려면 이스케이프 처리해야 한다(Case 5).

If literal value of a special character is required, it must be escaped with a backslash \. Case 1 does not match anything as both characters a special, Case 2 matches all $, Case 3 matches $ only if it is the first and Case 4 the last character. Backslash has special meaning and must be also escaped for literal use (Case 5).

<br>

### Source

$12$ \-\ $25$

<br>

### Case 1

Regular Expession : **^$**

First match : $12$ \-\ $25$

All matches : $12$ \-\ $25$

<br>

### Case 2

Regular Expession : **\$**

First match : `$`12$ \-\ $25$

All matches : `$`12`$` \-\ `$`25`$`

<br>

### Case 3

Regular Expession : **^\$**

First match : `$`12$ \-\ $25$

All matches : `$`12$ \-\ $25$

<br>

### Case 4

Regular Expession : \$**$**

First match : $12$ \-\ $25`$`

All matches : $12$ \-\ $25`$`

<br>

### Case 5

Regular Expession : **\\**

First match : $12$ `\`-\ $25$

All matches : $12$ `\`-`\` $25$

<br>

### Reference
[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_4](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_4)