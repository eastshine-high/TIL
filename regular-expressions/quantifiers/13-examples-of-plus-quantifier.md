# `+` 수량자의 여러 예

Several examples of `+` quantifier

<br>

### Source

-@@@- * ** - - "*" -- * ** -@@@-

<br>

### Case 1

Regular Expression : **\*+**

First match : -@@@- `*` ** - - "*" -- * ** -@@@-

All matches : -@@@- `*` `**` - - "`*`" -- `*` `**` -@@@-

<br>

### Case 2

Regular Expression : -@+-

First match : `-@@@-` * ** - - "*" -- * ** -@@@-

All matches : `-@@@-` * ** - - "*" -- * ** `-@@@-`

<br>

### Case 3

Regular Expression : [^ ]*

First match : `-@@@-` * ** - - "*" -- * ** -@@@-

All matches : `-@@@-` `*` `**` `-` `-` `"*"` `--` `*` `**` `-@@@-`

<br>

### Reference

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_13](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_13)
