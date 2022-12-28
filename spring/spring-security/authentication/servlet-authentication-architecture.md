**참조**

[https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html](https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html)

---

# Servlet Authentication Architecture

이번 페이지는 [Servlet Security: The Big Picture](https://github.com/eastshine-high/til/blob/main/spring/spring-security/architecture.md) 을 확장하여 Servlet 인증(Authentication)에 사용되는 Spring Security의 주요 아키텍처 구성요소를 살펴보겠습니다. 이 구성 요소들이 어떻게 협동하는지 구체적인 흐름 설명이 필요한 경우에는 [Authentication Mechanism](https://docs.spring.io/spring-security/reference/servlet/authentication/index.html#servlet-authentication-mechanisms) 을 참조하실 수 있습니다.

- `SecurityContextHolder` - Spring Security가 인증된 사람의 세부정보를 저장하는 곳입니다.
- `SecurityContext` - 는 `SecurityContextHolder`에서 가져올 수 있습니다. 현재 인증된 사용자의 `Authentication` 을 가집니다.
- `Authentication` - 사용자 인증 정보(credentials, 사용자가 인증을 위해 제공)를 제공하기 위해 `AuthenticationManager` 의 입력이 될 수 있습니다. 또는 `SecurityContext`의 현재 사용자입니다.
- `GrantedAuthority` - `Authentication`의 주체(principal)에게 부여되는 권한(authority)(예: 역할, 범위 등)
- `AuthenticationManager` - Spring Security의 필터가 어떻게 인증([authentication](https://docs.spring.io/spring-security/reference/features/authentication/index.html#authentication))을 수행하는지를 정의한 API입니다.
- `ProviderManager` - `AuthenticationManager`의 가장 일반적인 구현입니다.
- `AuthenticationProvider` - `ProviderManager` 에서 특정 유형의 인증(authentication)을 수행하는 데 사용됩니다.
- [AuthenticationEntryPoint를 이용한 사용자 증명 정보(Credentials) 요청](https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html#servlet-authentication-authenticationentrypoint) - 클라이언트에서 사용자 증명 정보(credentials)를 요청하는 데 사용됩니다(예: 로그인 페이지로 리다이렉션, `WWW-Authenticate` 응답 전송 등).
- `AbstractAuthenticationProcessingFilter` - 인증에 사용되는 기본 `Filter` 입니다. 또한 인증의 큰 흐름과 인증과 관련된 객체들의 협력에 대한 좋은 아이디어를 제공합니다.

## SecurityContextHolder

Spring Security 인증 모델의 핵심은 `SecurityContextHolder` 입니다. `SecurityContextHolder` 는 SecurityContext 를 포함합니다.

![https://docs.spring.io/spring-security/reference/_images/servlet/authentication/architecture/securitycontextholder.png](https://docs.spring.io/spring-security/reference/_images/servlet/authentication/architecture/securitycontextholder.png)

Spring Security는 인증된 사람의 세부 사항을 `SecurityContextHolder`에 저장하는 곳입니다. Spring Security는 `SecurityContextHolder` 가 어떻게 채워지는지는 관심 갖지 않습니다. 하지만 `SecurityContextHolder` 에 값이 있으면, 현재 인증된 사용자로 사용됩니다.

인증된 사용자인지를 나타내는 가장 간단한 방법은 `SecurityContextHolder`에 직접 설정(set)하는 것입니다.

**예제 1. `SecurityContextHolder` 설정**

```java
SecurityContext context = SecurityContextHolder.createEmptyContext(); // 1
Authentication authentication =
    new TestingAuthenticationToken("username", "password", "ROLE_USER"); // 2
context.setAuthentication(authentication);

SecurityContextHolder.setContext(context); // 3
```

1. 비어있는 `SecurityContext` 를 만드는 것에서 시작합니다. 여러(multiple) 스레드에서 경합 상태(race conditions)를 방지하려면 `SecurityContextHolder.getContext().setAuthentication(authentication)` 을 사용하는 대신 새 `SecurityContext` 인스턴스를 만드는 것이 중요합니다.
- 다음으로 새로운 `Authentication` 객체를 만듭니다. Spring Security는 `SecurityContext` 에 어떤 타입의 `Authentication` 구현이 설정되어 있는지 상관하지 않습니다. 여기서는 매우 간단한 `TestingAuthenticationToken` 을 사용합니다. 보다 일반적인 프로덕션 시나리오는 `UsernamePasswordAuthenticationToken(userDetails, password, authorities)` 입니다.
- 마지막으로 `SecurityContextHolder` 에 `SecurityContext` 를 설정(set)합니다. Spring Security는 인가([authorization](https://docs.spring.io/spring-security/reference/servlet/authorization/index.html#servlet-authorization))를 위해 이 정보를 사용합니다.

인증된 주 구성원(principal)에 대한 정보를 얻고싶은 경우, `SecurityContextHolder` 에서 접근할 수 있습니다.

**예제 2. 현재 인증된 사용자에 접근하기**

```java
SecurityContext context = SecurityContextHolder.getContext();
Authentication authentication = context.getAuthentication();
String username = authentication.getName();
Object principal = authentication.getPrincipal();
Collection<? extends GrantedAuthority> authorities = authentication.getAuthorities();
```

기본적(default)으로 `SecurityContextHolder` 는 세부 정보를 저장하기 위해 `ThreadLocal` 를 사용합니다. 즉, `SecurityContext` 가 메서드의 아규먼트로 명시적으로 전달되지 않더라도 동일한 스레드의 메서드들에서 항상 `SecurityContext` 를 사용할 수 있습니다. 이러한 방식으로 `ThreadLocal` 을 사용하는 것은 현재 보안 주체(principal)의 요청이 처리된 후 스레드를 지우도록 주의를 기울이면 상당히 안전합니다. Spring Security의 [FilterChainProxy](https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-filterchainproxy) 는 `SecurityContext` 가 항상 지워지도록 합니다.

## SecurityContext

[SecurityContext](https://docs.spring.io/spring-security/site/docs/5.7.1/api/org/springframework/security/core/context/SecurityContext.html) 는 `SecurityContextHolder` 에서 얻을 수 있습니다. `SecurityContext`는 `Authentication` 객체를 가집니다.

## Authentication

[Authentication](https://docs.spring.io/spring-security/site/docs/5.7.1/api/org/springframework/security/core/Authentication.html) 은 Spring Security 내에서 두 가지 주요 용도로 사용됩니다.

- 사용자가 인증을 위해 제공한 credentials(증명 정보)를 `AuthenticationManager` 의 입력으로 제공합니다.
- 현재 인증된 사용자를 나타냅니다. `SecurityContext` 에서 현재 `Authentication`을 가져올 수 있습니다.

`Authentication` 은 다음을 포함(contain)합니다.

- `principal` - 사용자를 식별합니다. username/password로 인증할 때, 종종 [UserDetails](https://docs.spring.io/spring-security/reference/servlet/authentication/passwords/user-details.html#servlet-authentication-userdetails) 의 인스턴스입니다.
- `credentials` - 보통 암호(password)입니다. 대부분의 경우 유출되지 않도록 사용자가 인증된 후에 지워집니다.
- `authorities` - `GrantedAuthority` 인스턴스들은 사용자에게 부여된 높은-수준(high-level)의 권한(permission)들입니다. 두 가지 예시로 역할(role)들 또는 범위(scope)들이 있습니다.

## GrantedAuthority

[GrantedAuthority](https://docs.spring.io/spring-security/site/docs/5.7.1/api/org/springframework/security/core/GrantedAuthority.html) 들은 사용자에게 부여된 높은-수준(high-level)의 권한(permission)들입니다. 두 가지 예시로 역할(role)들 또는 범위(scope)들이 있습니다.

`GrantedAuthority` 는 `Authentication.getAuthorities()` 메소드에서 얻을 수 있습니다. 이 메소드는 `GrantedAuthority` 객체의 컬렉션을 제공합니다. `GrantedAuthority` 는 당연하게도 주체(principal)에게 부여된 권한입니다. 이 권한은 일반적으로 `ROLE_ADMINISTRATOR` 또는 `ROLE_HR_SUPERVISOR` 와 같은 역할들입니다. 이러한 역할들은 나중에 웹 인가(authorization), 메서드 인가 및 도메인 객체 인가에 대해 구성(configure)됩니다. Spring Security의 다른 부분(parts)은 이러한 권한들을 해석하거나 있을 것으로 예상하는 것입니다. username/password 기반 인증을 사용하는 경우 `GrantedAuthority` 는 일반적으로 [UserDetailsService](https://docs.spring.io/spring-security/reference/servlet/authentication/passwords/user-details-service.html#servlet-authentication-userdetailsservice) 에 의해 로드됩니다.

일반적으로 `GrantedAuthority` 객체들은 애플리케이션 전체 권한들입니다. 주어진 도메인 객체에 한정되지 않습니다. 따라서 `Employee` 객체 번호 54에 대한 권한을 나타내는 `GrantedAuthority` 는 없을 가능성이 높습니다. 만약 그러한 권한이 수천 개 있는 경우, 메모리가 빨리 부족해지기 때문입니다(또는 최소한 애플리케이션이 사용자를 인증할 때 오랜 시간이 걸리기 때문입니다). 물론 Spring Security는 명시적으로 이러한 일반적인 요구 사항을 처리하도록 설계되었지만, 이 목적을 위해 프로젝트 도메인 객체의 보안 기능들을 대신 사용합니다.

## AuthenticationManager

**[AuthenticationManager](https://docs.spring.io/spring-security/site/docs/5.7.1/api/org/springframework/security/authentication/AuthenticationManager.html) 는 Spring Security의 필터들이 어떻게 인증([authentication](https://docs.spring.io/spring-security/reference/features/authentication/index.html#authentication))을 수행하는지를 정의하는 API입니다**. 반환된 `Authentication` 은 `AuthenticationManager` 를 호출한 컨트롤러(예: [Spring Security’s](https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-security-filters) `Filters`)에 의해 `SecurityContextHolder`에 설정(set)됩니다. 만약 Spring Security의 `Filter` 들과 통합하여 사용하지 않는 경우에는 `AuthenticationManager`를 사용할 필요는 없이, `SecurityContextHolder` 에 직접 설정(set)할 수도 있습니다.

무엇이든 `AuthenticationManager`의 구현체가 될 수 있지만, 가장 일반적인 구현체는 `ProviderManager` 입니다.

## ProviderManager

**`ProviderManager` 는 가장 일반적으로 사용되는 `AuthenticationManager` 의 구현**입니다. `ProviderManager` 는 `AuthenticationProvider` 인스턴스들의 `List` 에 위임합니다. 각 `AuthenticationProvider` 에는 인증이 성공 또는 실패해야 함을 나타내거나 결정을 내릴 수 없음을 나타내고 다운스트림 `AuthenticationProvider` 이 결정하도록 허용할 수 있습니다. 구성된 `AuthenticationProvider` 인스턴스 중 어느 것도 인증할 수 없는 경우 `ProviderNotFoundException` 과 함께 인증에 실패합니다. 이는 `ProviderManager`가 전달된 인증 유형을 지원하도록 구성되지 않았음을 나타내는 특수한 `AuthenticationException` 입니다.

![https://docs.spring.io/spring-security/reference/_images/servlet/authentication/architecture/providermanager.png](https://docs.spring.io/spring-security/reference/_images/servlet/authentication/architecture/providermanager.png)

## AuthenticationProvider

여러 개의 `AuthenticationProvider` 인스턴스를 `ProviderManager`에 삽입할 수 있습니다. 각 `AuthenticationProvider`는 특정 유형의 인증을 수행합니다. 예를 들어 [DaoAuthenticationProvider](https://docs.spring.io/spring-security/reference/servlet/authentication/passwords/dao-authentication-provider.html#servlet-authentication-daoauthenticationprovider) 는 사용자 이름/비밀번호 기반 인증을 지원하고 `JwtAuthenticationProvider` 는 JWT 토큰 인증을 지원합니다.

## Request Credentials with `AuthenticationEntryPoint`

[AuthenticationEntryPoint](https://docs.spring.io/spring-security/site/docs/6.0.0/api/org/springframework/security/web/AuthenticationEntryPoint.html) 는 클라이언트에 증명 정보(credentials)를 요청하는 HTTP 응답을 보내는 데 사용됩니다.

경우에 따라 클라이언트는 리소스를 요청하기 위해 자격 증명(사용자 이름과 암호와 같은)을 사전에 포함합니다. 이러한 경우에는 자격 증명이 이미 포함되어 있으므로 Spring Security는 클라이언트에 credentials를 요청하는 HTTP 응답을 보낼 필요가 없습니다.

다른 경우에는 클라이언트가 접근 권한이 없는 리소스에 대해 인증되지 않은 요청을 합니다. 이 경우에는 클라이언트에 자격 증명(credentials)을 요청하는 데 `AuthenticationEntryPoint` 구현이 사용됩니다. `AuthenticationEntryPoint` 구현은 [로그인 페이지로 리디렉션](https://docs.spring.io/spring-security/reference/servlet/authentication/passwords/form.html#servlet-authentication-form)하거나 [WWW-Authenticate](https://docs.spring.io/spring-security/reference/servlet/authentication/passwords/basic.html#servlet-authentication-basic) 헤더로 응답하거나 다른 작업을 수행할 수 있습니다.
