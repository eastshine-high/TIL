# 특수한 문자 집합(Special Sets of Characters)

- `\w`는 모든 단어 문자(영숫자 + `_`)와 일치한다.
- `\W`는 단어가 아닌 모든 문자와 일치한다.
- `\s`는 공백 문자(`[ \f\n\r\t\v]`)와 일치한다.
- `\d`는 모든 숫자와 일치하고 `\D`는 다른 모든 것과 일치한다.

## `\w`는 모든 단어 문자(영숫자 + `_`)와 일치한다.

`\w`는 모든 단어 문자(영숫자 + `_`)와 일치합니다. 일부 언어에서는 이러한 문자 약어가 인식되지 않습니다. 대신 문자 클래스(`[A-z0-9_]`)를 사용합니다(Case 5).

`\w` matches any word character ( alphanumeric plus `_` *). In some languages these letter abbreviations are not recognized. Use character classes (*`[A-z0-9_]`) instead (Case 5).

### Source

A1 B2 c3 d_4 e:5 ffGG77--__—

### Case 1

Regular Expression : **\w**

First match : `A`1 B2 c3 d_4 e:5 ffGG77--__—

All matches : `A1` `B2` `c3` `d_4` `e`:`5` `ffGG77`--`__`—

### Case 2

Regular Expression : **\w***

First match : `A1` B2 c3 d_4 e:5 ffGG77--__—

All matches : `A1` `B2` `c3` `d_4` `e`:`5` `ffGG77`--`__`—

### Case 3

Regular Expression : **[a-z]\w***

First match : A1 B2 `c3` d_4 e:5 ffGG77--__—

All matches : A1 B2 `c3` `d_4` `e`:5 `ffGG77`--__—

### Case 4

Regular Expression : **\w{5}**

First match : A1 B2 c3 d_4 e:5 `ffGG7`7--__—

All matches : A1 B2 c3 d_4 e:5 `ffGG7`7--__—

### Case 5

Regular Expression : **[A-z0-9_]**

First match : `A`1 B2 c3 d_4 e:5 ffGG77--__—

All matches : `A1` `B2` `c3` `d_4` `e`:`5` `ffGG77`--`__`—

## `\W`는 단어가 아닌 모든 문자와 일치한다.

`\W`는 단어가 아닌 모든 문자와 일치합니다(영숫자와 _를 제외한 모든 문자). Case 1과 Case 2를 비교하십시오. `[^A-z0-9_]`와 동일합니다.

`\W` matches any non-word character (everything but alphanumeric plus "_*" ). Compare Case 1 and Case 2. It is equivalent to `[^A-z0-9*]`.

### Source

AS _34:AS11.23 @#$ %12^*

### Case 1

Regular Expression : **\W**

First match : AS ``_34:AS11.23 @#$ %12^*

All matches : AS ``_34`:`AS11`.`23 `@#$ %`12`^*`

### Case 2

Regular Expression : **\w**

First match : `A`S _34:AS11.23 @#$ %12^*

All matches : `AS` `_34`:`AS11`.`23` @#$ %`12`^*

### Case 3

Regular Expression : **[^A-z0-9_]**

First match : AS ``_34:AS11.23 @#$ %12^*

All matches : AS ``_34`:`AS11`.`23 `@#$ %`12^`*`

## `\s`는 공백 문자(`[ \f\n\r\t\v]`)와 일치한다.

`\s`는 공백 문자(`[ \f\n\r\t\v]`)와 일치합니다. `\S`는 공백이 아닌 모든 문자(`[^ \f\n\r\t\v]`)와 일치합니다.

`\s` matches white space characters: space, new line and tab. `\S` matches any non-whitespace character.

### Source

Ere iron was found or tree was hewn, When young was mountain under moon; Ere ring was made, or wrought was woe, It walked the forests long ago.

### Case 1

Regular Expression : **\s**

First match

Ere ``iron was found or tree was hewn, When young was mountain under moon; Ere ring was made, or wrought was woe, It walked the forests long ago.

All matches

Ere **``**iron **``**was **``**found **``**or **``**tree **``**was **``**hewn, **``**When **``**young **``**was **``**mountain **``**under **``**moon; **``**Ere **``**ring **``**was **``**made, **``**or **``**wrought **``**was **``**woe, **``**It **``**walked **``**the **``**forests **``**long **``**ago.

### Case 2

Regular Expression : **\S**

First match

`E`re iron was found or tree was hewn, When young was mountain under moon; Ere ring was made, or wrought was woe, It walked the forests long ago.

All matches

`Ere` `iron` `was` `found` `or` `tree` `was` `hewn,` `When` `young` `was` `mountain` `under` `moon;` `Ere` `ring` `was` `made,` `or` `wrought` `was` `woe,` `It` `walked` `the` `forests` `long` `ago.`

## `\d`는 모든 숫자와 일치하고 `\D`는 다른 모든 것과 일치한다.

`\d`는 모든 숫자와 일치하고 `\D`는 다른 모든 것과 일치합니다. Case 1과 Case 2를 비교하십시오. 프로그래밍 언어가 이 약어를 지원하지 않는 경우 "`[0-9]`"를 사용하십시오(Case 3).

`\d` matches any digit and `\D` anything else. Compare *Case 1* and *Case 2*. Use "`[0-9]`" if your programming language does not support this abbreviation (*Case 3*).

### Source

Page 123; published: 1234 id=12#24@112

### Case 1

Regular Expression : **\d**

First match : Page `1`23; published: 1234 id=12#24@112

All matches : Page `123`; published: `1234` id=`12`#`24`@`112`

### Case 2

Regular Expression : **\D**

First match : `P`age 123; published: 1234 id=12#24@112 

All matches : `Page` 123`; published:` 1234 `id=`12`#`24`@`112

### Case 3

Regular Expression : **[0-9]**

First match : Page `1`23; published: 1234 id=12#24@112

All matches : Page `123`; published: `1234` id=`12`#`24`@`112`

## 참조

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_18](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_18)

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_19](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_19)

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_20](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_20)

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_21](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_21)
