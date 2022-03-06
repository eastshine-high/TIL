# Servlet filter

## 왜 사용할까?

### 공통 관심 사항

‘로그인 한 사용자만 상품 관리 페이지에 들어갈 수 있어야 한다’는 요구사항이 있다고 가정해 보자. 만약 관련 체크 로직이 없다면 로그인하지 않은 사용자도 관련 URL을 직접 호출하면 상품 관리 화면에 들어갈 수 있다.

상품 관리 컨트롤러에서 로그인 여부를 체크하는 로직을 하나하나 작성하면 되겠지만, 등록, 수정, 삭제, 조회 등등 상품관리의 모든 컨트롤러 로직에 공통으로 로그인 여부를 확인해야 한다. 더 큰 문제는 향후 로그인과 관련된 로직이 변경될 때 이다. 작성한 모든 로직을 다 수정해야 할 수 있다.

이렇게 애플리케이션 여러 로직에서 공통으로 관심이 있는 있는 것을 공통 관심사(cross-cutting concern)라고 한다. 여기서는 등록, 수정, 삭제, 조회 등등 여러 로직에서 공통으로 인증에 대해서 관심을 가지고 있다.

이러한 공통 관심사는 스프링의 AOP로도 해결할 수 있지만, 웹과 관련된 공통 관심사는 지금부터 설명할 서블릿 필터 또는 스프링 인터셉터를 사용하는 것이 좋다. 웹과 관련된 공통 관심사를 처리할 때는 HTTP의 헤더나 URL의 정보들이 필요한데, 서블릿 필터나 스프링 인터셉터는 HttpServletRequest 를 제공한다.

> AOP는 부가적인 기능이 거의 없다. 어떤 메소드가 호출되었는 지 정도만 알 수 있다. 서브릿의 필터나 스프링의 인터셉터는 수 많은 웹 관련 부가 기능을 제공해 준다.
> 

<br>

## 필터의 특성

필터는 서블릿이 지원하는 수문장이다. 필터의 특성은 다음과 같다.

### 필터 흐름

```
HTTP 요청 ->WAS-> 필터 -> 서블릿 -> 컨트롤러
```

필터를 적용하면 필터가 호출 된 다음에 서블릿이 호출된다. 그래서 모든 고객의 요청 로그를 남기는
요구사항이 있다면 필터를 사용하면 된다. 참고로 필터는 특정 URL 패턴에 적용할 수 있다. /* 이라고
하면 모든 요청에 필터가 적용된다.
참고로 스프링을 사용하는 경우 여기서 말하는 서블릿은 스프링의 디스패처 서블릿으로 생각하면 된다.

### 필터 제한

```
HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 컨트롤러 //로그인 사용자
HTTP 요청 -> WAS -> 필터(적절하지 않은 요청이라 판단, 서블릿 호출X) //비 로그인 사용자
```

필터에서 적절하지 않은 요청이라고 판단하면 거기에서 끝을 낼 수도 있다. 그래서 로그인 여부를 체크하기에 딱 좋다.

### 필터 체인

```
HTTP 요청 ->WAS-> 필터1-> 필터2-> 필터3-> 서블릿 -> 컨트롤러
```

터는 체인으로 구성되는데, 중간에 필터를 자유롭게 추가할 수 있다. 예를 들어서 로그를 남기는 필터를 먼저 적용하고, 그 다음에 로그인 여부를 체크하는 필터를 만들 수 있다.

### 필터 인터페이스

필터 인터페이스는 다음과 같이 구성되어 있다.

```java
public interface Filter {
    public default void init(FilterConfig filterConfig) throws ServletException
  
    public void doFilter(ServletRequest request, ServletResponse response,
            FilterChain chain) throws IOException, ServletException;

    public default void destroy()
 }
```

필터 인터페이스를 구현하고 등록하면 서블릿 컨테이너가 필터를 싱글톤 객체로 생성하고, 관리한다.

- `init()`: 필터 초기화 메서드, 서블릿 컨테이너가 생성될 때 호출된다.
- `doFilter()`: 고객의 요청이 올 때 마다 해당 메서드가 호출된다. 필터의 로직을 구현하면 된다.
- `destroy()`: 필터 종료 메서드, 서블릿 컨테이너가 종료될 때 호출된다.

### 구현 예

```java
import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletRequest;
import java.io.IOException;
import java.util.UUID;

@Slf4j
public class LogFilter implements Filter {

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        log.info("log filter doFilter");

        HttpServletRequest httpRequest = (HttpServletRequest) request;
        String requestURI = httpRequest.getRequestURI();

        String uuid = UUID.randomUUID().toString();

        try {
            log.info("REQUEST [{}][{}]", uuid, requestURI);
            chain.doFilter(request, response);
        } catch (Exception e) {
            throw e;
        } finally {
            log.info("RESPONSE [{}][{}]", uuid, requestURI);
        }

    }
}

```

- `public class LogFilter implements Filter {}`
    - 필터를 사용하려면 필터 인터페이스를 구현해야 한다.
- `doFilter(ServletRequest request, ServletResponse response, FilterChain chain)`
    - HTTP 요청이 오면 doFilter 가 호출된다.
    - ServletRequest request 는 HTTP 요청이 아닌 경우까지 고려해서 만든 인터페이스이다. HTTP를 사용하면 HttpServletRequest httpRequest = (HttpServletRequest) request; 와 같이 다운 케스팅 하면 된다.
- `String uuid = UUID.randomUUID().toString();`
    - HTTP 요청을 구분하기 위해 요청당 임의의 uuid 를 생성해둔다.
- `log.info("REQUEST [{}][{}]", uuid, requestURI);`
    - uuid 와 requestURI 를 출력한다.
- `chain.doFilter(request, response);`
    - 이 부분이 가장 중요하다. 다음 필터가 있으면 필터를 호출하고, 필터가 없으면 서블릿을 호출한다.
    만약 이 로직을 호출하지 않으면 다음 단계로 진행되지 않는다.

<br>

---

## 참조

<김영한, 스프링 MVC 2편 - 백엔드 웹 개발 활용 기술(강의), 인프런>
