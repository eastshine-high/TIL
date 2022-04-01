## Contents
- 순수한 자바를 이용한 DI
- 스프링을 이용한 DI

# 순수한 자바를 이용한 DI(Dependency Injection)
스프링의 DI를 이해하기 전에, 먼저 순수한 자바를 이용한 DI를 이해해보자.
## 요구사항

내가 만든 `UserDao`가 N 사와 D 사의 사용자 관리를 위해 사용하겠다는 요청이 들어왔다. 문제는 N 사와 D 사가 각기 다른 종류의 DB를 사용하고 있고, DB 커넥션을 가져오는 데 있어 독자적으로 만든 방법을 적용하고 싶다는 점이다. 더욱 큰 문제는 UserDao를 구매한 이후에도 DB 커넥션을 가져오는 방법이 종종 변경될 가능성이 있다는 점이다.

UserDao 소스코드를 N 사와 D 사에 제공해주지 않고도 고객 스스로 원하는 DB 커넥션 생성 방식을 적용해가면서 UserDao를 사용하게 하려면 어떻게 해야할까?

## 인터페이스를 이용한 구현

`ConnectionMaker` 인터페이스를 만들어 각 고객사 개발자가 `ConnectionMaker`를 구현하여 자신들의 DB 연결 기술을 이용해 DB 커넥션을 가져오도록 하면 좋을 것 같다.

```java
pulbic class DConnectionMaker implements ConnectionMaker {
		...
		public Conncection makeConnection() throws ClassNotFoundException, SQLException {
				// D 사의 독자적인 방법으로 Connection을 생성하는 코드
		}
}
```

```java
public class UserDao {
		private ConnectionMaker connectionMaker;

		public UserDao(){
				connectionMaker = new DConnectionMaker();
		}

    public void add(User user) throws ClassNotFoundException, SQLException{
        Connection c = connectionMaker.makeConnection();
        ....
    }
    
    public void get(User user) throws ClassNotFoundException, SQLException{
        Connection c = connectionMaker.makeConnection
        ....
    }
}
```

인터페이스를 이용해 역할과 구현을 분리하여 N사와 D사 각자 독자적인 방법으로 DB 커넥션을 가져올 수 있을 것 같다. 그런데 DB 커넥션을 가져오는 방법이 종종 변경될 때, 매번 소스를 수정해야 한다. 이 코드는 개방-폐쇄 원칙을 위반하고 있다.

## 개방-폐쇄 원칙

로버트 마틴은 확장 가능하고 변화에 유연하게 대응할 수 있는 설계를 만들 수 있는 원칙 중 하나로 개방-폐쇄 원칙(Open-Closed Principle, OCP)을 고안했다[Martin02]. 개방-폐쇄 원칙은 다음과 같은 문장으로 요약할 수 있다.

> **소프트웨어 개체(클래스, 모듈, 함수 등등)는 확장에 대해 열려 있어야 하고, 수정에 대해서는 닫혀 있어야 한다.**
> 

여기서 키워드는 ‘확장'과 ‘수정’이다. **이 둘은 순서대로 애플리케이션의 ‘동작'과 ‘코드'의 관점을 반영**한다.

- 확장에 대해 열려 있다 : 애플리케이션의 요구사항이 변경될 때 이 변경에 맞게 새로운 ‘동작'을 추가해서 애플리케이션의 기능을 확장할 수 있다.
- 수정에 대해 닫혀 있다 : 기존의 ‘코드'를 수정하지 않고도 애플리케이션의 동작을 추가하거나 변경할 수 있다.

개방-폐쇄 원칙은 유연한 설계란 기존의 코드를 수정하지 않고도 애플리케이션의 동작을 확장할 수 있는 설계라고 이야기한다. 처음에는 동작을 확장하는 것과 코드를 수정하지 않는 것이 서로 대립되는 개념으로 보일 수 있을 것이다. 일반적으로 애플리케이션의 동작을 확장하기 위해서는 코드를 수정하지 않는가? 어떻게 코드를 수정하지 않고도 새로운 동작을 추가할 수 있단 말인가?

### 컴파일타임 의존성을 고정시키고 런타임 의존성을 변경하라

**사실 개방-폐쇄 원칙은 런타임 의존성과 컴파일타임 의존성에 관한 이야기다.** 런타임 의존성은 실행 시에 협력에 참여하는 **객체**들 사이의 관계다. 컴파일타임 의존성은 코드에서 드러나는 **클래스**들 사이의 관계다.

그리고 앞 장에서 살펴본 것처럼 유연하고 재사용 가능한 설계에서 런타임 의존성과 컴파일타임 의존성은 서로 다른 구조를 가진다.

### 추상화가 핵심이다

