# URI

## ****URI(Uniform Resource Identifier)****

****"URI는 로케이터(locator), 이름(name) 또는 둘 다 추가로 분류될 수 있다"****

Ref : [https://www.ietf.org/rfc/rfc3986.txt](https://www.ietf.org/rfc/rfc3986.txt) ****- 1.1.3. URI, URL, and URN****

### ****URI 의미****

**U**niform: 리소스 식별하는 통일된 방식

**R**esource: 자원, URI로 식별할 수 있는 모든 것(제한 없음)

**I**dentifier: 다른 항목과 구분하는데 필요한 정보

### ****URL****

리소스 식별자의 가장 흔한 형태다. URL은 특정 서버의 한 **리소스에 대한 구체적인 위치를 지정**한다. URL은 리소스가 정확히 어디에 있고 어떻게 접근할 수 있는지 분명히 알려준다.

### URN

콘텐츠를 이루는 한 리소스에 대해, 그 **리소스의 위치에 영향 받지 않는 (유일무이한)이름을 부여**한다. 이 위치 독립적인 URN은 리소스를 여기저기로 옮기더라도 문제없이 동작한다. 리소스가 그 이름을 변하지 않게 유지하는 한, 여러 종류의 네트워크 접속 프로토콜로 접근해도 문제 없다. 예를 들어 ISBN:8960777331 (어떤 책의 isbn URN)가 어디에 있거나 상관없이 그것을 지칭하기 위해 사용할 수 있다. URN은 여전히 실험 중인 상태고 아직 널리 채택되지 않았다.

> 통상적인 관례에서는 특별한 언급이 없으면 URI와 URL은 같은 의미로 사용한다.
> 

## URL syntax

`scheme`://[`userinfo`@]`host`[:`port`][/`path`][?`query`][#`fragment`]

### ****scheme****

> ****`scheme`://[userinfo@]host[:port][/path][?query][#fragment]
`https`://www.google.com:443/search?q=hello&hl=ko**
> 
- 스킴은 주어진 리소스에 어떻게 접근하는지 알려주는 중요한 정보다. 이는 **URL을 해석하는 애플리케이션이 어떤 프로토콜(http, https, ftp 등등)을 사용하여 리소스를 요청해야 하는지 알려준다.** 위에서 예로 사용하고 있는 URL의 스킴은 `https`다.
- 스킴 컴포넌트는 알파벳으로 시작해야 하고 URL의 나머지 부분들과 첫 번째 `:` 문자로 구분한다. 스킴 명은 대소문자를 가리지 않는다(`HTTPS`, `https` 모두 가능).
- 스킴의 종류는 다양하다. 스킴의 종류는 Wiki를 참조하자.
    - [https://en.wikipedia.org/wiki/List_of_URI_schemes](https://en.wikipedia.org/wiki/List_of_URI_schemes)

### userinfo

> scheme://**[`userinfo@`]**host[:port][/path][?query][#fragment]
https://www.google.com:443/search?q=hello&hl=ko
> 
- URL에 사용자정보를 포함해서 인증한다.
- 거의 사용하지 않는다.

### ****host****

> scheme://[userinfo**@**]`host`[:port][/path][?query][#fragment]
https://`www.google.com`:443/search?q=hello&hl=ko
https://`velog.io`/@eastshine-high
http://`161.58.228.45`:80/index.html
> 
- 호스트는 접근하려고 하는 리소스를 가지고 있는 인터넷상의 호스트 장비를 가리킨다.
- 도메인명 또는 IP 주소를 직접 사용 가능하다.

### PORT

> scheme://[userinfo**@**]host[`:port`][/path][?query][#fragment]
https://www.google.com`:443`/search?q=hello&hl=ko
http://localhost`:3000`
> 
- 포트는 서버가 열어놓은 네트워크 포트를 가리킨다.
- 포트는 생략 가능하다(일반적으로 생략).
    - 생략시, 내부적으로 TCP 프로토콜을 사용하는 HTTP는 80 포트, HTTPS는 443 포트를 사용한다.

### path

> scheme://[userinfo**@**]host[:port][/`path`][?query][#fragment]
https://www.google.com:443/`search`?q=hello&hl=ko
> 
- 경로는 리소스가 서버의 어디에 있는지 알려준다.
- 유닉스 파일 시스템의 파일 경로와 유사할 수 있다.
    - `/home/file1.jpg`
- 보통 계층적 구조로 이뤄진다.
    - `/members`
    - `/members/100`, `/items/iphone12`

### query

> scheme://[userinfo**@**]host[:port][/path][`?query`][#fragment]
https://www.google.com:443/search`?q=hello&hl=ko`
> 
- 요청받을 리소스 형식의 범위를 좁히기 위해서 질문이나 질의를 받을 수 있다(주로 데이터베이스 같은 서비스).
- 물음표 `?`의 우측에 있는 값들을 질의라 부른다.
- `key=value` 형태를 띄고 `&`로 추가 가능하다.
- query parameter, query string 등으로 불린다(웹서버에 제공하는 파라미터, 문자 형태).

### fragment

> scheme://[userinfo@]host[:port][/path][?query][`#fragment`]
****https://docs.spring.io/spring-boot/docs/current/reference/html/features.html`#features.internationalization`
> 
- 리소스의 특정 부분을 가리킬 수 있도록, URL은 리소스 내의 조각을 가리킬 수 있는 fragment를 제공한다.
- HTML 내부 북마크 등에 사용된다.
- 서버에 전송하는 정보는 아니다.

### parameter

많은 scheme이 객체에 대한 호스트 및 경로 정보만으로는 리소스를 찾지 못한다. 서버가 어떤 포트를 열어놓고 있는지, 리소스에 접근하기 위해 사용자 이름과 비밀번호를 명시했는지 여부 외에도 **많은 프로토콜이 더 많은 정보를 요구**한다.

URL을 사용하는 애플리케이션이 리소스에 접근하려면 프로토콜 파라미터가 필요하다. 프로토콜 파라미터가 없으면, 다른 한편에 있는 서버는 그 요청을 잘못 처리하거나 처리를 하지 않을 것이다. 바이너리와 텍스트, 총 두 개의 포맷을 지원하는 FTP 예로 들어보자. 사용자는 바이너리 이미지가 텍스트 형식으로 전송되는 것을 원하지 않는다. 이미지가 엉망이 될 게 뻔하기 때문이다.

**URL의 파라미터는, 애플리케이션이 서버에 정확한 요청을 하기 위해 필요한 입력 파라미터를 받는데 사용한다.** 이 컴포넌트는 이름/값 쌍의 리스트로 URL 나머지 부분들로부터 `;` 문자로 구분하여 URL에 기술한다. 이를 통해 애플리케이션이 리소스에 접근하는데 필요한 어떤 추가 정보든 전달할 수 있다. 예를 들면 다음과 같다.

```
ftp;//prep.ai.mit.edu/pub/gnu;type=d
```

이 경우 이름은 ‘type’이고, 값은 ‘d’인 type=d라는 단 한 개의 파라미터를 전달한다.

## 참조

<데이빗 고울리 외, HTTP 완벽 가이드, 인사이트>

<김영한, (강의) 모든 개발자를 위한 HTTP 웹 기본 지식, 인프런>
