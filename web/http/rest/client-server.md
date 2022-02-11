# client-server

웹 콘텐츠는 웹 서버에 존재한다. 웹 서버는 HTTP 프로토콜로 의사소통하기 때문에 보통 HTTP 서버라고 불린다. 이들 웹 서버는 인터텟의 데이터를 저장하고, HTTP 클라이언트가 요청한 데이터를 제공한다. 클라이언트는 서버에게 HTTP 요청을 보내고 서버는 요청된 데이터를 HTTP 응답으로 돌려준다. HTTP 클라이언트와 HTTP 서버는 월드 와이드 웹의 기본 요소다.

![https://darvishdarab.github.io/cs421_f20/assets/images/client-server-1-d85a93ea16590c10bed340dd78294d0d.png](https://darvishdarab.github.io/cs421_f20/assets/images/client-server-1-d85a93ea16590c10bed340dd78294d0d.png)

아마 독자들은 HTTP 클라이언트를 매일 이용하고 있을 것이다. 가장 흔한 클라이언트는 마이크로소프트 인터넷 익스플로러나 구글 크롬 같은 웨브라우저다. 웹 브라우저는 서버에게 HTTP 객체를 요청하고 사용자의 화면에 보여준다.

예를 들어, http://www.hive.com/index.html 페이지를 열어볼 때, 웹브라우저는 HTTP 요청(Request)을 www.hive.com 서버로 보내고 응답을 대기한다. 서버는 요청받은 객체(이 경우"/index.html”)를 찾고, 성공했다면 그것의 타입, 길이 등의 정보와 함께 HTTP 응답(Response)에 실어서 클라이언트에게 보낸다.

이 구조가 중요한 것은 **클라이언트와 서버가 독립적으로 진화할 수 있다**는 점이다. 클라이언트(PC, 모바일 등)는 UI를 그리는 데(사용성)에 집중한다. 서버는 비즈니스 로직, 데이터 혹은 요청 트레픽(서비스가 커지면 더 많은 서버가 필요할 수 있다) 등에 집중할 수 있다.

## 참조

<데이빗 고울리 외, HTTP 완벽 가이드, 인사이트>

<김영한, (강의) 모든 개발자를 위한 HTTP 웹 기본 지식, 인프런>