**개방-폐쇄 원칙의 핵심은 추상화에 의존하는 것이다.** 여기서 ‘추상화'와 ‘의존'이라는 두 개념 모두가 중요하다.

**추상화란 핵심적인 부분만 남기도 불필요한 부분은 생략함으로써 복잡성을 극복하는 기법이다**. **추상화 과정을 거치면 문맥이 바뀌더라도 변하지 않는 부분만 남게 되고 문맥에 따라 변하는 부분은 생략된다. 추상화를 사용하면 생략된 부분을 문맥에 적합한 내용으로 채워넣음으로써 각 문맥에 적합하게 기능을 구체화하고 확장할 수 있다.**

개방-폐쇄 원칙의 관점에서 생략되지 않고 남겨지는 부분은 다양한 상황에서의 공통점을 반영한 추상화의 결과물이다. 공통적인 부분은 문맥이 바뀌더라도 변하지 않아야 한다. 다시 말해서 수정할 필요가 없어야 한다. **따라서 추상화 부분은 수정에 대해 닫혀 있다.** **추상화를 통해 생략된 부분은 확장의 여지를 남긴다. 이것이 추상화가 개방-폐쇄 원칙을 가능하게 만드는 이유다.**

여기서 주의할 점은 추상화를 했다고 해서 모든 수정에 대해 설계가 폐쇄되는 것은 아니라는 것이다. 수정에 대해 닫혀 있고 확장에 대해 열려 있는 설계는 공짜로 얻어지지 않는다. 변경에 의한 파급효과를 최대한 피하기 위해서는 변하는 것과 변하지 않는 것이 무엇인지를 이해하고 이를 추상화의 목적으로 삼아야만 한다. 추상화가 수정에 대해 닫혀 있을 수 있는 이유는 변경되지 않을 부분을 신중하게 결정하고 올바른 추상화를 주의 깊게 선택했기 때문이라는 사실을 기억하라.

## 생성과 사용을 분리

UserDao가 오직 ConnectionMaker라는 추상화에만 의존하기 위해서는 UserDao 내부에서 `new DConnectionMaker`같은 구체 클래스의 인스턴스를 생성해서는 안 된다. 위 코드에서 DB 커넥션을 변경할 수 있는 방법은 단 한 가지밖에 없다. 바로 DConnectionMaker의 인스턴스를 생성하는 부분을 NConnectionMaker의 인스턴스를 생성하도록 직접 코드를 수정하는 것 뿐이다. 이것은 동작을 추가하거나 변경하기 위해 기존의 코드를 수정하도록 만들기 때문에 `개방-폐쇄 원칙`을 위반한다.

결합도가 높아질수록 개방-폐쇄 원칙을 따르는 구조를 설계하기가 어려워진다. 알아야 하는 지식이 많으면 결합도도 높아진다. 특히 객체 생성에 대한 지식은 과도한 결합을 초래하는 경향이 있다. 유연하고 재사용 가능한 설계를 원한다면 객체와 관련된 두 가지 책임을 서로 다른 객체로 분리해야 한다. 하나는 객체를 생성하는 것이고, 다른 하나는 객체를 사용하는 것이다.

즉, 객체에 대한 생성과 사용을 분리해야 한다. 생성과 사용을 분리하기 위해 객체 생성에 특화된 객체를 FACTORY라고 부른다. 

```java
public class DaoFactory {

	public UserDao userDao() {
		return new UserDao(connectionMaker());
	}

	public ConnectionMaker connectionMaker() {
		return new DConnectionMaker();
	}
}
```

DaoFactory를 분리했을 때 얻을 수 있는 장점은 매우 다양하다. 그중에서도 애플리케이션의 컴포넌트 역할을 하는 오브젝트와 애플리케이션의 구조를 결정하는 오브젝트를 분리했다는 게 가장 큰 의미가 있다. 후자는 애플리케이션을 구성하는 컴포넌트의 구조와 관계를 정의한 설계도 같은 역할을 한다고 볼 수 있다.

## 의존성 주입

생성과 사용을 분리하면 사용 객체는 오로지 인스턴스를 사용하는 책임만 남게 된다. 이것은 외부의 다른 객체가 어떤 사용 객체에게 생성된 인스턴스를 전달해야 한다는 것을 의미한다. 이처럼 **사용하는 객체가 아닌 외부의 독립적인 객체가 인스턴스를 생성한 후 이를 전달해서 의존성을 해결하는 방법을 `의존성 주입(Dependency Injection)`**[Fowler04]이라고 부른다.

```java
public class UserDao {
		private final ConnectionMaker connectionMaker;

		public UserDao(ConnectionMaker connectionMaker){
				connectionMaker = this.connectionMaker;
		}
}
```

<br>

