# 단위(CSS Units)

CSS 단위는 크게 절대적 단위(Absoluete length units)와 상대적 단위(Relative length units)로 나눌 수 있다.

<br>

## Absoluete length units(절대적 단위)

절대적인 유닛은 cm 부터 시작해서 px 까지 다양한 것들이 있는데 사실은 이렇게 많은 것들을 CSS에서는 다양하게 쓰지 않는다. 픽셀을 제외한 나머지 단위들은 물리적인 세상에만 의미가 있다.

<br>

### 픽셀(pixel, px) 또는 화소

픽처 엘리먼트(Picture Element)'의 준말이며, 어휘 '화소' 역시 이를 직역한 것이다. 픽셀은 많은 분들이 알고 계시겠지만 **모니터 위에서 화면에 나타낼 수 있는 가장 작은 단위**를 말한다. 절대적인 absolute 유닛중에 픽셀이 가장 많이 쓰인다. 이 **픽셀을 쓰게 되면 발생할 수 있는 문제점은 컨테이너에 사이즈가 변경되어도 컨텐츠가 그대로 고정된 값으로 유지되는 것**이다.

대부분은 이런 고정된 px 보다는 %를 이용한다. 예를들어 부모의 80% 를 표기하는 방식으로 많이 사용되어지고 있다.

<br>

## Relative length units(상대적 단위)
<table class="ws-table-all notranslate">
  <tr>
    <th style="width:12%">Unit</th>
    <th>Description</th>    
  </tr>
  <tr>
    <td>em</td>
    <td>Relative to the font-size of the element (2em means 2 times the size of the current font)</td>    
  </tr>
  <tr>
    <td>ex</td>
    <td>Relative to the x-height of the current font (rarely used)</td>    
  </tr>
  <tr>
    <td>ch</td>
    <td>Relative to the width of the &quot;0&quot; (zero)</td>    
  </tr>
  <tr>
    <td>rem</td>
    <td>Relative to font-size of the root element</td>
  </tr>
  <tr>
    <td>vw</td>
    <td>Relative to 1% of the width of the viewport*</td>    
  </tr>
  <tr>
    <td>vh</td>
    <td>Relative to 1% of the height of the viewport*</td>
  </tr>
  <tr>
    <td>vmin</td>
    <td>Relative to 1% of viewport's* smaller dimension</td>
  </tr>
  <tr>
    <td>vmax</td>
    <td>Relative to 1% of viewport's* larger dimension</td>
  </tr>
  <tr>
    <td>%</td>
    <td>Relative to the parent element</td>
  </tr>
    </table>

<br>

## 이 단위는 어디에 사용하면 좋을까?

- 부모 엘러먼트 크기(size)에 상대적 단위 : %, em
- 브라우져에 상대적인 단위 : v*, rem
- 엘러먼트의 너비 혹은 높이에 따른 단위 : %, v*
- 폰트 사이즈에 따른 단위 : em, rem
    - 사용하는 컴포넌트가 부모 요소에 따라서 달라져야 한다면 : em
    - 사용하는 컴포넌트가 어느 위치에 상관없이 동일해야 한다면 : rem
    - 폰트 사이즈에서는 rem 사용을 권장. em은 엘러먼트의 계층에 따라서 폰트 사이즈가 유동적이기 때문에 폰트 사이즈를 계산하기가 복잡하다.

<br>

## 상대적 크기 계산하기

- 결국 브라우저에서는 px로 변환되어 스크린에 보여준다.
- 브라우저 지정 HTML의 기본 Font Size : 16px  → 1em = 16px
- HTML 엘러먼트의 기본 %는 100%

<br>

### em(relative to parent element)

부모 요소에서 지정한 폰트의 대문자 M의 너비를 1em으로 지정한 것

<br>

### %(parent related)

부모 요소에 상대적인 크기

```html
.parent{
    font-size: 8em;  → 16px(HTML) ✖️ 8em = 128px
    (8em은 800%와 같은 의미)
}
```

```html
.child {
    font-size: 0.5em; // 128px(.parent) ✖️ 0.5em(50%) = 64px
    (0.5em은 50%와 같은 의미)
}
```

<br>

### rem(relative to root element)ㅊㄴ

root인 HTML의 기본 font size인 16px에 상대적인 값

```html
.parent{
    font-size: 8rem;  // 16px(HTML) ✖️ 8rem = 128px
}
```

```html
.child{
    font-size:0.5rem  //16px(HTML) ✖️ 0.5rem = 8px
}
```

<br>

### vw(viewport with)

브라우저의 너비에 상대적인 값(`%` 는 부모 요소의 상대적으로 반응한다)

<br>

### vh(viewport height)

브라우저 높이의 상대적인 값(`%` 는 부모 요소의 상대적으로 반응한다)

<br>

---

### Reference

드림코딩 : [https://www.youtube.com/watch?v=7Z3t1OWOpHo&list=PLv2d7VI9OotQ1F92Jp9Ce7ovHEsuRQB3Y&index=21](https://www.youtube.com/watch?v=7Z3t1OWOpHo&list=PLv2d7VI9OotQ1F92Jp9Ce7ovHEsuRQB3Y&index=21)

W3Schools : https://www.w3schools.com/cssref/css_units.asp