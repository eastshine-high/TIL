# Layout

### 웹 사이트를 만들 때 가장 중요한 것은 무엇일까?

우리가 정의한 박스들을 원하는 위치에 원하는 사이즈로 배치하는 것이 중요하다.

<br>

### HTML 엘러먼트는 블록 레벨과 인라인 레벨로 구분할 수 있다.

- 블록 레벨(block-level) : 태그를 사용해 엘러먼트를 삽입했을 때 혼자 한 줄을 차지합니다. 너비나 마진, 패딩 등을 이용해 크기나 위치를 지정하려면 블록 레벨 요소여야 합니다.
    - **div**, p, h1 ~ h6, ul, ol, blockquote, form, hr, table, fieldset, address
- 인라인 레벨(inline-level) : 줄을 차지하지 않는 엘러먼트입니다. 나머지 공간에는 다른 엘러먼트가 올 수 있습니다. 컨텐츠 자체만을 꾸밉니다.
    - **span**, img, strong, object, br, sub, sup, input, textarea, label, button

<br>

## 박스 모델(box model) - 박스 형태의 콘텐츠

CSS에서 "box model"이라는 용어는 디자인과 레이아웃에 대해 이야기할 때 사용됩니다. **CSS box model은 기본적으로 모든 HTML 요소를 감싸는 상자**입니다. 여백, 테두리, 패딩 및 실제 콘텐츠로 구성됩니다. 아래 이미지는 box model을 보여줍니다.

