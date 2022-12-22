# Architecture

이번 페이지에서는 서블릿 기반 애플리케이션 안에 있는 Spring Security의 아키텍처에 대해 알아봅니다. Spring Security의 [Authentication(인증)](https://docs.spring.io/spring-security/reference/servlet/authentication/index.html#servlet-authentication), [Authorization(인가)](https://docs.spring.io/spring-security/reference/servlet/authorization/index.html#servlet-authorization), [Protection Against Exploits(악용에 대한 보호)](https://docs.spring.io/spring-security/reference/servlet/exploits/index.html#servlet-exploits)는 이 아키텍처의 이해를 기반으로 합니다.

## A Review of `Filter`s

Web tier(UI 및 HTTP 백엔드용)에 있는 **Spring Security는 서블릿 필터를 기반으로 합니다**. 따라서 일반적인 필터의 역할을 먼저 살펴보는 것이 도움이 됩니다. 다음 그림은 단일 HTTP 요청 처리를 위한 핸들러들의 일반적인 계층화(layering)를 보여줍니다.

![https://github.com/spring-guides/top-spring-security-architecture/raw/main/images/filters.png](https://github.com/spring-guides/top-spring-security-architecture/raw/main/images/filters.png)

클라이언트는 애플리케이션에 요청을 보내고 컨테이너는 요청 URI의 경로를 기반으로 어떤 필터들과 어떤 서블릿을 적용할지 결정합니다. 최대 하나의 서블릿이 단일 요청을 처리할 수 있지만 필터들은 체인을 형성하므로 순서화됩니다. 사실 필터는 요청 자체만 처리하려는 경우, 체인의 나머지 부분을 거부할 수 있습니다. 또한 필터는 자신보다 아래에 있는 필터들과 서블릿에서 사용되는 요청(request) 혹은 응답(response)을 수정할 수 있습니다. 

필터 체인의 순서는 매우 중요하며 Spring Boot는 두 가지 메커니즘을 통해 이를 관리합니다. `Filter` 유형의 `@Bean`은 `@Order`를 갖거나 `Ordered`를 구현할 수 있으며 API에 자체 순서를 가지고 있는 `FilterRegistrationBean`의 일부가 될 수 있습니다. 일부 기성(off-the-shelf) 필터는 서로에 대해 원하는 순서를 알리는 데 도움이 되는 고유한 상수들을 정의합니다(예를 들어, Spring Session의 `SessionRepositoryFilter`는 `DEFAULT_ORDER`가 `Integer.MIN_VALUE + 50`이며 이는 체인의 초기에 있지만 그 앞에 오는 다른 필터를 배제하지 않습니다.

## DelegatingFilterProxy

**Spring은 Servlet 컨테이너의 라이프사이클과 Spring의 `ApplicationContext` 사이를 연결하는 [DelegatingFilterProxy](https://docs.spring.io/spring-framework/docs/5.3.20/javadoc-api/org/springframework/web/filter/DelegatingFilterProxy.html)라는 `Filter`의 구현을 제공합니다.** 서블릿 컨테이너 자체 표준을 사용하여 필터를 등록할 수 있지만 Spring에서 정의한 Bean을 인식하지 못합니다. **`DelegatingFilterProxy`는 표준 서블릿 컨테이너 메커니즘을 통해 등록할 수 있지만 모든 작업은 `Filter`를 구현하는 Spring Bean에 위임**합니다.
다음은 `DelegatingFilterProxy`가 [Filter들과 FilterChain](https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-filters-review)에 어떻게 구조화 되는지 보여줍니다.

![https://docs.spring.io/spring-security/reference/_images/servlet/architecture/delegatingfilterproxy.png](https://docs.spring.io/spring-security/reference/_images/servlet/architecture/delegatingfilterproxy.png)

`DelegatingFilterProxy`는 `ApplicationContext`에서 Bean Filter0을 찾은 다음 Bean Filter0을 호출합니다. 아래는 `DelegatingFilterProxy`의 의사 코드입니다([Multiple HttpSecurity](https://docs.spring.io/spring-security/reference/servlet/configuration/java.html#_multiple_httpsecurity)). 

```java
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) {
		// Lazily get Filter that was registered as a Spring Bean
		// For the example in DelegatingFilterProxy 

	delegate

 is an instance of Bean Filter0
			Filter delegate = getFilterBean(someBeanName);
			// delegate work to the Spring Bean
			delegate.doFilter(request, response);
}
```

**`DelegatingFilterProxy`의 또 다른 이점은 `Filter` 빈 인스턴스들을 찾는 것을 지연시킬 수 있다는 것**입니다. 이 부분은 서블릿 컨테이너가 시작되기 전에 `Filter` 인스턴스들을 등록해야 하는 점 때문에 중요합니다. Spring은 일반적으로 `ContextLoaderListener`를 사용하여 Spring Bean을 로드합니다. 그러나 Spring Bean들은 `Filter` 인스턴스를 등록해야 하는 시점에 로드되지 못합니다.

## FilterChainProxy

Servlet에 대한 스프링 시큐리티의 지원은 `FilterChainProxy`에 포함돼 있습니다. **`FilterChainProxy`는 Spring Security에서 제공하는 특수 `Filter` 입니다**. **`FilterChainProxy`는 [SecurityFilterChain](https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-securityfilterchain)을 통해 많은 `Filter` 인스턴스들에게 위임할 수 있게 합니다**. `FilterChainProxy`는 Bean이므로 일반적으로 [DelegatingFilterProxy](https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-delegatingfilterproxy)에 래핑됩니다.

![https://docs.spring.io/spring-security/reference/_images/servlet/architecture/filterchainproxy.png](https://docs.spring.io/spring-security/reference/_images/servlet/architecture/filterchainproxy.png)

## SecurityFilterChain

`SecurityFilterChain`는 [FilterChainProxy](https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-filterchainproxy)가 현재의 요청(request)에 어떤 스프링 시큐리티 `Filter`들을 사용할 지 결정하는 데 사용됩니다.

![https://docs.spring.io/spring-security/reference/_images/servlet/architecture/securityfilterchain.png](https://docs.spring.io/spring-security/reference/_images/servlet/architecture/securityfilterchain.png)

`SecurityFilterChain`의 [Security Filter들](https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-security-filters)은 일반적으로 Bean입니다. 이들은 [DelegatingFilterProxy](https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-delegatingfilterproxy)이 아닌 `FilterChainProxy`에 등록됩니다. **`FilterChainProxy`는 [Security Filter들](https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-security-filters)을 Servlet 컨테이너 또는 [DelegatingFilterProxy](https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-delegatingfilterproxy)에 직접 등록하는 것과 비교하여 많은 이점을 제공합니**다.

**먼저, Spring Security의 모든 Servlet 지원을 위한 시작점을 제공합니다**. 이러한 이유로 Spring Security의 Servlet 지원 문제를 트러블 슈팅하기 위해 `FilterChainProxy`에 디버그 포인트를 추가하는 것은 좋은 시작점입니다.

**둘째, `FilterChainProxy`는 Spring Security 사용의 핵심이기 때문에 선택 사항으로 보이지 않는 작업들을 수행할 수 있습니다**. 예를 들어 메모리 누수를 방지하기 위해 `SecurityContext`를 지웁니다. 또한 Spring Security의 [HttpFirewall](https://docs.spring.io/spring-security/reference/servlet/exploits/firewall.html#servlet-httpfirewall)을 적용하여 특정 유형의 공격들로부터 애플리케이션을 보호합니다.

**또한 `FilterChainProxy`는 언제 `SecurityFilterChain`을 호출하는 지를 결정하는 데 있어 더 많은 유연성을 제공**합니다. 서블릿 컨테이너의 `Filter`들은 URL만을 기반으로 호출됩니다. 그러나 `FilterChainProxy`는 `RequestMatcher` 인터페이스를 활용하여 `HttpServletRequest`의 모든 항목을 기반으로 호출을 결정할 수 있습니다.

특히 `FilterChainProxy`는 어떤 `SecurityFilterChain`을 사용하는 지를 결정하는데 사용될 수 있습니다. 이를 통해 애플리케이션의 여러 부분에 완전히 별도의 구성(configuration)을 제공할 수 있습니다.

![https://docs.spring.io/spring-security/reference/_images/servlet/architecture/multi-securityfilterchain.png](https://docs.spring.io/spring-security/reference/_images/servlet/architecture/multi-securityfilterchain.png)

*Multiple SecurityFilterChain*

위의 [Multiple SecurityFilterChain](https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-multi-securityfilterchain-figure) 그림에서 `FilterChainProxy`는 어떤 `SecurityFilterChain`을 사용할 지를 결정합니다. **일치하는 첫 번째 SecurityFilterChain만 호출됩니다**. 

`/api/messages/` URL이 요청되면, 먼저 `SecurityFilterChain0`의 패턴 `/api/**`와 일치하므로 `SecurityFilterChainn`에서도 일치하더라도 `SecurityFilterChain0`만 호출됩니다. 

`/messages/`의 URL이 요청되면 `SecurityFilterChain0`의 `/api/**` 패턴과는 일치하지 않으므로 `FilterChainProxy`는 계속해서 다른 각각의 `SecurityFilterChain`들을 시도합니다. 중간에 다른 일치하는 인스턴스들이 없다고 가정하면, 이 요청은 `/**`와 일치하므로 `SecurityFilterChainn`이 호출됩니다.

위의 그림을 자세히 보면 `SecurityFilterChain0`에는 3개의 보안 필터 인스턴스로 구성되어 있습니다. 그러나 `SecurityFilterChainn`에는 4개의 보안 필터가 구성되어 있습니다. 즉, 각 `SecurityFilterChain`은 고유(unique)하고 별도(isolation)로 구성될 수 있다는 점을 주목해 볼 수 있습니다. 만약 어플리케이션이 Spring Security가 요청들을 무시하기를 원하는 경우, `SecurityFilterChain`는 0개의 보안 필터를 가질 수도 있습니다.

다음은 Multiple SecurityFilterChain의 구성 코드 예시입니다. 복수의 `SecurityFilterChain` `@Bean`을 등록하고 있습니다([Multiple HttpSecurity](https://docs.spring.io/spring-security/reference/servlet/configuration/java.html#_multiple_httpsecurity_instances)).

```java
@Configuration
@EnableWebSecurity
public class MultiHttpSecurityConfig {
	@Bean
	public UserDetailsService userDetailsService() throws Exception { // (1)
		// ensure the passwords are encoded properly
		UserBuilder users = User.withDefaultPasswordEncoder();
		InMemoryUserDetailsManager manager = new InMemoryUserDetailsManager();
		manager.createUser(users.username("user").password("password").roles("USER").build());
		manager.createUser(users.username("admin").password("password").roles("USER","ADMIN").build());
		return manager;
	}

	@Bean
	@Order(1)
	public SecurityFilterChain apiFilterChain(HttpSecurity http) throws Exception { // (2)
		http
			.securityMatcher("/api/**"). // (3)
			.authorizeHttpRequests(authorize -> authorize
				.anyRequest().hasRole("ADMIN")
			)
			.httpBasic(withDefaults());
		return http.build();
	}

	@Bean
	public SecurityFilterChain formLoginFilterChain(HttpSecurity http) throws Exception { // (4)
		http
			.authorizeHttpRequests(authorize -> authorize
				.anyRequest().authenticated()
			)
			.formLogin(withDefaults());
		return http.build();
	}
}
```

- 평소와 같이 인증을 구성합니다. Configure Authentication as usual.
- 먼저 고려해야 할 SecurityFilterChain을 지정하기 위해 `@Order`가 포함된 SecurityFilterChain의 인스턴스를 만듭니다.
- `http.securityMatcher`는 이 HttpSecurity가 `/api/`로 시작하는 URL에만 적용된다고 명시합니다.
- `SecurityFilterChain`의 다른 인스턴스를 만듭니다. URL이 `/api/`로 시작하지 않는 경우 이 구성이 사용됩니다. 이 구성은 `apiFilterChain` 이후에 고려됩니다. 1 뒤에 `@Order` 값이 있기 때문입니다(`@Order` 기본값이 마지막이 아님).

## Security Filters

보안 필터들(Security Filters)은 [SecurityFilterChain](https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-securityfilterchain) API를 사용하여 [FilterChainProxy](https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-filterchainproxy)에 삽입됩니다.

### 필터들의 순서

필터는 넣거나 뺄 수 있고 순서를 조절할 수 있습니다. 이때, 필터의 순서가 매우 중요할 수 있기 때문에 기본 필터들은 그 순서가 어느정도 정해져 있습니다. 아래는 Spring Security Filter 순서의 전체 목록입니다.

- [ForceEagerSessionCreationFilter](https://docs.spring.io/spring-security/reference/servlet/authentication/session-management.html#session-mgmt-force-session-creation)
- ChannelProcessingFilter
- WebAsyncManagerIntegrationFilter
- SecurityContextPersistenceFilter
- HeaderWriterFilter
- CorsFilter
- CsrfFilter
- LogoutFilter
- OAuth2AuthorizationRequestRedirectFilter
- Saml2WebSsoAuthenticationRequestFilter
- X509AuthenticationFilter
- AbstractPreAuthenticatedProcessingFilter
- CasAuthenticationFilter
- OAuth2LoginAuthenticationFilter
- Saml2WebSsoAuthenticationFilter
- [UsernamePasswordAuthenticationFilter](https://docs.spring.io/spring-security/reference/servlet/authentication/passwords/form.html#servlet-authentication-usernamepasswordauthenticationfilter)
- OpenIDAuthenticationFilter
- DefaultLoginPageGeneratingFilter
- DefaultLogoutPageGeneratingFilter
- ConcurrentSessionFilter
- [DigestAuthenticationFilter](https://docs.spring.io/spring-security/reference/servlet/authentication/passwords/digest.html#servlet-authentication-digest)
- BearerTokenAuthenticationFilter
- [BasicAuthenticationFilter](https://docs.spring.io/spring-security/reference/servlet/authentication/passwords/basic.html#servlet-authentication-basic)
- RequestCacheAwareFilter
- SecurityContextHolderAwareRequestFilter
- JaasApiIntegrationFilter
- RememberMeAuthenticationFilter
- AnonymousAuthenticationFilter
- OAuth2AuthorizationCodeGrantFilter
- SessionManagementFilter
- [ExceptionTranslationFilter](https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-exceptiontranslationfilter)
- [FilterSecurityInterceptor](https://docs.spring.io/spring-security/reference/servlet/authorization/authorize-requests.html#servlet-authorization-filtersecurityinterceptor)
- SwitchUserFilter

### 필터들의 관심사

“`SecurityFilterChain`에 어떤 필터들을 넣을 것인가”는 스프링 시큐리티의 가장 큰 부분이기도 하다. 각각의 필터는 ‘단일 책임 원칙’ 처럼, 각기 서로 다른 관심사를 해결합니다. 예를 들면 다음과 같습니다.

- *HeaderWriterFilter* : Http 해더를 검사한다. 써야 할 건 잘 써있는지, 필요한 해더를 더해줘야 할 건 없는가?
- *CorsFilter* : 허가된 사이트나 클라이언트의 요청인가?
- *CsrfFilter* : 내가 내보낸 리소스에서 올라온 요청인가?
- *LogoutFilter* : 지금 로그아웃하겠다고 하는건가?
- *UsernamePasswordAuthenticationFilter* : username / password 로 로그인을 하려고 하는가? 만약 로그인이면 여기서 처리하고 가야 할 페이지로 보내 줄께.
- *ConcurrentSessionFilter* : 여거저기서 로그인 하는걸 허용할 것인가?
- *BearerTokenAuthenticationFilter* : Authorization 해더에 Bearer 토큰이 오면 인증 처리 해줄께.
- *BasicAuthenticationFilter* : Authorization 해더에 Basic 토큰을 주면 검사해서 인증처리 해줄께.
- *RequestCacheAwareFilter* : 방금 요청한 request 이력이 다음에 필요할 수 있으니 캐시에 담아놓을께.
- *SecurityContextHolderAwareRequestFilter* : 보안 관련 Servlet 3 스펙을 지원하기 위한 필터라고 한다.(?)
- *RememberMeAuthenticationFilter* : 아직 Authentication 인증이 안된 경우라면 RememberMe 쿠키를 검사해서 인증 처리해줄께
- *AnonymousAuthenticationFilter* : 아직도 인증이 안되었으면 너는 Anonymous 사용자야
- *SessionManagementFilter* : 서버에서 지정한 세션정책을 검사할께.
- *ExcpetionTranslationFilter* : 나 이후에 인증이나 권한 예외가 발생하면 내가 잡아서 처리해 줄께.
- *FilterSecurityInterceptor* : 여기까지 살아서 왔다면 인증이 있다는 거니, 니가 들어가려고 하는 request 에 들어갈 자격이 있는지 그리고 리턴한 결과를 너에게 보내줘도 되는건지 마지막으로 내가 점검해 줄께.
- 그 밖에... OAuth2 나 Saml2, Cas, X509 등에 관한 필터들도 있다.

---

**참조**

[https://docs.spring.io/spring-security/reference/servlet/architecture.html](https://docs.spring.io/spring-security/reference/servlet/architecture.html)

[https://spring.io/guides/topicals/spring-security-architecture/](https://spring.io/guides/topicals/spring-security-architecture/)

[https://gitlab.com/jongwons.choi/spring-boot-security-lecture/-/blob/master/part1/Lec-1 스프링시큐리티의큰그림.md](https://gitlab.com/jongwons.choi/spring-boot-security-lecture/-/blob/master/part1/Lec-1%20%EC%8A%A4%ED%94%84%EB%A7%81%EC%8B%9C%ED%81%90%EB%A6%AC%ED%8B%B0%EC%9D%98%ED%81%B0%EA%B7%B8%EB%A6%BC.md)
