# JWT with Java

이번 페이지 에서는 JWT에 대해 학습하고 이를 Java 언어로 표현해 봅니다.

JWT(JSON Web Token)는 작고(compact) 자족적인(self-contained) 방법으로 당사자 간에 안전하게 정보를 주고받는 JSON 객체를 정의한 공개 표준([RFC 7519](https://www.rfc-editor.org/rfc/rfc7519))입니다. 이 정보는 디지털로 서명되었기 때문에 검증되고 신뢰할 수 있습니다. JWT는 ECDSA 알고리즘을 사용하는 비밀키(a secret) 또는 RSA 또는 ECDSA를 사용하는 공개/개인키 쌍을 사용하여 서명됩니다.

> Cf. RFC : An Internet Standard is documented by a **Request for Comments** (RFC) or a set of RFCs
> 

## **Json Web Token의 구조**

JSON Web Token은 점(.)으로 구분되는 3개의 부분으로 구성됩니다.

- Header
- Payload
- Signature

따라서 일반적으로 JWT는 다음의 형태를 띕니다.

```bash
xxxxx.yyyyy.zzzzz
```

### Header

헤더는 일반적으로 두 부분으로 구성됩니다. 하나는 JWT의 토큰 유형이고 다른 하나는 HMAC SHA256 또는 RSA와 같은 서명에 사용될 알고리즘입니다.

```json
{
    "alg" : "HS256" ,
    "typ" : "JWT"
}
```

그런 다음 이 JSON은 **Base64Url**로 인코딩되어 JWT의 첫 번째 부분을 구성합니다.

### Payload

토큰의 두 번째 부분은 여러 클레임들**(**claims)을 가지는 페이로드(Payload)입니다. **클레임들**(claims)은 엔티티(일반적으로 사용자)와 추가 데이터에 대한 성명들(statements)입니다. 클레임은 세 가지 유형이 있습니다: 등록된(registered) 클레임, 공개(public) 클레임, 비공개(private) 클레임.

- 등록된(registered) 클레임: 미리 정의된 클레임 세트입니다. 필수는 아니지만 유용하고, 상호 운용 가능한(interoperable) 클레임 세트를 제공하기 위해 권장됩니다. 등록된 클레임으로는 iss (발행자), exp (만료 시간), sub (주제), aud (대상자(audience)) 등이 있습니다. 다음 [링크](https://datatracker.ietf.org/doc/html/rfc7519#section-4.1)를 참조하시면 됩니다.
- 공개(public) 클레임: JWT를 사용하는 사람들이 자유롭게 정의 할 수 있습니다. 그러나 충돌을 피하기 위해 [IANA JSON 웹 토큰 레지스트리](https://www.iana.org/assignments/jwt/jwt.xhtml)에 정의되거나 충돌 방지 네임 스페이스가 포함 된 URI로 정의되어야 합니다.
- 비공개 소유권(private) 클레임: 비공개 사용자 지정 클레임은 (등록된 클레임이나 공개 클레임이 아니라) 이 클레임을 사용하기로 동의한 당사자들 간에 정보 공유를 위해 만들어졌습니다.

페이로드(Payload)는 아래의 예시처럼 될 수 있습니다.

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```

그 뒤, 페이로드는 Base64Url로 인코딩되어 JSON 웹 토큰의 두 번째 부분을 구성합니다.

> 주의 : 서명된 토큰의 경우 이 정보는 변조되지 않도록 보호되지만 모든 사람이 읽을 수 있음을 유의해야 한다. 암호화되지 않은 JWT의 페이로드 또는 헤더 요소에 secret(암호키)를 넣지 말아야 합니다.
> 

### Signature

서명 부분을 만들려면 `인코딩된 헤더`, `인코딩된 페이로드`, `암호키(secret)`, `헤더에 지정된 알고리즘`이 필요합니다. 예를 들어 HMAC SHA256 알고리즘을 사용하려면 다음과 같은 방법으로 서명을 만듭니다.

```json
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

서명은 이 과정에서 메시지가 변경되지 않았음을 확인하는 데 사용되며, 개인키로 서명된 토큰의 경우, JWT 발신자가 누구인지 확인할 수도 있습니다.

The signature is used to verify the message wasn't changed along the way, and, in the case of tokens signed with a private key, it can also verify that the sender of the JWT is who it says it is.

### 구성 요소(**Header, Payload, Signature**)의 조합

결과물은 점으로 구분 된 세 개의 Base64-URL 문자열이며 HTML 및 HTTP 환경에서 쉽게 전달할 수 있습니다. 이는 SAML과 같은 XML 기반 표준과 비교할 때보다 컴팩트합니다. 다음은 JWT가 헤더, 페이로드가 인코드되어 있으며 암호키로 서명되어 있는 것을 보여줍니다.

![https://cdn.auth0.com/content/jwt/encoded-jwt3.png](https://cdn.auth0.com/content/jwt/encoded-jwt3.png)

만약 JWT를 활용하고 싶거나 위의 개념들을 연습하고 싶다면, [jwt.io Debugger](https://jwt.io/#debugger-io)를 이용하여 JWT들을 디코드, 검증, 생성할 수 있습니다.

### 코드로 표현하기

그러면 지금까지의 과정을 코드(Java)로 표현해 보겠습니다. 먼저 [JWT를 지원하는 의존성](https://github.com/jwtk/jjwt)을 추가합니다.

`build.gradle`

```groovy
implementation 'io.jsonwebtoken:jjwt-api:0.11.2'
runtimeOnly 'io.jsonwebtoken:jjwt-impl:0.11.2'
runtimeOnly 'io.jsonwebtoken:jjwt-jackson:0.11.2'
```

추가한 라이브러리를 활용하여 JWT을 발행합니다.

```java
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.security.Keys;

// 메소드
public String makeSignature(){
	String secret = "1234567890123456789012";
	Key key = Keys.hmacShaKeyFor(secret.getBytes());
	//hmacShaKeyFor메소드는 HMAC-SHA256 algorithms과 바이트로 된 암호(a secret)를 가지고 비밀키를 만든다.

	return Jwts.builder()
			.claim("userId", 1L)
			.setIssuedAt(new Date()) // 토큰 발행 일시
			.setExpiration(new Date(System.currentTimeMillis() + (60 * 1000) * 100)) // 토큰 유효 일시(+100분)
			.signWith(key)
			.compact();
}
```

## JSON Web Token의 작동

인증(authentication)에서 사용자가 자격 증명(credential)을 통해 성공적으로 로그인을 하면, JWT가 반환됩니다. 토큰이 자격 증명으로 사용될 때부터는 보안 이슈가 발생하지 않도록 매우 큰 주의가 따라야 합니다. 일반적으로 토큰은 필요한 것보다 오래 유지되어서는 안 됩니다. 또한 [민감한 세션 정보는 보안에 취약하기 때문에 브라우저 저장소](https://cheatsheetseries.owasp.org/cheatsheets/HTML5_Security_Cheat_Sheet.html#local-storage)에 보관해서는 안 됩니다.

사용자 에이전트는 사용자가 보호된 경로나 리소스에 접근하려 할 때 마다 (일반적으로) 인가(Authorization) 헤더 안에 Bearer 스키마를 사용하여 JWT를 보내야 합니다.

헤더의 내용은 아래와 같은 모양이어야 합니다.

```
Authorization: Bearer <token>
```

이것은 어떤 경우에는 무상태 인가(a stateless authorization) 메커니즘이 될 수 있습니다. 서버의 보호된 경로(라우터)들은 인가 헤더 안에 있는 JWT가 유효한지 체크할 것입니다. 그리고 만약 유효하다면, 사용자는 보호된 자원에 접근하는 것이 허가될 것입니다. 만약 JWT가 필요한 정보를 가지고 있는 경우에는, 어떤 로직 수행을 위해 데이터베이스에 쿼리를 수행하는 일을 감소시켜 줄 것입니다. 물론 이것이 모든 경우에 해당하지는 않습니다.

만약 토큰이 인가(Authorization) 헤더 안에 보내진다면, CORS(Cross-Origin Resource Sharing)는 이슈되지 않을 것입니다. 이것은 쿠키들을 사용하지 않기 때문입니다.

아래의 다이어그램은 어떻게 JWT가 얻어지고 다시 이것이 어떻게 API들 또는 리소스들에 접근하는데 사용되어지는 지를 보여줍니다.

![https://cdn2.auth0.com/docs/media/articles/api-auth/client-credentials-grant.png](https://cdn2.auth0.com/docs/media/articles/api-auth/client-credentials-grant.png)

1. 앱 또는 클라이언트는 인가(Authorization) 서버에 권한 부여(Authorization)를 요청합니다. 이것은 여러 인가 흐름 중에 한 방법으로 진행됩니다. 예를 들어, 일반적인 Open ID Connect 호환 웹 응용 프로그램은 인증 코드 흐름을 사용하여 /oauth/authorize의 엔드포인트를  통과합니다.
2. 권한 부여가 완료되면 인가 서버는 접근 토큰(access token)을 요청한 어플리케이션에 반환합니다.
3. 어플리케이션은 보호된 리소스에 접근하기 위해 받은 접근 토큰(access token)을 사용합니다.

> 주의. 서명된 토큰들 모두, 토큰 안에 모든 정보들이 들어갑니다. 이것을 변조할 수 없더라도 사용자나 다른 그룹에 노출되기 때문에 비밀 정보를 토큰 안에 넣어서는 안 됩니다.
> 

### 코드로 표현하기

**Client**

예를 들어 상품 등록은 인증된 사용자만 등록할 수 있다고 가정하겠습니다. 클라이언트는 위의 예시에서 생성한 JWT을 `Authorization` 헤더의 `Bearer` 스키마에 추가하여 요청합니다.

아래는 클라이언트(HTTPie)를 이용하여 서버에 상품 등록을 요청하는 예시입니다.

```bash
~ $ http POST localhost:8080/products name="쥐돌이" maker="코드숨" price=1000 "Authorization: BEARER eyJhbGciOiJIUzI1NiJ9.eyJ1c2VySWQiOjF9.neCsyNLzy3lQ4o2yliotWT06FwSGZagaHpKdAkjnGGw"
```

**Server**

아래는 위의 HTTP 요청을 처리하는 스프링 컨트롤러 입니다. Request Header에서 토큰을 얻어 JWT를 파싱합니다.

```java
import io.jsonwebtoken.Claims;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.security.Keys;

import java.security.Key;

@RestController
@RequestMapping("/products")
public class JwtHandler{
		private final JwtHandler jwtHandler;
		private final Key key;

    //secret은 application.yml에 보관
		public JwtHandler(@Value("${jwt.secret}") String secret) {
        key = Keys.hmacShaKeyFor(secret.getBytes());
		}

		@PostMapping
		@ResponseStatus(HttpStatus.CREATED)
		public ResponseEntity create(
		        @RequestHeader("Authorization") String authorization,
		        @RequestBody @Valid ProductData productData
		) {
		    String accessToken = authorization.substring("Bearer ".length());

		try {
            Claims claims = Jwts.parserBuilder()
                    .setSigningKey(signKey)
                    .build()
                    .parseClaimsJws(accessToken)
                    .getBody();

        } catch (SignatureException | MalformedJwtException e) {
            throw new AuthenticationException(ErrorCode.INVALID_TOKEN);
        } catch (ExpiredJwtException e) {
            throw new AuthenticationException(ErrorCode.EXPIRED_TOKEN);
        }

				...
		}
}
```

---

**참조**

JWT : [https://jwt.io/introduction](https://jwt.io/introduction)

Java JWT : [https://github.com/jwtk/jjwt](https://github.com/jwtk/jjwt)
