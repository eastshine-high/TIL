# \w는 모든 단어 문자(영숫자 + "_")와 일치합니다

`\w`는 모든 단어 문자(영숫자 + `_`)와 일치합니다. 일부 언어에서는 이러한 문자 약어가 인식되지 않습니다. 대신 문자 클래스(`[A-z0-9_]`)를 사용합니다(Case 5).

`\w` matches any word character ( alphanumeric plus `_` ). In some languages these letter abbreviations are not recognized. Use character classes (`[A-z0-9_]`) instead (Case 5).

<br>

### Source

A1 B2 c3 d_4 e:5 ffGG77--__—

<br>

### Case 1

Regular Expression : **\w**

First match : `A`1 B2 c3 d_4 e:5 ffGG77--__—

All matches : `A1` `B2` `c3` `d_4` `e`:`5` `ffGG77`--`__`—

<br>

### Case 2

Regular Expression : **\w***

First match : `A1` B2 c3 d_4 e:5 ffGG77--__—

All matches : `A1` `B2` `c3` `d_4` `e`:`5` `ffGG77`--`__`—

<br>

### Case 3

Regular Expression : **[a-z]\w***

First match : A1 B2 `c3` d_4 e:5 ffGG77--__—

All matches : A1 B2 `c3` `d_4` `e`:5 `ffGG77`--__—

<br>

### Case 4

Regular Expression : **\w{5}**

First match : A1 B2 c3 d_4 e:5 `ffGG7`7--__—

All matches : A1 B2 c3 d_4 e:5 `ffGG7`7--__—

<br>

### Case 5

Regular Expression : **[A-z0-9_]**

First match : `A`1 B2 c3 d_4 e:5 ffGG77--__—

All matches : `A1` `B2` `c3` `d_4` `e`:`5` `ffGG77`--`__`—

<br>

### Reference

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_18](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_18)
