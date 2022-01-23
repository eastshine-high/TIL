# HTML

Wikipedia에서는 다음과 같이 HTML을 정의한다.

> The HyperText Markup Language, or HTML is the standard markup language for documents designed to be displayed in a web browser.
> 

> **HyperText Markup Language은 웹 브라우저에 보여지도록 설계된 문서의 표준화된 Markup 언어이다.** 
> 

- 웹 브라우저는 웹 서버 또는 로컬 저장소에서 HTML 문서들을 수신하고 이 문서들을 멀티미디어 웹 페이지로 렌더링한다. HTML은 웹 페이지의 구조를 의미론적으로 설명하고 문서의 모양에 대한 단서를 포함한다.
- **Hypertext란 다른 텍스트로 참조(링크)되어있는 텍스트를 말한다.** 사용자는 이 참조(링크)를 통해 해당 텍스트(링크)로 즉시 접근할 수 있다. 즉 웹 사이트에서 링크를 클릭해 다른 문서나 사이트로 즉시 이동할 수 있는 기능을 말한다.
- **Markup Language란 일반적인 텍스트와 문법적으로 구분하기 위해서 문서에 어노테이팅 된 것을 말한다.** 즉 태그(tag)를 사용해 문서에서 어느 부분이 제목이고 본문인지, 어느 부분이 사진이고 링크인지 표시하여 문서를 구조화하는 것을 말한다.
- 이 언어는 CSS(Cascading Style Sheets) 및 JavaScript와 같은 스크립팅 언어와 같은 기술의 도움을 받을 수 있다.

최종적으로 웹에서 자유롭게 오갈 수 있는 웹 문서를 만드는 언어가 HTML이라고 정리할 수 있다.

<br>

### HTML의 구조

```HTML
<!DOCTYPE html>
<html lang="en">
		<head>
		    <meta charset="UTF-8">
		    <meta http-equiv="X-UA-Compatible" content="IE=edge">
		    <meta name="viewport" content="width=device-width, initial-scale=1.0">
		    <title>Document</title>
		</head>
		<body>
		    
		</body>
</html>
```

(VSCode에서 Emmit 플러그인을 설치했다면 `! + tab` 키를 이용하여 HTML의 기본 구조를 만들 수 있다)

- `<!DOCTYPE html>` : Document 타입이 html이라는 것을 정의한다. 예전에는 여기에 브라우저가 따라야 하는 중요한 RULE이나 버전 같은 것을 정의했다. 지금은 그냥 관습적으로 적고 있다.
- `<html>` : html 태그는 html 파일의 가장 상위에 있는 테그이다. 그리고 html 태그 안에서 head와 body 태그로 나누어 짐은 확인할 수 있다.
- `<head>` : 사용자에게 보여지는 UI 적인 요소는 없다. 구글에서 검색할 때 나오는 타이틀이나 부가 설명 그리고 북마크를 추가할 때 나오는 제목이나 아이콘들이 정의되어 있다 그리고 css 파일이 있다면 css 파일을 연결하는 것도 head 영역에서 진행이 된다. 즉 메타 데이터들을 정의한다.
- `<body>` : 사용자에게 보여주는 가장 중요한 최상의 컨테이너이다. 여기에 작성한 내용이 사용자에게 보여진다.

<br>

### HTML Element vs Tag

- HTML Element는 HTML을 이루는 빌딩 블록이다. 보통 요소로 번역된다.
- HTML Tag는 HTML element의 시작과 끝 부분이다.

혼용되어 사용하지만 정확한 의미를 알아두자.

![https://www.bluekatanasoft.com/wp-content/uploads/element-structure.png](https://www.bluekatanasoft.com/wp-content/uploads/element-structure.png)

[https://www.bluekatanasoft.com/wp-content/uploads/element-structure.png](https://www.bluekatanasoft.com/wp-content/uploads/element-structure.png)

<br>

## HTML Element

### 엘러먼트는 기능을 기준으로 구분할 수 있다.

- 텍스트와 관련된 엘러먼트
- 목록과 관련된 엘러먼트
- 표를 만드는 엘러먼트
- 멀티미디어 엘러먼트
- 폼 엘러먼트, 링크 엘러먼트

<br>

### 엘러먼트는 Box와 Item으로 구분할 수 있다.

- Item : 실제 사용자의 눈에 보인다.
    - a, video, button, audio, input, map, label, canvas, img, table..
- Box : 사용자의 눈에는 보이지 않는다. 섹션링을 통해 Item들을 정리한다.
    - header, section, footer, article, nav, div, aside, span, main, form..

<br>

### 엘러먼트는 블록 레벨과 인라인 레벨로 구분할 수 있다.

- 블록 레벨(block-level) : 태그를 사용해 엘러먼트를 삽입했을 때 혼자 한 줄을 차지한다.
    - p, h1 ~ h6, ul, ol, div, blockquote, form, hr, table, fieldset, address
- 인라인 레벨(inline-level) : 줄을 차지하지 않는다.
    - img, object, br, sub, sup, span, input, textarea, label, button

<br>

### Elements Reference

[HTML Element들의 자세한 사용법은 MDN Docs를 참조하자.](https://developer.mozilla.org/en-US/docs/Web/HTML/Element)

[링크를 참조하여 Box와 Item을 이용한 웹 사이트의 구조(HTML5)에 대해 이해해 보자.](https://developer.mozilla.org/en-US/docs/Learn/HTML/Introduction_to_HTML/Document_and_website_structure)

[태그를 통해 작성한 HTML 문서가 유효한 지를 확인하고 싶다면 validator 사이트를 이용하자.](https://validator.w3.org/)

<br>

### Character entities

어떤 문자들(characters)은 HTML에 의해 예약(reserved)되어 있다. 만약 `<` 문자나 `>` 문자를 텍스트로 사용하려고 하면, 브라우저는 HTML 태그와 혼동한다. Character entities는 HTML에 의해 예약된 문자들을 나타내기 위해서 사용한다.

**자주 사용하는 Character entities**

`' '(space 1칸)` &nbsp; (non-breaking space)

`<` &lt; &LT;

`>`  &gt; &GT;

Character entities Reference : [https://dev.w3.org/html5/html-author/charref](https://dev.w3.org/html5/html-author/charref)