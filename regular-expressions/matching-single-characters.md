# 문자 하나 찾기(Matching single characters)

- 정규식은 대소문자를 구분한다.
- 검색 패턴 내의 각 문자는 공백 문자(공백, 탭, 새 줄)를 포함하여 중요하다.
- 점 `.` 은 모든 문자와 일치한다.
- 특수 문자의 리터럴 값이 필요한 경우 백슬래시 \로 이스케이프해야 한다.

## 정규식은 대소문자를 구분한다.

정규식은 대소문자를 구분한다. 따라서 Case 1은 지정된 텍스트를 찾지만 Case 2는 찾지 않는다.

Regular expressions are case sensitive. Therefore *Case 1* will find the specified text, but *Case 2* will not.

### Source

Hello, world!

### Case 1

Regular Expession : **Hello**

First match : `Hello`, world

All matches : `Hello`, world

### Case 2

Regular Expession : **Hello**

First match : hello, world

All matches : hello, world

## 검색 패턴 내의 각 문자는 공백 문자(공백, 탭, 새 줄)를 포함하여 중요하다.

검색 패턴 내의 각 문자는 공백 문자(공백, 탭, 새 줄)를 포함하여 중요하다.

Each character inside the search pattern is significant including whitespace characters (space, tab, new line)

### Source

Hello, world!

### Case 1

Regular Expession : **Hello, world**

First match : `Hello, world`

All matches : `Hello, world`

### Case 2

Regular Expession : **Hello,    world!**

First match : Hello, world!

All matches : Hello, world!

## 점 `.` 은 모든 문자와 일치한다

점 . 은 모든 문자와 일치한다.

Point . matches any character.

### Source

Regular expressions are powerful!!!

### Case 1

Regular Excpression :     **.**

First match : `R`egular expressions are powerful!!!

All matches : `Regular expressions are powerful!!!`

### Case 2

Regular Excpression :     **......**

First match : `Regula`r expressions are powerful!!!

All matches : `Regular expressions are powerful!!!`

## 특수 문자의 리터럴 값이 필요한 경우 백슬래시 \로 이스케이프해야 한다.

특수 문자의 리터럴 값이 필요한 경우 백슬래시 \로 이스케이프해야 한다. Case 1은 두 문자 모두 특수 문자이므로 아무 것도 일치하지 않으며 Case 2는 모든 $와 일치하며 Case 3은 첫 번째 문자인 경우에만 $와 일치하고 Case 4는 마지막 문자인 경우에만 일치한다. 백슬래시는 특별한 의미를 가지며 문자 그대로 사용하려면 이스케이프 처리해야 한다(Case 5).

If literal value of a special character is required, it must be escaped with a backslash \. Case 1 does not match anything as both characters a special, Case 2 matches all $, Case 3 matches $ only if it is the first and Case 4 the last character. Backslash has special meaning and must be also escaped for literal use (Case 5).

### Source

$12$ \-\ $25$

### Case 1

Regular Expession : **^$**

First match : $12$ \-\ $25$

All matches : $12$ \-\ $25$

### Case 2

Regular Expession : **\$**

First match : `$`12$ \-\ $25$

All matches : `$`12`$` \-\ `$`25`$`

### Case 3

Regular Expession : **^\$**

First match : `$`12$ \-\ $25$

All matches : `$`12$ \-\ $25$

### Case 4

Regular Expession : \$**$**

First match : $12$ \-\ $25`$`

All matches : $12$ \-\ $25`$`

### Case 5

Regular Expession : **\\**

First match : $12$ `\`-\ $25$

All matches : $12$ `\`-`\` $25$

## 참조

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_1](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_1)

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_2](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_2)

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_4](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_4)

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_6)5
