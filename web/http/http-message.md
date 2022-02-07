# HTTP message

**HTTP 메시지는 HTTP 애플리케이션 간에 주고받은 데이터의 블록**들이다. 이 데이터의 블록들은 메시지의 내용과 의미를 설명하는 텍스트 메터 정보로 시작하고 그 다음에 선택적으로 데이터가 올 수 있다. 이 메시지는 클라이언트, 서버, 프락시 사이를 흐른다.

## Message Format

HTTP 메시지는 단순한, 데이터의 구조화된 블록이다. 아래 코드는 [공식 스펙](https://datatracker.ietf.org/doc/html/rfc7230#section-3)(RFC5322)에 정의된 형식이다.

```
HTTP-message   = start-line
                      *( header-field CRLF )
                      CRLF
                      [ message-body ]
```

각 메시지는 클라이언트로부터의 요청이나 서버로부터의 응답 중 하나를 포함한다. 메시지는 시작줄, 헤더 블록, 본문 이렇게 세 부분으로 이루어진다. 시작줄은 이것이 어떤 메시지인지 서술하며, 헤더 블록은 속성을, 본문은 데이터를 담고 있다. 본문은 아예 없을 수도 있다.

**시작줄(start-line)과 헤더는 그냥 줄 단위로 분리된 아스키 문자열이다.** 각 줄은 캐리지 리턴(ASCII 13)과 개행 문자(ASCII 10)로 구성된 두 글자의 줄바꿈 문자열으로 끝난다. 이 줄바꿈 문자열은 ‘CRLF’라고 쓴다. HTTP 명세에 따른다면 줄바꿈 문자열은 CRLF이지만 견고한 애플리케이션이라면 그냥 개행 문자로 받아들일 수 있어야 한다. 헤더나 엔터티 본문이 없더라도 HTTP 헤더의 집합은 항상 빈 줄(그냥 CRLF)로 끝나야 한다.

![https://www.oreilly.com/library/view/http-the-definitive/1565925092/httpatomoreillycomsourceoreillyimages96838.png](https://www.oreilly.com/library/view/http-the-definitive/1565925092/httpatomoreillycomsourceoreillyimages96838.png)

엔터티 본문이나 메시지 본문(혹은 그냥 ‘본문')은 단순히 선택적인 데이터 덩어리다. 시작줄이나 헤더와는 달리, 본문은 텍스트나 이진 데이터를 포함할 수도 있고 그냥 비어있을 수도 있다.

위의 그림에서 헤더는 본문에 대한 꾀 많은 정보를 준다. Content-Type 줄은 본문이 무엇인지 말해준다(이 예에서는 플레인 텍스트 문서다). Content-Length 줄은 본문의 크기를 말해준다. 이 예에서는 19 Byte이다.

### Request message

요청 메시지의 형식은 다음과 같다.

```
<method> SP(공백) <request-target> SP <HTTP-version> CRLF(엔터)
<헤더>

<엔터티 본문>
```

```
GET /search?q=hello&hl=ko HTTP/1.1
Host: www.google.com
```

### Response message

응답 메시지의 형식은 다음과 같다(시작줄에서만 문법이 다르다).

--- 

## 참조

<데이빗 고울리 외, HTTP 완벽 가이드, 인사이트>
