# Resource modeling

## Contents

- 리소스
    - URI
    - 리소스는 싱글톤 또는 컬렉션이 될 수 있다.
    - 리소스는 서브 컬렉션 리소스들을 포함할 수도 있다.
- 리소스 모델링
- 리소스 원형
    - 다큐먼트
    - 컬렉션
    - 스토어
    - 컨트롤러
- API 설계하기
    - 리소스 식별
    - 리소스와 행위를 분리

## 리소스

[REST](https://restfulapi.net/)에서는 주요 데이터에 대한 표현을 리소스라고 부른다.

### URI

REST API**는** 리소스의 위치(주소)를 나타내기 위해 **URI(Uniform Resource Identifier)**를 사용한다. 오늘날의 웹에서 URI는 `http://api.example.restapi.org/france/paris/louvre/leonardo-davinci/mona-lisa`처럼 분명하게 명작들을 가리키는 API 리소스 모델부터 사람이 이해하기 힘든 `http://api.example.restapi.org/67dd0-a9d3-11e0-9flc-0800200c9a66`과 같은 것에 이르기까지 다양하게 사용된다.

### 리소스는 싱글톤 또는 컬렉션이 될 수 있다.

예를 들어, 은행 도메인에서 `customers`는 컬렉션 리소스이고, `customer`는 싱글톤 리소스이다. 우리는 `/customers` 라는 URI를 사용해서 `customers` 컬렉션을 식별할 수 있고 `/customers/{customerId}` URI를 사용해서 단일 `customer` 리소스를 식별할 수도 있다.

### 리소스는 서브 컬렉션 리소스들을 포함할 수도 있다.

예를 들어, 특정 `customer`의 서브 컬렉션 리소스인 `accounts`는 `/customers/{customerId}/accounts` 라는 URN을 사용해 식별할 수 있다. 비슷하게 서브 컬렉션 리소스인 `accounts` 안에 있는 싱글톤 리소스 `account` 도 다음과 같이 식별될 수 있다(`/customers/{customerId}/accounts/{accountId}`).

## 리소스 모델링

URI 경로는 REST API의 리소스 모델을 다루는데, 포워드 슬래시(`/`)로 경로 구문을 나눈다. 이 **경로 구문은 리소스 모델 계층에서 유일한 리소스에 해당된다.** 다음 예를 보자.

`http://api.soccer.restapi.org/leagues/seattle/teams/trebuchet`

이 URI 디자인은 다음과 같은 자체 리소스 주소를 가진 URI가 있다는 것을 뜻한다.

`http://api.soccer.restapi.org/leagues/seattle/teams`

`http://api.soccer.restapi.org/leagues/seattle`

`http://api.soccer.restapi.org/leagues`

`http://api.soccer.restapi.org`

리소스 모델링은 API의 주요 개념을 확실하게 잡는 훈련과도 같다. 이 과정은 마치 관계형 데이터베이스 스키마를 정의하기 위한 데이터 모델링이나, 객체 지향 시스템에서의 클래스 모델링과 유사하다. URI 경로 설계에 들어가기 전, REST API의 **리소스 모델**에 대해 생각해 보는 것이 도움이 될 것이다.

## 리소스 원형(resource archetypes)

API 리소스를 모델링할 때, 기본적인 리소스 원형(document, collection, store, controller)을 가지고 시작할 수 있다. 설계자는 리소스 원형을 이용하여 디자인 패턴에서와 같이 **REST API 디자인에서 보편적으로 쓰이는 리소스의 구조와 행동을 일관된 방식으로 다룰 수 있다.**

> 리소스 모델로 명확하고 정확하게 클라이언트와 통신하려면, REST API는 리소스 원형 중에서 하나로만 설계해야 한다. 일관성을 위해 리소스 원형 하나 이상을 이용하여 리소스를 설계하지 않도록 주의를 기울여야 한다. 대신, 링크를 통해 계층적으로 연관될 수 있는 분리된 리소스 설계를 고민해야 한다.
> 

### 도큐먼트(Document)

도큐먼트 리소스는 객체 인스턴스나 데이터베이스 레코드와 유사한 단일 개념이다. 일반적으로 도큐먼트의 상태 표현은 값을 가진 필드와 다른 관련 리소스와의 링크 둘 다를 가지게 된다. 기본적인 필드와 링크 기반 구조로 인해, 도큐먼트 타입은 다른 리소스 원형들의 기반 원형이 된다. 즉, 서로 다른 리소스 원형 세 개는 도큐먼트 원형에서 분리된 것이라 볼 수 있다. 도큐먼트 리소스를 사용할 때는 "단수"를 사용해야 한다.

다음 URI는 각각 도큐먼트 리소스를 나타낸다.

```
http://api.soccer.restapi.org/leagues/seattle
http://api.soccer.restapi.org/leagues/seattle/teams/trebuchet
http://api.soccer.restapi.org/leagues/seattle/teams/trebuchet/players/mike
```

### 컬렉션(Collection)

컬렉션 리소스는 서버에서 관리하는 **디렉토리 리소스**이다. 서버가 리소스의 URI를 생성하고 관리한다. 클라이언트는 등록될 리소스의 URI를 모른다. 컬렉션은 POST 메서드를 기반으로 리소스를 등록한다(이 말은 아래에 스토어와 비교하면 이해할 수 있을 것이다).

회원 등록 API를 예로 들어보자.

- **회원** 목록 /members **> GET**
- **회원** 등록 /members **> POST**
- **회원** 조회 /members/{id} **> GET**
- **회원** 수정 /members/{id} **> PATCH, PUT, POST**
- **회원** 삭제 /members/{id} **> DELETE**

클라이언트는 아래와 같이 회원 등록을 요청(request)한다. 이 때, 클라이언트는 등록될 리소스의 URI를 모른다.

```
**POST /members**
```

서버가 새로 등록된 리소스 URI를 생성해준다(response).

```
HTTP/1.1 201 Created
Location: **/members/100**
```

컬렉션 리소스를 사용할 때는 "복수"를 사용해야 한다.

```
http://api.example.com/device-management/managed-devices
http://api.example.com/user-management/users
http://api.example.com/user-management/users/{id}/accounts
```

### 스토어(store)

스토어는 클라이언트에서 관리하는 리소스 저장소다. 스토어 리소스는 API 클라이언트가 리소스를 넣거나 빼는 것, 지우는 것에 관여한다. 또한 클라이언트가 리소스의 URI를 알고 관리한다(클라이언트가 직접 리소스의 URI를 지정한다). 스토어 스스로 새로운 리소스를 생성하지는 못하기 때문에, 새로운 URI를 만들지는 못한다. 대신 리소스는 스토어에 처음 저장될 때, 클라이언트가 선택한 URI를 가진다. 스토어 리소스를 나타낼 때는 복수 명사를 사용한다.

예를 들어 파일 관리 시스템 API는 컬렉션(Collection)에서의 회원 등록 API와 다르게 POST가 아닌 **PUT을 기반으로 리소스를 등록**한다. PUT은 리소스의 현재 상태를 주어진 표현으로 대치하는 메소드이다. 파일 시스템에서 파일을 등록할 때, 등록할 URI에 파일이 이미 존재한다면 기존 파일을 먼저 지우고 등록을 진행해야 한다. 따라서 PUT을 기반으로 리소스를 등록한다.

파일 관리 시스템 API 설계(PUT 기반 등록)

- **파일** 목록 조회 /files **> GET**
- **파일** 조회 /files/{filename} **> GET**
- **파일** 등록 /files/{filename} **> PUT**
- **파일** 삭제 /files/{filename} **> DELETE**
- **파일** 대량 등록 /files **> POST**

스토어는 `/files`이다. PUT으로 파일을 등록하고 있기 때문에 POST는 임의로 지정할 수 있다. 여기서는 파일 대량 등록을 위해 사용하고 있다. 다른 경로를 이용해도 괜찮다.

### 컨트롤러(controller)

컨트롤러 리소스는 절차적인 개념을 모델화한 것이다. 컨트롤러 리소스는 실행 가능한 함수와 같아서 파라미터(입력 값)와 반환 값(출력 값)이 있다.

REST API는 CRUD라고 알려진 표준적인 메서드와는 논리적으로 매핑되지 않는 애플리케이션 고유의 행동(HTTP 메서드로 해결하기 애매한 경우)을 컨트롤러 리소스의 도움을 받아 수행한다.

일반적으로 컨트롤러 이름은 URI 경로의 제일 마지막 부분에 표시되며, 계층적으로 뒤따르는 자식 리소스는 없다. 컨트롤러 리소스를 사용할 때는 "동사"를 사용해야 한다.

다음 예는 클라이언트가 사용자에게 경고를 재전송하는 컨트롤러 리소스다.

```
POST /alserts/245743/resend
```

다른 예로 만약 HTML FORM(GET, POST 메소드만 지원)만 사용한다고 가정해 보자. 이 경우 컨트롤러를 이용하여 생성(`/new`), 수정(`/edit`), 삭제(`/delete`)를 구분해 볼 수 있을 것이다.

## API 설계하기

### 리소스 식별

REST API를 설계하는 첫 단계는 **리소스를 식별**하는 것이다.

다음의 회원(member) 정보 관리 API를 만든다고 가정해 보자.

- 회원 목록 조회
- 회원 조회
- 회원 등록
- 회원 수정
- 회원 삭제

먼저 리소스를 식별해 보자. 리소스는 회원을 등록하고 수정하고 조회하는 것이 아니다. **회원**이라는 개념 자체가 바로 리소스다. REST URI는 동사 대신 명사를 사용해 리소스를 나타내야 한다. 이는 동사엔 없는 명사의 성질 때문인데, 이 성질은 리소스의 성질과도 유사하다.

다음과 같이 회원을 URI에 매핑해 볼 수 있다.

```
http://api.example.com/members
http://api.example.com/members/{id}
```

### 리소스와 행위를 분리

리소스를 식별한 다음 단계로 리소스와 행위를 분리한다. URI는 리소스만 식별한다. URI만으로는 회원 리소스를 어떻게(등록, 수정, 삭제) 처리할 것인지 구분할 수 없다. 이제 리소스를 어떻게 처리하는지 행위를 구분한다. 일반 적인 행위는 [HTTP request methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)를 이용하여 구분할 수 있다.

다음과 같이 members 리소스에 HTTP request method들을 적용해 볼 수 있다.

- **회원** 목록 /members **> GET**
- **회원** 등록 /members **> POST**
- **회원** 조회 /members/{id} **> GET**
- **회원** 수정 /members/{id} **> PATCH, PUT, POST**
- **회원** 삭제 /members/{id} **> DELETE**

## 참조

[https://restfulapi.net/resource-naming/](https://restfulapi.net/resource-naming/)

<마크 마세, REST API 디자인 규칙, 한빛미디어>

<김영한, (강의) 모든 개발자를 위한 HTTP 웹 기본 지식, 인프런>
