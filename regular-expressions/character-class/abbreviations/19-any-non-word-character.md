# \W는 단어가 아닌 모든 문자와 일치합니다

`\W`는 단어가 아닌 모든 문자와 일치합니다(영숫자와 `_`를 제외한 모든 문자). Case 1과 Case 2를 비교하십시오. `[^A-z0-9_]`와 동일합니다.

`\W` matches any non-word character (everything but alphanumeric plus `_` ). Compare Case 1 and Case 2. It is equivalent to `[^A-z0-9_]`.

<br>

### Source

AS _34:AS11.23 @#$ %12^*

<br>

### Case 1

Regular Expression : **\W**

First match : AS ``_34:AS11.23 @#$ %12^*

All matches : AS ``_34`:`AS11`.`23 `@#$ %`12`^*`

<br>

### Case 2

Regular Expression : **\w**

First match : `A`S _34:AS11.23 @#$ %12^*

All matches : `AS` `_34`:`AS11`.`23` @#$ %`12`^*

<br>

### Case 3

Regular Expression : **[^A-z0-9_]**

First match : AS ``_34:AS11.23 @#$ %12^*

All matches : AS ``_34`:`AS11`.`23 `@#$ %`12^`*`

<br>

### Reference

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_19](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_19)
