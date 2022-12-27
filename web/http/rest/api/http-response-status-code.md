# HTTP response status code

이번 페이지에서는 [김영한님의 인프런 강의 - HTTP 웹 기본 지식](https://www.inflearn.com/course/http-웹-네트워크) 을 바탕으로, 자주 사용하는 HTTP 응답 상태 코드에 대하여 정리하였습니다. 여기서 정리하지 못한 HTTP 상태 코드에 대한 설명은 [mozilla.org](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status) 에서 참조하실 수 있습니다.

HTTP 응답 상태 코드는 HTTP 요청이 성공적으로 완료되었는지 여부를 나타냅니다. 응답은 5개 클래스로 그룹화됩니다.

- 1xx (Informational responses): 요청이 수신되어 처리중
- 2xx (Successful responses): 요청 정상 처리
- 3xx (Redirection messages): 요청을 완료하려면 추가 행동이 필요
- 4xx (Client error responses): 클라이언트 오류, 잘못된 문법등으로 서버가 요청을 수행할 수 없음
- 5xx (Server error responses): 서버 오류, 서버가 정상 요청을 처리하지 못함

## 1xx (Informational)

요청이 수신되어 처리중을 의미합니다. 거의 사용하지 않습니다.

## 2xx(Successful)

클라이언트의 요청을 성공적으로 처리함을 의미합니다.

- 200 OK - 요청 성공
- 201 Created - 요청에 성공해서 새로운 리소스가 생성됨
- 202 Accepted - 요청이 접수되었으나 처리가 완료되지 않을 경우
- 204 No Content - 서버가 요청을 성공적으로 수행했지만, 응답 페이로드 본문에 보낼 데이터가 없음

### 200 OK - 요청 성공

가장 많이 사용하며 흔히 볼 수 있는 상태코드입니다.

### 201 Created - 요청 성공해서 새로운 리소스가 생성됨

요청

```java
POST /members HTTP/1.1
Content-Type: application/json
{
	"username": "young",
	"age": 20
}
```

응답

```java
HTTP/1.1 201 Created
Content-Type: application/json
Content-Length: 34
Location: /members/100
{
	"username": "young",
	"age": 20
}
```

- 생성된 리소스는 응답의 `Location` 헤더 필드로 식별합니다.

### 202 Accepted - 요청이 접수되었으나 처리가 완료되지 않을 경우

- 배치 처리 같은 곳에서 사용합니다.
- 예) 요청 접수 후 1시간 뒤에 배치 프로세스가 요청을 처리

### 204 No Content - 서버가 요청을 성공적으로 수행했지만, 응답 페이로드 본문에 보낼 데이터가 없음

- 결과 내용이 없어도 204 메시지(2xx)만으로 성공을 인식할 수 있습니다.
- 예) 웹 문서 편집기에서 save 버튼
    - save 버튼의 결과로 아무 내용이 없어도 됩니다.
    - save 버튼을 눌러도 같은 화면을 유지해야 할 경우.

## 3xx (Redirection) - 리다이렉션

요청을 완료하기 위해 유저 에이전트의 추가 조치가 필요한 경우를 의미합니다.

- 300 Multiple Choices
- 301 Moved Permanently
- 302 Found
- 303 See Other
- 304 Not Modified
- 307 Temporary Redirect
- 308 Permanent Redirect

### 리다이렉션 이해 - 자동 리다이렉트 흐름

웹 브라우저는 3xx 응답의 결과에 Location 헤더가 있으면, Location 위치로 자동 이동합니다(Redirect).

![http://dl.dropbox.com/s/akjo5wzz3bstitw/auto-redirection.png](http://dl.dropbox.com/s/akjo5wzz3bstitw/auto-redirection.png)

### 리다이렉션의 3가지 종류

(1) **영구 리다이렉션** - 특정 리소스의 URI가 영구적으로 이동합니다.

- 예) /members -> /users
- 예) /event -> /new-event

(2) **일시 리다이렉션** - 일시적인 변경

