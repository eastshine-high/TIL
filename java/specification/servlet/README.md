# Servlet

**Servlet은 요청-응답 프로그래밍 모델을 통해 접근되는 (어플리케이션을 호스팅하는)서버의 기능을 확장하는 데 사용되는 Java 클래스**(요청에 응답하는 자바 클래스를 구현하기 위한 표준인 [Servlet API](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/index.html)를 준수)다. 서블릿은 모든 유형의 요청에 응답할 수 있지만 일반적으로 웹 서버에서 호스팅하는 어플리케이션을 확장하는 데 사용된다. 이러한 어플리케이션을 위해 Java Servlet 기술은 특정 HTTP Servlet 클래스를 정의한다.

![https://velog.velcdn.com/images/eastshine-high/post/8e44dda6-8a1a-444a-a96c-9017a71ae033/image.png](https://velog.velcdn.com/images/eastshine-high/post/8e44dda6-8a1a-444a-a96c-9017a71ae033/image.png)

Servlet 구현 예

```java
@WebServlet(name = "helloServlet", urlPatterns = "/hello")
public class ServletLifeCycleExample extends HttpServlet {
    private Integer sharedCounter;

    @Override
    public void init(final ServletConfig config) throws ServletException {
        super.init(config);
        getServletContext().log("init() called");
        sharedCounter = 0;
    }

    @Override
    protected void service(final HttpServletRequest request, final HttpServletResponse response) throws ServletException, IOException {
				// 업무 로직 작성
        getServletContext().log("service() called");
        int localCounter;
        synchronized (sharedCounter) {
            sharedCounter++;
            localCounter = sharedCounter;
        }
        response.getWriter().write("Incrementing the count to " + localCounter);  // accessing a local variable
    }

    @Override
    public void destroy() {
        getServletContext().log("destroy() called");
    }
}
```

- urlPatterns(**`/hello`**)의 URL이 호출되면 서블릿 코드가 실행된다.
- HTTP 요청 정보를 편리하게 사용할 수 있는 HttpServletRequest
- HTTP 응답 정보를 편리하게 제공할 수 있는 HttpServletResponse
- 개발자는 HTTP 스펙을 매우 편리하게 사용할 수 있다.

# 서블릿 컨테이너

- 웹 컨테이너(서블릿 컨테이너라고도 함)는 `javax.servlet.Servlet`과 상호 작용하는 웹 서버의 컴포넌트다(e.g. Apache Tomcat).
- 웹 컨테이너는 서블릿의 수명 주기(서블릿 객체를 생성, 초기화, 호출, 종료)를 관리한다.
- 서블릿 객체는 싱글톤으로 관리
    - 모든 고객의 요청은 동일한 서블릿 객체 인스턴스에 접근한다. 때문에 **공유 변수 사용에 주의**해야 한다.
- URL을 특정 서블릿에 매핑하고 URL 요청자가 올바른 액세스 권한을 갖고 있는지 확인한다.
- 웹 컨테이너는 서블릿, JSP(Jakarta Server Pages) 파일 및 서버 측 코드를 포함하는 기타 유형의 파일에 대한 요청을 처리한다.
    - HTTP 요청이 들어오면 요청 및 응답 객체를 생성 및 관리하고, 기타 서블릿 관리 작업을 수행한다.
    - JSP도 서블릿으로 변환 되어서 사용.
- 동시 요청을 위한 멀티 쓰레드 처리를 지원한다.
- 웹 컨테이너는 Jakarta EE 아키텍처의 웹 구성 요소 계약을 구현한다.
    - 이 아키텍처는 보안, 동시성, 수명 주기 관리, 트랜잭션, 배포 및 기타 서비스를 포함한 추가 웹 구성 요소에 대한 런타임 환경을 지정한다.
    - e.g. The Apache Tomcat®  software is an open source implementation of the [Jakarta Servlet](https://projects.eclipse.org/projects/ee4j.servlet), [Jakarta Server Pages](https://projects.eclipse.org/projects/ee4j.jsp), [Jakarta Expression Language](https://projects.eclipse.org/projects/ee4j.el), [Jakarta WebSocket](https://projects.eclipse.org/projects/ee4j.websocket), [Jakarta Annotations](https://projects.eclipse.org/projects/ee4j.ca) and [Jakarta Authentication](https://projects.eclipse.org/projects/ee4j.authentication) specifications. These specifications are part of the [Jakarta EE platform](https://projects.eclipse.org/projects/ee4j.jakartaee-platform).

---

### 참조

[https://en.wikipedia.org/wiki/Jakarta_Servlet](https://en.wikipedia.org/wiki/Jakarta_Servlet)

[https://docs.oracle.com/javaee/5/tutorial/doc/bnafe.html](https://docs.oracle.com/javaee/5/tutorial/doc/bnafe.html)

<김영한, 스프링 웹 MVC 1편(강의), 인프런>
