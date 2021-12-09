# Selector(선택자)

### 선택자 우선순위

- 더 특정적인 선택자가 더 일반적인 선택자보다 우선합니다.

<br>

### 가장 마지막에 지정된 스타일 우선 적용

- 만약 충돌하는 두 스타일들이 같은 원천 소스를 가지거나 선택자 우선순위가 같다면 가장 마지막에 지정된 스타일이 우선 적용됩니다.

```html
<head>
<style type "text/css">
p.test {
    color: black;
}
</style>
</head>
 
<body>
    <p class="test" style="color: red;"> 경고문 </p>
</body>
```

이 경우 "경고문"이라는 텍스트는 가장 마지막에 지정된 스타일이 적용되어 빨간색으로 출력됩니다.

<br>

### 선택자(Selector)의 종류

- 전체 선택자(universal selector) : 전체 선택자는 "`*`"로 표시하며 모든 엘리먼트를 포함합니다.
- 타입 선택자(type selector) : 엘리먼트(`body`, `p`, `h1`, `ol`, `li` 등)를 선택자로 하는것을 말합니다.
- 하위 선택자(descendant selector) : 하위 선택자는 공백으로 분리된 두개 이상의 선택자들로 만들어집니다.(ex, depth에 상관없이 body안의 p태그는 모두 선택됩니다.)
- 자식 선택자(child selector) : 자식 선택자는 "`>`"로 분리된 두개 이상의 선택자들로 만들어집니다.(1depth아래의 자식들만 선택됩니다.)
- 인접 형제 선택자(adjacent sibling selector) : 인접 선택자는 "`+`"로 분리된 두개 이상의 선택자들로 만들어집니다.(div와 같은 depth이면서 div 바로 다음에 나오는 p가 선택됩니다.)

```html
<style type="text/css">
div + p {
    color: red;
}
</style>
 
<body>
    <p> text 1</p>
    <div>
        <p> text 2 </p>    
    </div>
    <p> text 3</p>     /* text 3이 빨간색으로 출력됩니다.  */ 
</body>
```

- 클래스 선택자(class selector) : 클래스 선택자`.`를 이용하면 특정 엘리먼트에만 스타일을 적용할 수 있습니다.

```html
p.summary {            /* 클래스가 summary인 p태그 선택 */
    color: gray;
}
.summary {              /* 클래스가 summary인 모든 태그 선택 */
    color: gray;
}
```

- 애트리뷰트 선택자(attribute selector) : 엘러먼트의 속성 값에 대한 표현과 일치하는 HTML 엘러먼트를 선택할 때 사용합니다.
    - a[target] : target 속성을 사용한 모든 a 엘러먼트를 선택
    - a[target =_blank] : target="_blank" 속성 표현을 가진 모든 a 엘러먼트를 선택
    - a[title~=food] : title 속성의 값에 단어 'food'를 포함하고 있는 모든 a 엘러먼트를 선택
    - a[title|=google] : title 속성의 값이 'google'이거나 'google-'로 시작하는 값을 가진 모든 a 엘러먼트를 선택
    - a[href^="https"] : href 속성의 값이 'https'로 시작하는 모든 a 엘러먼트를 선택
    - a[href$=".jpg"] : href 속성의 값이 '.jpg'로 끝나는 모든 a 엘러먼트를 선택
    - a[href*="co"] : href 속성의 값으로 'co'라는 부분문자열을 포함하고 있는 모든 a 엘러먼트를 선택
- ID 선택자(ID selector) : ID 선택자는 `#`로 표시하며, 문서안에서 유일한 엘러먼트를 선택할 때 사용합니다.
- 가상 클래스(pseudo-class) : 가상 클래스는 스타일이 적용되는 대상이 요소나 속성, 속성값에 따라 분류되는게 아니라 "상황"에 따라 분류됩니다.
    - 링크
        - a:link - 아직 방문하지 않은 모든 링크를 선택
        - a:visited - 방문한 모든 링크를 선택
    - 사용자 동작
        - a:hover - 마우스가 올라간 링크를 선택
        - a:active - 활성화되는(클릭되는) 링크를 선택
        - input:focus - 포커스를 가진 Input 엘러먼트를 선택
    - 사용자 인터페이스 상태
        - input:enabled - 활성화된 모든 Input 엘러먼트를 선택
        - input:disabled - 비활성화된 모든 Input 엘러먼트를 선택
        - input:checked - 체크된 모든 input 엘러먼트(라디오 버튼, 체크박스)를 선택
    - 부정
        - p:not(.sample) - class 속성이 sample이 아닌 모든 p 엘러먼트를 선택
- 가상 앨리먼트(pseudo-element) : 어떤 요소의 내용에 대해 XHTML에서는 최초의 줄이나 최초의 문자를 지정할 수 있는 방법이 없는데 가상 엘리먼트를 이용하여 그것에 스타일을 적용할 수 있습니다.
    - p:first-line : 모든 p 엘러먼트의 첫 번째 줄을 선택
    - p:first-letter : 모든 p 엘러먼트의 첫 번째 글자를 선택
    - p:before : p 엘러먼트의 내용 앞에 내용/스타일을 추가
    - p:after :  p 엘러먼트의 내용 뒤에 내용/스타일을 추가

<br>

### Reference

생활코딩 : [https://opentutorials.org/module/484/4150](https://opentutorials.org/module/484/4150)

[https://www.w3schools.com/cssref/css_selectors.asp](https://www.w3schools.com/cssref/css_selectors.asp)