DaoFactory의 userDao 메소드를 호출하면 DConnectionMaker를 사용해 DB 커넥션을 가져오도록 이미 설정된 UserDao 오브젝트를 도려준다. UserDaoTest는 이제 UserDao가 어떻게 만들어지는지 어떻게 초기화되어 있는지에 신경 쓰지 않고 팩토리로부터 UserDao 오브젝트를 받아다가, 자신의 관심사인 테스트를 위해 활용하기만 하면 그만이다. 이렇게 해서 각각이 자신의 책임에만 충실하도록 역할에 따라 분리하는 작업을 마쳤다.

```java
public class UserDaoTest{
		pulbic static void main(String[] args) throws ClassNotFoundException, SQLException{
				UserDao dao = new DaoFactory().userDao();
		}
}
```

# 스프링을 이용한 DI

드디어 스프링을 사용해볼 때가 됐다. 지금까지 만들었던 것들을 그대로 스프링에서 사용할 수 있다. 스프링은 여러 가지 얼굴을 가졌다. 애플리케이션 개발의 다양한 영역과 기술에 관여한다. 그리고 매우 많은 기능을 제공한다. 하지만 스프링의 핵심을 담당하는 건 바로 빈 팩토리 또는 애플리케이션 컨텍스트라고 불리는 것이다. 이 두 가지는 우리가 만든 DaoFactory가 하는 일을 좀 더 일반화한 것이라고 설명할 수 있다.

## 오브젝트 팩토리를 이용한 스프링 IoC

### 애플리케이션 컨텍스트와 설정정보

이제 DaoFactory를 스프링에서 사용이 가능하도록 변신시켜보자. 스프링에서는 스프링이 제어권을 가지고 직접 만들고 관계를 부여하는 오브젝트를 빈(Bean)이라고 부른다. 자바빈 또는 엔터프라이즈 자바빈(EJB)에서 말하는 빈과 비슷한 오브젝트 단위의 애플리케이션 컴포넌트를 말한다. 동시에 스프링 빈은 스프링 컨테이너가 생성과 관계설정, 사용 등을 제어해주는 제어의 역전이 적용된 오브젝트를 가리키는 말이다.

**스프링에서는 빈의 생성과 관계설정 같은 제어를 담당하는 IoC 오브젝트를 `빈 팩토리(bean factory)`라고 부른다.** 뒤에서 자세히 설명하겠지만 **보통 빈 팩토리보다는 이를 좀 더 확장한 `애플리케이션 컨텍스트(application context)`를 주로 사용한다.** 애플리케이션 컨텍스트는 IoC 방식을 따라 만들어진 일종의 빈 팩토리라고 생각하면 된다. 앞으로 빈 팩토리와 애플리케이션 컨텍스트라는 용어를 함께 사용할 텐데, 두 가지가 동일하다고 생각하면 된다. **빈 팩토리라고 말할 때는 빈을 생성하고 관계를 설정하는 IoC의 기본 기능에 초점을 맞춘 것**이고, **애플리케이션 컨텍스트라고 말할 때는 애플리케이션 전반에 걸쳐 모든 구성요소의 제어 작업을 담당하는 IoC 엔진이라는 의미**가 좀 더 부각된다고 보면 된다.

**애플리케이션 컨텍스트는 별도의 정보를 참고해서 빈(오브젝트)의 생성, 관계설정 등의 제어 작업을 총괄한다.** 기존 DaoFactory 코드에는 설정정보, 예를 들어 어떤 클래스의 오브젝트를 생성하고 어디에서 사용하도록 연결해줄 것인가 등에 관한 정보가 평범한 자바 코드로 만들어져 있다. **애플리케이션 컨텍스트는 직접 이런 정보를 담고 있진 않다. 대신 변도로 설정정보를 담고 있는 무엇인가를 가져와 이를 활용하는 범용적인 IoC 엔진 같은 것**이라고 볼 수 있다.

빈 팩토리 또는 애플리케이션 컨텍스트가 사용하는 설정정보를 만드는 방법은 여러가지가 있는데, 우리가 앞에서 만든 오브젝트 팩토리인 DaoFactory도 조금만 손을 보면 설정정보로 사용할 수 있다. 앞에서는 DaoFactory 자체가 설정정보까지 담고 있는 IoC 엔진이었는데 여기서는 자바 코드로 만든 애플리케이션 컨텍스트의 설정정보로 활용될 것이다. 그림 1-8에서 애플리케이션의 로직을 담고 있는 컴포넌트와 설계도 역할을 하는 팩토리를 구분했었다. 바로 이 설계도라는 게 바로 이런 애플리케이션 컨텍스트와 그 설정정보를 말한다고 보면 된다. 그 자체로는 애플리케이션 로직을 담당하지는 않지만 IoC 방식을 이용해 애플리케이션 컴포넌트를 생성하고, 사용할 관계를 맺어주는 등의 책임을 담당하는 것이다. 마치 건물이 설계도면을 따라서 만든어지듯이, 애플리케이션도 애플리케이션 컨텍스트와 그 설정정보를 따라서 만들어지고 구성된다고 생각할 수 있다.

