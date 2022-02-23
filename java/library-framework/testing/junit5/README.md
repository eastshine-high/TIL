# JUnit5

JUnit은 자바 언어를 위한 단위 테스트 프레임워크다. 이전 버전의 JUnit(하나의 jar 파일 형태)과 달리 JUnit 5는 몇 가지 모듈들로 구성되어 있다.

**JUnit 5 = *JUnit Platform* + *JUnit Jupiter* + *JUnit Vintage***

**JUnit Platform**은 JVM에서 테스트 프레임워크를 시작하기 위한 기반 역할을 한다. 또한 플랫폼에서 실행되는 테스트 프레임워크를 개발하기 위한 TestEngine API를 정의한다. 또한 플랫폼은 명령줄에서 플랫폼을 시작하는 콘솔 런처와 플랫폼에서 하나 이상의 테스트 엔진을 사용하여 맞춤 테스트 제품군(test suite)을 실행하기 위한 [JUnit 플랫폼 제품군 엔진](https://junit.org/junit5/docs/current/user-guide/#junit-platform-suite-engine)을 제공한다. JUnit 플랫폼에 대한 최고 수준의 지원은 인기 있는 IDE(IntelliJ IDEA, Eclipse, NetBeans, Visual Studio Code 참조)와 빌드 도구(Gradle, Maven, Ant 참조)에도 있다.

**JUnit Jupiter**는 JUnit 5에서 테스트 및 확장을 작성하기 위한 새로운 프로그래밍 모델과 확장 모델의 조합이다. Jupiter 하위 프로젝트는 플랫폼에서 Jupiter 기반 테스트를 실행하기 위한 TestEngine을 제공한다.

**JUnit Vintage**는 플랫폼에서 JUnit 3 및 JUnit 4 기반 테스트를 실행하기 위한 TestEngine을 제공한다. 클래스 경로 또는 모듈 경로에 JUnit 4.12 이상이 있어야 한다.

<hr>

### 참조
https://junit.org/junit5/docs/current/user-guide/
