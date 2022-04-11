# 반복 찾기(Repeating matches)

### 중괄호를 사용하면 문자 반복을 정확하게 지정할 수 있습니다.

- {m}은 정확히 m번 일치합니다(Case 1).
- {m,n}은 최소 m번, 최대 n번 일치합니다(Case 2).
- {m,}는 최소 m번 일치를 지정합니다(Case 3).

Curly brackets enable precise specification of character repetitions. {m} matches precisely m times (Case 1), {m,n} matches minimaly m times and maximaly n times (Case 2) and {m,}matches minimaly m times(Case 3).

### Source

One ring to bring them all and in the darkness bind them

### Case 1

Regular Expression : **.{5}**

First match : `One r`ing to bring them all and in the darkness bind them

All matches : `One ring to bring them all and in the darkness bind the`m

### Case 2

Regular Expression : **[els]{1,3}**

First match : On`e` ring to bring them all and in the darkness bind them

All matches : On`e` ring to bring th`e`m a`ll` and in the darkn`ess` bind th`e`m

### Case 3

Regular Expression : **[a-z]{3,}**

First match : One `ring` to bring them all and in the darkness bind them

All matches : One `ring` to `bring` `them` `all` `and` in `the` `darkness` `bind` `them`

### 참조

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_15](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_15)
