# Flexbox

Flexbox는 레이아웃을 만들 수 있는 다양한 방법들 중 가장 유연하고 쉬운 방법 중 하나입니다. 플렉스박스는 이름 그대로 유연한 박스 모델을 제공하며, 특히 정렬 기능이 탁월하기 때문에 여러가지 속성 값을 조합해 위치와 정렬을 맞추는 수고로움을 하지 않아도 되는 장점이 있습니다.

Flexbox를 이용하여 박스가 커지면 박스 안의 아이템들이 어떤 식으로 커지면서 공간을 매꿔야 할 지, 박스가 작아지면 박스 안의 아이템들이 어떤 식으로 작아지면서 공간을 매꿔야 할 지 등을 정의할 수 있습니다.

CSS Grid와 가장 다른 점은 그리드가 테이블 모양으로 정형화된 형태를 배치하는데 강점이 있는 반면, 플렉스박스는 비정형 레이아웃을 좀 더 자유롭고 결하게 배치할 수 있는 장점이 있습니다.

Flexbox는 컨테이너에 적용되는 속성과 컨테이너 안의 각각의 아이템들에 적용할 수 있는 속성으로 나눌 수 있습니다.

<br>

## Properties for the Parent(flex container, 컨테이너 속성)

### Flexbox 만들기

Flexbox를 만드려면 먼저 화면 배치 방법을 결정하는 `display` 속성을 이용해 컨테이너를 flex 형태로 지정해야 합니다.

```css
.container{
    // 플렉스 컨테이너를 위한 속성 값으로는 flex와 inline-flex가 있다.
    display : flex;
}
```

<br>

### 중심축 지정하기

그 다음으로 중요한 것은 Flexbox의 아이템을 배치할 방향(중심축)을 지정하는 것입니다. `flex-direction` 속성을 사용해 플렉스 아이템 중심축을 지정합니다. 따로 지정하지 않으면 기본 값인 row로 인식합니다.

