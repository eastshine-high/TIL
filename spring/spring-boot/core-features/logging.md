# logging

Spring Boot는 모든 내부 로깅에 대해 [Commons Logging](https://commons.apache.org/logging)을 사용하지만 기본 로그 구현 또한 가능하다. 기본적으로 [Java Util Logging](https://docs.oracle.com/javase/8/docs/api/java/util/logging/package-summary.html), [Log4J2](https://logging.apache.org/log4j/2.x/) 및 [Logback](https://logback.qos.ch/)에 대한 구성(configuration)들이 제공된다. 각 로그들 모두 콘솔 출력을 사용하도록 미리 구성되어 있으며 선택적으로 파일 출력을 사용할 수 있다.

스프링 부트에서는 기본적으로 `logback`이 로그에 사용된다. Java Util Logging, Commons Logging, Log4J 또는 SLF4J를 사용하는 라이브러리 의존성이 모두 올바르게 작동하기 위해 적절한 Logback 라우팅도 포함되어 있다.

### 로그 형식(Format)

스프링 부트의 기본적인 로그 출력은 다음과 유사하다.

```
`2019-03-05 10:57:51.112  INFO 45469 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/7.0.52
2019-03-05 10:57:51.253  INFO 45469 --- [ost-startStop-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2019-03-05 10:57:51.253  INFO 45469 --- [ost-startStop-1] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 1358 ms
2019-03-05 10:57:51.698  INFO 45469 --- [ost-startStop-1] o.s.b.c.e.ServletRegistrationBean        : Mapping servlet: 'dispatcherServlet' to [/]
2019-03-05 10:57:51.702  INFO 45469 --- [ost-startStop-1] o.s.b.c.embedded.FilterRegistrationBean  : Mapping filter: 'hiddenHttpMethodFilter' to: [/*]`
```

다음의 항목이 출력된다.

- Date and Time: Millisecond precision and easily sortable.
- Log Level: `ERROR`, `WARN`, `INFO`, `DEBUG`, or `TRACE`.
- Process ID.
- A `--` separator to distinguish the start of actual log messages.
- Thread name: Enclosed in square brackets (may be truncated for console output).
- Logger name: This is usually the source class name (often abbreviated).
- The log message.

### 로그 레벨 설정

다음은 `application.properties`에서 로그 레벨를 설정하는 예다.

```
logging.level.root=warn
logging.level.org.springframework.web=debug
logging.level.org.hibernate=error
logging.level.com.eastshine=debug
```

- 위와 같은 방식의 로그 레벨 설정은 패키지 레벨만 가능하다(클래스 레벨은 로거를 이용).
- `logging.level.root` : 전체 로그 레벨 설정을 한다. 기본은 info이다.
- `logging.level.com.eastshine` : `com.eastshine` 패키지와 그 하위의 로그 레벨 설정

### 로그 그룹

로그는 그룹을 통해서 한 곳에서 동시에 설정할 수 있다. 다음은 톰켓과 관련된 로거 설정을 그룹지어 설정한 예다.

`application.properties`

```
logging.group.tomcat=org.apache.catalina,org.apache.coyote,org.apache.tomcat

logging.level.tomcat=trace
```

### 파일 출력(**File Output**)

기본적으로 Spring Boot는 콘솔에만 로그를 하고 로그 파일을 작성하지 않는다. 콘솔 출력 외에 로그 파일을 작성하려면 `application.properties`에서 `logging.file.name` 또는 `logging.file.path` 속성을 설정해야 한다.
다음 표는 `logging.*` 속성들을 사용하는 방법을 보여준다.

| logging.file.name | logging.file.path | Example | Description |
| --- | --- | --- | --- |
| (none) | (none) |  | Console only logging. |
| Specific file | (none) | my.log | Writes to the specified log file. Names can be an exact location or relative to the current directory. |
| (none) | Specific directory | /var/log | Writes spring.log to the specified directory. Names can be an exact location or relative to the current directory. |

로그 파일은 10MB에 도달하면 회전하고 콘솔 출력과 마찬가지로 기본적으로 `ERROR`,  `WARN`, `INFO` 레벨의 메시지가 기록된다.

> 주의
`logging.file.name`과 `logging.file.path`은 속성명으로 보면 함께 사용하는 속성으로 보인다. 하지만 표에서 보여주듯 **두 속성은 같이 사용할 수 없다.** `logging.file.path` 속성을 사용할 경우 파일 이름을 `spring.log`로 고정된다.
> 

### 파일 회전(**File Rotation**)

Logback을 사용하는 경우, `application.properties` 또는 `application.yaml` 파일을 사용하여 로그 회전을 조정할 수 있다. 다른 모든 로깅 시스템의 경우 회전 설정을 직접 구성해야 한다(예: Log4J2를 사용하는 경우 log4j2.xml 또는 log4j2-spring.xml 파일을 추가할 수 있다).

다음은 지원되는 순환 정책 속성들(rotation policy properties)이다.

| Spring Environment | System Property | Comments |
| --- | --- | --- |
| logging.logback.rollingpolicy.file-name-pattern | LOGBACK_ROLLINGPOLICY_FILE_NAME_PATTERN | Pattern for rolled-over log file names (default ${LOG_FILE}.%d{yyyy-MM-dd}.%i.gz). |
| logging.logback.rollingpolicy.clean-history-on-start | LOGBACK_ROLLINGPOLICY_CLEAN_HISTORY_ON_START | Whether to clean the archive log files on startup. |
| logging.logback.rollingpolicy.max-file-size | LOGBACK_ROLLINGPOLICY_MAX_FILE_SIZE | Maximum log file size. |
| logging.logback.rollingpolicy.total-size-cap | LOGBACK_ROLLINGPOLICY_TOTAL_SIZE_CAP | Total size of log backups to be kept. |
| logging.logback.rollingpolicy.max-history | LOGBACK_ROLLINGPOLICY_MAX_HISTORY | Maximum number of archive log files to keep. |

다음은 **파일 출력(File Output)과 파일 회전(File Rotation)을** 적용하여 하루 단위로 파일 출력을 회전하는 예(`application-local.yml`)이다.

```yaml
logging:
  file:
    name: ./log/local
  logback:
    rollingpolicy:
      file-name-pattern: ${LOG_FILE}.%d{yyyy-MM-dd}.%i.log
```

- `file-name-pattern`에서 `%d`와 `%i`는 필수적으로 포함되어야 속성이 적용된다.
- [logback](https://logback.qos.ch/manual/index.html)에 대한 추가적인 설정이 필요하다면 `logback.xml`을 활용하도록 하자. [logback 사용에 대한 설명은 검프님](https://livenow14.tistory.com/64)이 잘 정리해 주셨다.

### 로그 구성 커스텀하기(**Custom Log Configuration)**

다양한 로깅 시스템은 클래스 경로에 적절한 라이브러리를 포함하여 활성화할 수 있으며 클래스 경로의 루트 또는 다음 Spring 환경(`application.properties`) 속성인 `logging.config`에 지정된 위치에 적절한 구성 파일을 제공하여 추가로 사용자 정의할 수 있다.

`org.springframework.boot.logging.LoggingSystem` 시스템 속성을 사용하여 Spring Boot가 특정 로깅 시스템을 사용하도록 할 수 있다. 값은 `LoggingSystem` 구현의 정규화된 클래스 이름이어야 한다. `none` 값을 사용하여 Spring Boot의 로깅 구성을 완전히 비활성화할 수도 있다. **이에 대한 자세한 설명은 [howto.logging](https://docs.spring.io/spring-boot/docs/current/reference/html/howto.html#howto.logging)를 참조하자.**

> 로깅은 `ApplicationContext`가 생성되기 전에 초기화되기 때문에, Spring `@Configuration` 파일의 `@PropertySources`에서 로깅을 제어하는 것이 불가능하다. 로깅 시스템을 변경하거나 비활성화하는 것은 시스템 속성을 통해서만 가능하다.
> 

설정된 로깅 시스템에 맞춰 다음 파일이 로드된다.

| Logging System | Customization |
| --- | --- |
| Logback | logback-spring.xml, logback-spring.groovy, logback.xml, or logback.groovy |
| Log4j2 | log4j2-spring.xml or log4j2.xml |
| JDK (Java Util Logging) | logging.properties |

### 참조

[https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.logging](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.logging)
