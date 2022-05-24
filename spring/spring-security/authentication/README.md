# 인증(Authentication)

애플리케이션 보안은 두 가지 정도의 독립적인 문제로 요약된다 : **인증(Authentication**, 당신은 누구입니까)과 **인가(authorization**, 당신은 무엇을 할 수 있습니까)이다. Spring Security에는 인증과 인가를 분리하도록 설계된 아키텍처가 있으며 이에 대한 각각의 전략들과 확장점들(extension points)을 가지고 있다. 이번 페이지에서는 인증에 대해서 알아본다.

**인증을 위한 주요 전략 인터페이스는 `AuthenticationManager`이다.** 이 인터페이스는 하나의 메소드만 가지고 있다.

```java
public interface AuthenticationManager {

  Authentication authenticate(Authentication authentication)
    throws AuthenticationException;
}
```

따라서 `AuthenticationManager` 는 `authenticate()` 메소드로 3가지 중 하나를 할 수 있다.

- 입력이 유효한 주체(principal)를 나타내는지 확인할 수 있는 경우, `Authentication`(보통 `authenticated=true`도 같이)을 반환한다.
- 입력이 유효하지 않은 주체(principal)를 나타낸다고 판단되면 `AuthenticationException`을 던진다.
- 판단할 수 없으면 `null`을 반환한다.

`AuthenticationException`은 런타임 예외다. 보통 애플리케이션의 스타일이나 목적에 따라 일반적인 방식(generic way)으로 어플리케이션에서 처리된다. 다시 말하면, 사용자의 코드가 이를 포착하고 처리할 것으로 예상되지는 않는다. 예를 들어 웹 UI는 인증이 실패됐다는 메세지를 나타내는 페이지를 렌더링할 수 있다. 그리고 백엔드 HTTP 서비스는 401 응답(컨텍스트에 따라 `WWW-Authenticate` 헤더를 포함할 수도 있다)을 보낼 것이다.

**`ProviderManager`는가장 일반적인 `AuthenticationManager`의 구현체이다. 이 구현체는`AuthenticationProvider` 인스턴스들 중 하나의 체인에 위임**한다. `AuthenticationProvider`는 `AuthenticationManager`와 약간 비슷하다. 하지만 호출자가 주어진 `Authentication` type을 지원하는지 여부를 질의할 수 있도록 하는 추가 메소드를 가진다.

```java
public interface AuthenticationProvider {

	Authentication authenticate(Authentication authentication)
			throws AuthenticationException;

	boolean supports(Class<?> authentication);
}
```

`supports()` 메소드에 있는 `Class<?>` 아규먼트는 정확히는 `Class<? extends Authentication>`이다( `authenticate()` 메서드에  전달되는 아규먼트가 지원되는 지 여부만 묻는다).

`ProviderManager`는 `AuthenticationProvider`들의 체인에 위임함으로써 같은 어플리케이션에 있는 여러 다른 인증 메커니즘들을 지원할 수 있다. 만약 `ProviderManager`가 어떤 특정 `Authentication` 인스턴스 타입을 인지하지 못했을 경우, 이 과정은 생략된다.

`ProviderManager`에는 선택적(optional) 부모가 있으며 모든 공급자가 `null`을 반환하는 경우 이를 참조(consult)할 수 있습니다. 만약 이 부모를 이용할 수 없는 경우, `null` `Authentication`은 `AuthenticationException`을 발생시킨다.

애플리케이션은 보호되는 리소스의 논리적 그룹들(예를 들어 `/api/**`과 같은 경로 패턴과 일치하는 모든 웹 리소스들)을 가질 수 있고 각 그룹은 전용 `AuthenticationManager`을 가질 수 있다. 보통 각 `AuthenticationManager`들은  `ProviderManager`이고 한 부모를 공유한다. 이 부모는 일종의 "전역" 리소스인데, 모든 공급자들의 대비책(fallback)을 수행한다.

![https://github.com/spring-guides/top-spring-security-architecture/raw/main/images/authentication.png](https://github.com/spring-guides/top-spring-security-architecture/raw/main/images/authentication.png)

Figure 1. `ProviderManager`(구현체)를 사용하는 **`AuthenticationManager`의 계층 구조**

### **Customizing** Authentication Managers

**스프링 시큐리티는 애플리케이션에 공통 인증 관리자 기능을 빠르게 설정하기 위한 몇 가지 구성 도우미(configuration helper)들을 제공**한다. 가장 일반적으로 쓰이는 도우미**는 `AuthenticationManagerBuilder`이**다. 이것은 **메모리 내, JDBC 또는 LDAP 사용자 세부 정보를 설정하거나 커스텀 `UserDetailsService`를 추가**하는 데 유용하다. 아래 예는 애플리케이션의 전역(부모)`AuthenticationManager`를 설정하는 것을 보여준다.

```java
@Configuration
public class ApplicationSecurity extends WebSecurityConfigurerAdapter {
	
	   ... // web stuff here
	
	  @Autowired
	  public void initialize(AuthenticationManagerBuilder builder, DataSource dataSource) {
	    builder.jdbcAuthentication().dataSource(dataSource).withUser("dave")
	      .password("secret").roles("USER");
	  }
}
```

이 예제는 웹 어플리케이션과 관련이 있지만 `AuthenticationManagerBuilder` 사용이 더 광범위하게 적용된다(웹 어플리케이션 보안 구현 방법에 대한 자세한 내용은 **[Web Security](https://spring.io/guides/topicals/spring-security-architecture/#web-security)** 글 참조). `**AuthenticationManagerBuilder`는 `@Bean` 안에 있는 메소드에 `@Autowired` 되어 있는 것에 주목하자. 이것이 전역(부모) `AuthenticationManager`를 빌드하게 한다.** 

이와 대조적으로 아래의 예를 한번 봐 보자.

```java
@Configuration
public class ApplicationSecurity extends WebSecurityConfigurerAdapter {

	  @Autowired
	  DataSource dataSource;
	
	   ... // web stuff here
	
	  @Override
	  public void configure(AuthenticationManagerBuilder builder) {
	    builder.jdbcAuthentication().dataSource(dataSource).withUser("dave")
	      .password("secret").roles("USER");
	  }
}
```

**구성자(configurer)에 있는 메서드에 `@Override`를 사용한 경우, `AuthenticationManagerBuilder`는 전역 `AuthenticationManager`의 자식이 되는 "로컬" `AuthenticationManager`를 빌드하는 데 사용된다.** Spring Boot 애플리케이션에서는 전역`AuthenticationManager`를 다른 bean으로 `@Autowired`할 수 있다. 하지만 명시적으로 직접 노출하려 하지 않는 한 로컬`AuthenticationManager`에 그렇게 할 필요는 없다. 

Spring Boot는 기본 전역 `AuthenticationManager`(사용자가 한 명)를 제공한다(`AuthenticationManager` 유형의 자체 빈을 제공하여 선점하지 않는 한). 사용자 정의 전역 `AuthenticationManager`가 적극적으로 필요하지 않는 한 기본값은 자체적으로 충분히 안전하므로 크게 걱정할 필요는 없다. 만약 `AuthenticationManager`를 만들어 구성을 하는 경우, 보호하는 리소스들에 대해 지역적으로 수행할 수 있으며 전역 기본값에 대해서 걱정할 필요는 없다.

### 참조

[https://spring.io/guides/topicals/spring-security-architecture/](https://spring.io/guides/topicals/spring-security-architecture/)
