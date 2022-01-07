# \s는 공백 문자(공백, 개행 및 탭)와 일치합니다

`\s`는 공백 문자(공백, 개행 및 탭)와 일치합니다. `\S`는 공백이 아닌 모든 문자와 일치합니다.

`\s` matches white space characters: space, new line and tab. `\S` matches any non-whitespace character.

<br>

### Source

Ere iron was found or tree was hewn, When young was mountain under moon; Ere ring was made, or wrought was woe, It walked the forests long ago.

<br>

### Case 1

Regular Expression : **\s**

**First match**

Ere ``iron was found or tree was hewn, When young was mountain under moon; Ere ring was made, or wrought was woe, It walked the forests long ago.

**All matches**

Ere **``**iron **``**was **``**found **``**or **``**tree **``**was **``**hewn, **``**When **``**young **``**was **``**mountain **``**under **``**moon; **``**Ere **``**ring **``**was **``**made, **``**or **``**wrought **``**was **``**woe, **``**It **``**walked **``**the **``**forests **``**long **``**ago.

<br>

### Case 2

Regular Expression : **\S**

**First match**

`E`re iron was found or tree was hewn, When young was mountain under moon; Ere ring was made, or wrought was woe, It walked the forests long ago.

**All matches**

`Ere` `iron` `was` `found` `or` `tree` `was` `hewn,` `When` `young` `was` `mountain` `under` `moon;` `Ere` `ring` `was` `made,` `or` `wrought` `was` `woe,` `It` `walked` `the` `forests` `long` `ago.`

<br>

### Reference

[http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_20](http://zvon.org/comp/r/tut-Regexp.html#Pages~Page_20)