### DaoFactory를 사용하는 애플리케이션 컨텍스트

DaoFactory를 스프링의 빈 팩토리가 사용할 수 있는 본격적인 설정정보로 만들어보자. 먼저 스프링이 빈 팩토리를 위한 오브젝트 설정을 담당하는 클래스라고 인식할 수 있도록 `@Configuration`이라는 어노테이션을 추가한다. 그리고 오브젝트를 만들어 주는 메소드에는 `@Bean`이라는 어노테이션을 붙여준다. userDao() 메소드는 UserDao타입 오브젝트를 생성하고 초기화해서 돌려주는 것이니 당연히 `@Bean`이 붙는다. 또한 ConnectionMaker 타입의 오브젝트를 생성해주는 connectionMaker() 메소드에도 `@Bean`을 붙여준다. 그 외에는 수정할 코드가 없다. 이 두 가지 어노테이션만으로 스프링 프레임워크의 빈 팩토리 또는 애플리케이션 컨텍스트가 IoC 방식의 기능을 제공할 때 사용할 완벽한 설정정보가 된 것이다.

아래 코드는 이 두 가지 어노테이션이 적용된 `DaoFactory` 클래스다. 자바 코드의 탈을 쓰고 있지만, 사실을 XML과 같은 스프링 전용 설정정보라고 보는 것이 좋다.

```java
import org.springframework.context.annotiation.Bean;
import org.springframework.context.annotiation.Configuration;

@Configuration
public class DaoFactory {
	@Bean
	public UserDao userDao() {
		return new UserDao(connectionMaker());
	}

	@Bean
	public ConnectionMaker connectionMaker() {
		return new DConnectionMaker();
	}
}
```

이제 DaoFactory를 설정정보로 사용하는 애플리케이션 컨텍스트를 만들어보자. 애플리케이션 컨텍스트는 `ApplicationContext` 타입의 오브젝트다. ApplicationContext를 구현한 클래스는 여러 가지가 있는데 DaoFactory처럼 `@Configuration`이 붙은 자바 코드를 설정정보로 사용하려면 AnnotationConfigApplicationContext를 이용하면 된다. 애플리케이션 컨텍스트를 만들 때 생성자 파라미터로 DaoFacotry 클래스를 넣어준다. 이제 이렇게 준비된 ApplicationContext의 getBean()이라는 메소드를 이용해 UserDao의 오브젝트를 가져올 수 있다. 리스트 19는 애플리케이션 컨텍스트를 적용하도록 수정한 UserDaoTest 코드다.

```java
public class UserDaoTest{
	public static void main(String[] args) throws ClassNotFoundException,
			SQLException {
		ApplicationContext context = 
			new AnnotationConfigApplicationContext(DaoFactory.class);
		UserDao dao = context.getBean("userDao", UserDao.class);
		...
}
```

getBean() 메소드는 ApplicationContext가 관리하는 오브젝트를 요청하는 메소드다. getBean()의 파라미터인 “userDao”는 ApplicationContext에 등록된 빈의 이름이다. **DaoFactory에서 `@Bean`이라는 어노테이션을 userDao라는 이름의 메소드에 붙였는데, 이 메소드 이름이 바로 빈의 이름이 된다.** userDao라는 이름의 빈을 가져온다는 것은 DaoFactory의 userDao() 메소드를 호출해서 그 결과를 가져온다고 생각하면 된다. 메소드의 이름을 myPreciousUserDao()라고 했다면 getBean(”myPreciousUserDao”, UserDao.class)로 가져올 수 있다. 그런데 UserDao를 가져오는 메소드는 하나뿐인데 왜 굳이 이름을 사용할까? 그것은 UserDao를 생성하는 방식이나 구성을 다르게 가져가는 메소드를 추가할 수 있기 때문이다. 그때는 specialUserDao()라는 메소드로 만들고 getBean(”specialUserDao”, UserDao.class)로 가져오면 된다.

**getBean()은 기본적으로 Object 타입으로 리턴**하게 되어 있어서 매번 리턴되는 오브젝트에 다시 캐스팅을 해줘야 하는 부담이 있다. **getBean()의 두 번째 파라미터에 리턴 타입을 주면, 자바 5 이상의 제네릭(Generic) 메소드 방식을 사용해 지저분한 캐스팅 코드를 사용하지 않아도 된다.**

## 참조

<이일민, 토비의 스프링 3.1 vol. 1, 에이콘>

<조영호, 오브젝트, 위키북스>