![https://s3.ap-northeast-2.amazonaws.com/opentutorials-user-file/module/484/1349.gif](https://s3.ap-northeast-2.amazonaws.com/opentutorials-user-file/module/484/1349.gif)

<br>

### **박스의 구성 요소**

- content : 요소가 담고 있는 내용
- padding : 컨텐츠 안의 스페이싱(content와 border사이의 간격)
- border : 테두리 선
- margin : 컨텐츠 밖의 스페이싱(border와 다른 요소 사이의 간격)

<br>

### width, height 속성 - 콘텐츠 영역의 크기

박스 모델에서 콘텐츠 영역의 크기를 지정할 때는 너비를 지정하는 width 속성과 높이를 지정하는 height 속성을 사용합니다.

속성 값: <크기>, <백분율>, <auto>(높이와 너비 값이 콘텐츠 양에 따라 자동으로 결정됩니다. 기본 값입니다.)

<br>

### 엘러먼트의 크기

Total element width = width 값 + 좌우 패딩 + 좌우 테두리 + 좌우 마진

Total element height = height 값 + 위아래 패딩 + 위아래 테두리 + 위아래 마진

```css
.bx1{
	width:200px;
	height:100px;
	background: #ff6a00;
	padding:10px;
	border: 5px #ccc solid;
}
```

`.bx1`의 너비는 width(200px) + padding(10px x 2) + border(5px x 2) = 230px이다. 정확히는 개발자 도구(ctrl + shift + i )를 확인하자.

<br>

### box-sizing 속성 - 박스 너비의 기준 정하기

웹 문서에 여러 엘러먼트를 배치하려면 각 엘러먼트의 너비 값을 기준으로 해야 하는데 width 속성 값이 콘텐츠 영역의 너비만 나타내기 때문에 패딩이나 테두리는 따로 계산해야 합니다. 이 때 box-sizing 속성을 사용하면 **width 속성 값을 콘텐츠 영역 너비값으로 사용할지, 패딩이나 테두리까지 포함시켜 사용할지 결정**할 수 있습니다(마진은 너비에 포함되지 않습니다).

- 속성 값
    - content-box : width 속성 값을 콘텐츠 영역 너비 값으로 사용합니다. 기본 값입니다.
    - border-box : width 속성 값을 콘텐츠 영역에 테두리까지 포함한 박스 모델 전체 너비 값으로 사용합니다.

<br>

## display 속성 - 화면 배치 방법 결정하기

**display 속성은 엘러먼트가 화면에 어떻게 보일지를 지정할 때 사용합니다.** 이 속성을 사용하면 블록 레벨 요소를 인라인 레벨 요소로 바꾸거나 인라인 레벨 요소를 블록 레벨 요소로 바꿀 수 있습니다. 이 방법은 언제 필요할까요? 세로로 표시되는 목록을 가로 내비게이션으로 바꿀 때, 한 줄로 표시되는 이미지에 여백과 테두리를 추가해 갤러리로 표시할 때 이 방법을 사용합니다.

HTML5를 기준으로 display 속성 값은 20개가 넘습니다. 그만큼 다양하고 복잡한 화면 레이아웃을 만들어 내는데 있어서 display 속성은 중요한 역할을 합니다. 아래는 실제 사용되는 빈도를 기준으로 많이 사용하는 기초 속성 값을 정리합니다.

<br>

### 주요 속성

- `Inline` : 엘러먼트가 <span> 태그처럼 동작합니다. 이어진 여러 엘러먼트에 적용하면 한 줄로 엘러먼트들이 나열됩니다. <span>태그와 마찬가지로 크기 값을 지정할 수 없습니다. 엘러먼트 안에 들어있는 컨텐츠의 길이나 크기에 따라 줄의 너비가 정해지기 때문에 크기를 제한할 필요가 있는 요소에는 적합하지 않습니다.
- `block` : 요소가 <div> 태그처럼 동작합니다. 크기 값을 지정할 수 있습니다.
- `inline-block` : <span> 요소처럼 동작하지만 크기 값을 지정할 수 있습니다. 이어진 여러 요소에 적용하면 원하는 크기를 가진 요소들을 한 줄로 배치할 수 있습니다. 배치를 적당히 잘하면 그리드 형태로 배치할 수도 있습니다.
- `flex` : 자식 요소들을 가로 또는 세로 방향으로 `inline-block` 속성처럼 배치해주며, 추가 flex 속성들을 사용해 자식 요소들의 여백과 크기 등을 한번에 설정할 수 있습니다. 박스 형태의 영역을 가지는 많은 아이템들을 한방향으로 정렬해 배치하기 편리하기 때문에, 방응형 레이아웃을 만드는데 필수 속성입니다.
- `grid` : 자식 요소들을 격자 형태로 배치할 수 있도록 해주는 레이아웃 속성입니다. 격자들을 병합해서 새로운 구획을 만들 수 있어 다양한 배치 구조를 만들 수 있습니다. 가장 최신 레이아웃 속성으로, 간편하고 빠른 레이아웃 생성이 최대 장점이지만, 인터넷 익스플로어 호환성 유지를 위해 예외 처리를 해야 하는 CSS 코드 양이 너무 많은 단점이 있습니다.
- `contents` : 조금 독특한 속성값인데 사용하면 해당 요소가 사라지고, 자식 요소들이 부모 요소 DOM의 자식으로 올라가 붙게 됩니다. 일종의 투명 컨테이너 같은 개념으로 자식 요소들을 하나로 묶어서 관리하기 위한 용도로 사용합니다.
- `initial` : 기본값
- `none` : 이 속성 값을 지정하면 해당 요소를 화면에 아예 표시하지 않습니다. `visibility:hidden;` 도 비슷한 역할을 하는데 `visibility` 속성은 화면에서 감추기만 할 뿐 원래 요소가 있는 공간은 그대로 차지하지만 `display:none;`은 아예 공간조차 차지하지 않습니다.

<br>

## position - 배치 방법 지정하기

position 속성은 웹 문서 안의 요소들을 자유자재로 배치해 주는 속성으로 HTML과 CSS를 이용해 웹 문서를 만들 때 중요하게 사용하는 속성 중 하나입니다.

![https://cdn.educba.com/academy/wp-content/uploads/2019/12/CSS-Position.jpg](https://cdn.educba.com/academy/wp-content/uploads/2019/12/CSS-Position.jpg)

[https://cdn.educba.com/academy/wp-content/uploads/2019/12/CSS-Position.jpg](https://cdn.educba.com/academy/wp-content/uploads/2019/12/CSS-Position.jpg)

### static

- position의 기본 값이다.
- 각 엘러먼트가 html에 정의된 순서대로 브라우저 상에 자연스럽게 보여지는 것을 의미한다.
- top이나 right, bottm, left 같은 속성을 사용할 수 없다. static을 제외한 나머지 속성 값에서는 좌표 속성을 이용해 각 요소의 위치를 조절할 수 있다.

### relative

- 원래 있어야 하는 자리(static)에서처럼 문서의 흐름대로 배치되지만 top이나 right, bottm, left 같은 속성을 사용하여 위치를 지정할 수 있다. ex) left: 20px; top:20px

### absolute

- 내가 담겨있는 상자(박스) 안에서 위치를 움직이는 것을 의미한다.
- 이 기준이 되는 박스는 가까운 부모 엘러먼트나 조상 엘러먼트 중 position 속성이 relative인 요소이다.

### fixed

- (상자에서 완전히 벗어나서)페이지(윈도우)상에서 위치를 옮겨가는 것을 말합니다. 즉 기준이 되는 박스가 부모 엘러먼트가 아닌 브라우저 창이 된다.

### sticky

- 원래 있어야 되는 자리에 있으면서 대신에 스크롤링 되어도 없어지지 않고 원래 있던 자리에 그대로 붙어있는 것을 의미한다.

<br>

---

## reference

box model : [https://www.w3schools.com/css/css_boxmodel.asp](https://www.w3schools.com/css/css_boxmodel.asp)

box-sizing : [https://developer.mozilla.org/en-US/docs/Web/CSS/box-sizing](https://developer.mozilla.org/en-US/docs/Web/CSS/box-sizing)

생활코딩 : [https://opentutorials.org/module/484/4121](https://opentutorials.org/module/484/4121)

드림코딩(youtube) : [https://www.youtube.com/watch?v=jWh3IbgMUPI&list=PLv2d7VI9OotQ1F92Jp9Ce7ovHEsuRQB3Y&index=8&t=547s](https://www.youtube.com/watch?v=jWh3IbgMUPI&list=PLv2d7VI9OotQ1F92Jp9Ce7ovHEsuRQB3Y&index=8&t=547s)

<HTML5 + CSS3 웹 표준의 정식, 고경희, 이지스 퍼블리싱>

<HTML&CSS 마스터북, 어포스트, 어포스트>

<br>

### Cf.

사용하고자 하는 속성값들이 브라우저에서 사용할 수 있는 지 확인 : [https://caniuse.com/](https://caniuse.com/)

해외에서는 explore는 배제하고 개발중이다. 그럼에도 지원이 안 되는 브라우저를 위해서 PostCSS라는 프레임워크를 사용하면 뒤의 버전들을 위한 수고적인 처리를 해준다. 따라서 배우는 시기에는 최신의 것들을 많이 써보는 것을 추천한다.
