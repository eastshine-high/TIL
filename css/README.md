# CSS
- Cascading Style Sheets의 약자로 '위에서 아래로 흐르는 스타일 시트(문서)'라는 뜻이다. 이는 웹 제작자, 사용자, 브라우저 스타일 간의 충돌을 막기 위해서 이다.
- 웹 문서의 글꼴, 색상, 여백 등의 스타일을 기술하는 언어이다.

<br>

### 스타일 우선순위

1. !important 선언을 한 사용자 스타일
2. !important 선언을 한 제작자 스타일
    - !important는 가능한 사용하지 않는 것이 좋다.
3. 제작자(author) 스타일
4. 사용자(user) 스타일
5. 사용자 에이전트(User Agent)
    - 제작자 : 실제 사이트를 제작하는 사이트 개발자가 작성한 CSS.
    - 사용자 : 사이트를 방문하는 일반 사용자들이 작성한 CSS(브라우저에서 스타일을 수정할 수 있다).
    - 사용자 에이전트 : 일반 사용자의 환경, 즉 브라우저의 자체 스타일.

<br>

### CSS 형식

하나의 CSS는 선택자(selector)와 선언 블록으로 구성된다.

![https://s3.ap-northeast-2.amazonaws.com/opentutorials-user-file/module/484/1361.gif](https://s3.ap-northeast-2.amazonaws.com/opentutorials-user-file/module/484/1361.gif)