- 주문 완료 후 주문 내역 화면으로 이동
- PRG패턴: Post/Redirect/Get

(3) **특수 리다이렉션**

- 결과 대신 캐시를 사용

### 영구 리다이렉션 - **301, 308**

- 리소스의 URI이 영구적으로 이동합니다.
- 원래의 URL를 사용하지 않으며, 검색 엔진 등에서도 변경 인지할 수 있습니다.
- **301 Moved Permanently**
    - 리다이렉트시 요청 메서드가 GET으로 변하고, 본문이 제거될 수 있습니다(ALMOST).
    - 실무에서는 301을 자주 사용하는데, 대개 리다이렉션된 Form에서 필요한 양식이 기존과 다르기 때문입니다.
- **308 Permanent Redirect**
    - 301과 기능은 같습니다(본문이 제거될 수 있는 부분을 개선하기 위해 308 사용).
    - 리다이렉트시 요청 메서드와 본문 유지합니다(처음 POST를 보내면 리다이렉트도 POST 유지).
- 영구 리다이렉션은 일시 리다이렉션에 비해 많이 사용하지는 않습니다

### 일시적인 리다이렉션 - 302, 307, 303

- 리소스의 URI가 일시적으로 변경합니다.
- 영구적으로 변경된 것이 아니기 때문에(알 수 없음) 검색 엔진 등에서 URL을 변경하여 인식하지 않습니다.

- **302 - Found**
    - 리다이렉트시 요청 메서드가 GET으로 변하고, 본문이 제거될 수 있습니다(ALMOST)
- **307 - Temporary Redirect**
    - 302와 기능은 같습니다
    - 리다이렉트시 요청 메서드와 본문 유지하며, 요청 메서드를 변경하면 안됩니다(MUST).
- **303 - See Other**
    - 302와 기능은 같습니다.
    - 리다이렉트시 요청 메서드가 GET으로 변경됩니다(명확히).

**역사**

- 처음 302 스펙의 의도는 HTTP 메서드를 유지하는 것이었습니다.
- 그런데 웹 브라우저들이 대부분 GET으로 바꾸어버립니다(일부는 다르게 동작).
- 그래서 모호한 302를 대신하는 명확한 307, 303이 등장합니다(301 대응으로 308도 등장).

**무엇을 써야할까?**

- 307, 303을 권장하지만 현실적으로 이미 많은 애플리케이션 라이브러리들이 302를 기본값으로 사용합니다.
- 자동 리다이렉션시에 GET으로 변해도 되면 그냥 302를 사용해도 큰 문제는 없습니다.

### 일시적인 리다이렉션 사용 예시 - PRG(Post/Redirect/Get)

POST로 주문 후에 웹 브라우저를 새로고침하면, 새로고침은 요청을 다시 하기 때문에, 중복 주문 문제가 발생할 수 있습니다.