[https://samanthaming.gumlet.io/flexbox30/9-flex-direction.jpg.gz](https://samanthaming.gumlet.io/flexbox30/9-flex-direction.jpg.gz)

[https://samanthaming.gumlet.io/flexbox30/9-flex-direction.jpg.gz](https://samanthaming.gumlet.io/flexbox30/9-flex-direction.jpg.gz)

<br>

### flex-wrap

기본적으로 flex 아이템들은 주측 방향을 따라 한 줄로 배치됩니다. 이 속성은 크기가 작아졌을 때도 유지됩니다. `flex-wrap` 속성값을 `wrap`으로 설정할 경우 크기가 작아졌을 때 반응적으로 다음 줄로 넘어가록 됩니다

- no-wrap : 플렉스 아이템들을 한 줄에 표시합니다. 기본 값입니다.
- wrap : 플렉스 아이템들을 여러 줄에 표시합니다.
- wrap-reverse : 플렉스 아이템들을 여러 줄에 표시하되 기존 방향과 반대로 배치합니다.

[https://css-tricks.com/snippets/css/a-guide-to-flexbox/#flex-wrap](https://css-tricks.com/snippets/css/a-guide-to-flexbox/#flex-wrap)

```css
.container{
    //박스를 flex로 만든다.
    display : flex;
    //flex의 방향 즉 중심축 설정
    /* flex-direction : row(default), row-reverse, column, comlumn-reverse */
    //즉 한 줄이 가득차면 다음 줄로 넘어갈 것인 지.
    /* flex-wrap : nowrap(default), wrap, wrap-reverse; */

    // flex-direction 속성과 flex-wrap 속성을 한 번에 정의
    flex-flow: column nowrap

    // cf. border style을 한 번에 정의
    /* border : 1px solid black; */
}
```

<br>

### 주측에서의 아이템 배치 방법 지정

`justify-content` 속성을 이용하면 플렉스 항목을 주측 방향으로 배치할 때의 배치 기준을 지정할 수 있습니다.

다음과 같은 속성값이 있습니다.

[https://css-tricks.com/snippets/css/a-guide-to-flexbox/#justify-content](https://css-tricks.com/snippets/css/a-guide-to-flexbox/#justify-content)

- flex-start : 중심축의 처음에서 배치
- flex-end : 아이템들의 순서는 유지 중심축의 끝에서 배치
- center : 중심축의 중앙에 배치
- space-around : 아이템들의 스페이싱을 넣어준다. 가장 양쪽은 스페이스가 1개 만큼이다.
- space-evenly : 똑같은 간격으로 스페이싱
- space-between : 왼쪽과 오른쪽은 끝에 배치한 뒤에 중간 아이템들만 스페이싱

<br>

### 교차축(반대축)에서의 아이템 배치 방법 지정

`align-items` ****속성을 이용하면 플렉스 항목을 반대축 방향으로 배치할 때의 배치 기준을 지정할 수 있습니다.

[https://css-tricks.com/snippets/css/a-guide-to-flexbox/#align-items](https://css-tricks.com/snippets/css/a-guide-to-flexbox/#align-items)

<br>

## Properties for the Children(flex items, 아이템 속성)

### 컨테이너의 사이즈가 커졌을 때 얼마나 어떻게 늘어야 되는 지를 지정

- `flex-grow` 속성을 이용하면 가변 너비에 대한 대응을 쉽게 할 수 있기 때문에 반응형 레이아웃을 빠르게 만들 수 있습니다.
- Flexbox 너비에서 한 행에 표시되는 아이템들의 너비 합을 뺀 **나머지 너비를 아이템들에 분배하는 비율**을 말합니다. 기본 값은 0이며, 0은 남은 너비가 분배되지 않고, 처음 크기 그대로 유지합니다.
- 예를 들어 사이드바 너비를 `280px`로 고정하고, 본문 영역 너비가 웹 브라우저 너비 변경에 따라 자동으로 변하도록 하려면 사이드바의 `flex-grow` 속성 값은 `0`으로, 본문의 `flex-grow` 속성 값은 `1`로 설정하면 됩니다.

[https://css-tricks.com/snippets/css/a-guide-to-flexbox/#flex-grow](https://css-tricks.com/snippets/css/a-guide-to-flexbox/#flex-grow)

<br>

### 컨테이너의 사이즈가 작아졌을 때 얼마나 어떻게 줄어야 되는 지를 지정

- `flow-shrink` 속성은 레이아웃을 벗어난 아이템 너비를 분배해서 줄이는 방법을 지정합니다.
- `flex-shrink` 속성은 Flexbox에 `flex-wrap: wrap` 속성을 부여한 경우 적용되지 않습니다.
- 레이아웃 너비를 넘을 경우 끝에 걸치는 아이템은 다음 행의 왼쪽부터 다시 배치되기 때문에 아이템들의 너비 합이 레이아웃 영역을 넘는 상황이 생기지 않습니다.
- `flex-shrink` 속성은 기본 값이 1이기 때문에 속성을 정의하지 않아도 자동으로 아이템이 축소되어 적용됩니다.
- 자동으로 아이템 너비가 축소되지 않도록 하려면 반드시 `flex-shrink: 0`을 아이템에 별도로 선언해야 합니다.

[https://css-tricks.com/snippets/css/a-guide-to-flexbox/#flex-shrink](https://css-tricks.com/snippets/css/a-guide-to-flexbox/#flex-shrink)

<br>

### flex-basis

- 아이템들이 공간을 얼마나 차지해야 하는 지 조금 더 세부적으로 명시할 수 있게 도와주는 속성
- 기본 값은 `auto`이다. 이 경우 `flex-grow`, `flex-shrink` 속성 값에 맞추어 아이템이 변형됩니다.
- `flex-grow`, `flex-shrink` 속성을 이 속성을 이용해 한번에 지정할 수 있다. 속성 값은 비율 `%`이며 소수점 사용이 가능하다.

<br>

### align-self

- 컨테이너 레벨에서는 `justify-content` , `align-items`, `align-content`을 이용하여 아이템들을 골고루 배치할 수 있다면, 아이템 레벨에서는 `align-self` 를 이용해서 아이템 별로 아이템을 정렬할 수 있다.

```css
.item2 {
    //아이템들이 공간을 얼마나 차지해야하는 지 세부적으로 도와준다.
    flex-basis : 30%;
    //컨테이너 레벨에서는 justify-content, align-item, align-content를 통해서 아이템들을
    //골고루 배치할 수 있다면 이 align-self 를 이용해서 아이템별로 아이템들을 정렬 할 수가 있다.
    align-self : center;    
}
```

<br>

---

### Reference

드림코딩(youtube) : [https://www.youtube.com/watch?v=7neASrWEFEM&list=PLv2d7VI9OotQ1F92Jp9Ce7ovHEsuRQB3Y&index=9&t=1129s](https://www.youtube.com/watch?v=7neASrWEFEM&list=PLv2d7VI9OotQ1F92Jp9Ce7ovHEsuRQB3Y&index=9&t=1129s)

<HTML5 + CSS3 웹 표준의 정식, 고경희, 이지스 퍼블리싱>

<HTML&CSS 마스터북, 어포스트, 어포스트>