![http://dl.dropbox.com/s/nwpxj2h2nkjvi1k/before-prg.png](http://dl.dropbox.com/s/nwpxj2h2nkjvi1k/before-prg.png)

따라서 PRG(Post/Redirect/Get)를 적용하여 중복 주문 문제를 개선합니다.

![http://dl.dropbox.com/s/vk54qs9w6h6ijfm/after-prg.png](http://dl.dropbox.com/s/vk54qs9w6h6ijfm/after-prg.png)

POST로 주문 후에 주문 결과 화면을 GET 메서드로 리다이렉트하여 결과 화면을 조회합니다. 그 결과 POST로 주문 후에 새로 고침으로 인한 중복 주문을 방지하고 결과 화면만 GET 요청으로 다시 합니다.

### 특수 리다이렉션

**304 - Not Modified**

- 캐시를 목적으로 사용합니다(많이 사용).
- 클라이언트에게 리소스가 수정되지 않았음을 알려줍니다. 따라서 클라이언트는 로컬PC에 저장된 캐시를 재사용합니다(캐시로 리다이렉트 한다).
- 304 응답은 로컬 캐시를 사용해야 하므로 응답에 메시지 바디를 포함하면 안됩니다.
- 조건부 GET, HEAD 요청시 사용합니다.

## 4xx (Client Error) - 클라이언트 오류

- 클라이언트의 요청에 잘못된 문법등으로 서버가 요청을 수행할 수 없는 경우를 의미합니다.
- 오류의 원인이 클라이언트에 있습니다.
- 클라이언트 에러(4XX)와 서버 에러(5XX)의 가장 큰 차이는 같은 요청이 재성공 가능한지의 여부입니다. 클라이언트 에러는 아무리 요청을 반복해도 요청이 재성공할 수 없습니다. 반면 500에러는 서버 장애가 복구 된다면 클라이언트의 요청이 재성공할 수 있습니다.

주요 상태 코드

- 400 Bad Request
- 401 Unauthorized
- 403 Forbidden
- 404 Not Found

### 400 Bad Request - 클라이언트가 잘못된 요청을 해서 서버가 요청을 처리할 수 없음

- 요청 구문, 메시지 등등 오류
- 클라이언트는 요청 내용을 다시 검토하고, 보내야함
- 예) 요청 파라미터가 잘못되거나, API 스펙이 맞지 않을 때
- 여담) 백엔드 개발자는 철저히 클라이언트의 요청을 검증해야 합니다. 그래야 오류의 원인을 명확히 할 수 있습니다.

### 401 Unauthorized - 클라이언트가 해당 리소스에 대한 인증이 필요함

- 인증(Authentication)이 되지 않음을 의미합니다.
    - 401 상태코드는 Unauthorized 이지만 실질적으로 인증(Authentication)이 되지 않음을 의미합니다(이름이 아쉬움).
    - 인증(Authentication): 본인이 누구인지 확인(로그인)
    - 인가(Authorization): 권한부여 (ADMIN 권한처럼 특정 리소스에 접근할 수 있는 권한,
    인증이 있어야 인가가 있음)
- 401 오류 발생시 응답에 `WWW-Authenticate` 헤더와 함께 인증 방법을 설명해야 합니다.

### 403 Forbidden - 서버가 요청을 이해했지만 승인을 거부함

- 주로 인증 자격 증명은 있지만, 접근 권한이 불충분한 경우(Unauthorized)
- 예) 어드민 등급이 아닌 사용자가 로그인은 했지만, 어드민 등급의 리소스에 접근하는 경우

### 404 Not Found - 요청 리소스를 찾을 수 없음

- 요청 리소스가 서버에 없음
- 클라이언트가 권한이 부족한 리소스에 접근할 때 해당 리소스를 숨기고 싶을 때도 사용합니다.

## 5xx (Server Error) - 서버 오류

- 서버 문제로 오류 발생
- 서버에 문제가 있기 때문에 재시도 하면 성공할 수도 있습니다(복구가 되거나 등등).

### 500 (Internal Server Error) - 서버 문제로 오류 발생

- 서버 내부 문제로 오류 발생
- 애매하면 500 오류를 사용합니다.
    - 예) NullPointException, SQLException
    - 여담) 왠만해서는 500에러를 발생해서는 안 됩니다. 진짜로 서버에 문제가 생겼을 때 발생되어야 합니다.
        - 예) 고객의 잔고가 부족한 경우는 비즈니스 로직의 예외이므로 500이 아닌 400오류입니다.

### 503 (Service Unavailable) - 서비스 이용 불가

- 서버의 일시적인 과부하 또는 예정된 작업으로 잠시 요청을 처리할 수 없음을 의미합니다.
- `Retry-After` 헤더 필드로 얼마뒤에 복구가 되는지 보낼 수도 있습니다.
- 여담) 503보다는 500을 보는 경우가 많다.

---

**참조**

<김영한, 모든 개발자를 위한 HTTP 웹 기본 지식, 인프런>

https://developer.mozilla.org/en-US/docs/Web/HTTP/Status
