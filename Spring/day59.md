# DAY59

## Spring?

- 오픈 소스 프레임워크
- 엔터프라이즈 어플리케이션 개발의 복잡성을 줄여주기 위한 목적
- EJB 사용으로 수행되었던 모든 기능을 일반 POJO(Plain Old Java Object)를 사용해서 가능하게 함
- 경량 컨테이너

> POJO
>
> - Plain Old Java Object의 약자
> - Plain의 뜻 : component interface를 상속받지 않는 특징(특정 프레임워크에 종속되지 않는)
> - Old : EJB 이전의 자바 클래스를 의미함 



### 주요 개념

- 의존성 주입
- 관점 지향 컨테이너 



### 장점

- 경량 컨테이너 
  - 객체의 라이프 사이클 관리
  - J2EE 구현을 위한 다양한 API 제공
- DI(Dependency Injection) 지원 
- AOP(Aspect Oriented Programming) 지원 
- POJO(Plain Old Java Object) 지원
- JDBC를 위한 다양한 API 지원
- Transaction 처리를 위한 일관된 방법을 제공
- 다양한 API와의 연동 지원 



### 취지 

- 기술 자체보다 좋은 설계가 중요함
- 인터페이스를 통한 느슨한 결합은 자바빈의 훌륭한 모델임
- 코드는 테스트하기 쉽고 편해야함
- EJB의 복잡도에서 벗어나야 함 

> 느슨한 결합?
>
> - 두 객체가 느슨하게 결합되어 있다는 것
> - 두 객체가 상호작용을하긴 하지만, 서로에 대하 잘모른다는 뜻
> - 느슨하게 결합되는 디자인을 사용하면 변경사항이 생겨도 무난하게 처리가능
> - 유연한 객체지향 시스템 구축 가능



### 의존성 주입(Dependency Injection, DI)

- 의존성 관계 주입(dependency injection)
- 제어의 역행(inversion of control, IoC) 이라는 의미로 사용됨 



- 결합도가 높음 

```java
class UserServiceImpl{
  UserDaoJDBCImpl dao;
  public void register(User user){}
  public User findUser(String name){
    dao = new UserDaoJDBCImpl();
    User r = dao.select(name);
    return r;
  }
}

class UserDaoJDBCImpl{
  public User select(String name){
    //이름을 받아서 select * from user where name=?
  }
}--> User res = new UserServiceImpl().findUser("홍길동");
```



### 스프링 프레임워크의 DI 방법

- IoC(Inversion of Control)
  - 제어 역행화는 말 그대로 제어가 일반적인 흐름을 따르지 않고 반대로 흘러간다는 뜻
  - 각각의 프로그램이 가지고 있던 구현 객체에 대한 정보가 이젠 프레임워크에서 관리된는 것임
  - 현재는 DI라는 용어를 더 많이 사용
- 의존성
  - 보통 비즈니스 로직을 수행하기 위해 둘 이상의 클래스를 사용
  - 전통적으로 각 객체는 협업할 때 객체의 참조를 취득해야 하는 책임이 있음 -> 이것이 의존성
  - 객체 간의 결합도가 높으면 테스트하기 어려운 코드를 만들어냄 





### 스프링 프레임워크 컨테이너 종류

- BeanFactory
  - org.springframework.beans.factory.BeanFactory
  - 기본적인 의존성 주입을 지원하는 가장 간단한 형태의 컨테이너
  - AOP를 지원하지 않음
- ApplicationContext
  - org.springframework.context.ApplicationContext
  - 프로퍼티 파일의 메시지 해석, 이벤트 발행들의 서비스 제공
- WebApplicationContext
  - rg.springframework.web.context.WebApplicationContext
  - 웹 어플리케이션을 위한 ApplicationContext
- BeanFactory 와 ApplicationContext 차이 
- Lazy Loading 이란?



#### BeanFactory

- 가장 단순한 컨테이너

- 다양한 유형의 빈을 생성하고 분배하는 책임을 짐 

- 클래스를 객체화할 때 협업하는 객체간의 연관관계를 형성함 

- Resource의 구현 클래스

  - FileSystemResource(파일로부터)
  - InputStreamResource(스트림으로부터)
  - ClassPathResource(클래스패스로부터)
  - UrlResource(url로부터)
  - ServletContextResource(ServletContext로 부터)
  - PortletContextResource(포틀릿으로부터)

  > 포틀릿이란?
  >
  > 
  >
  > 포탈이란?



#### ApplicationContext

- BeanFactory 보다 좀더 진보된 컨테이너(BeanFactory 인터페이스를 확장)
- 빈을 로딩하고 빈들을 와이어링(Wiring)하며, 요청에 따라 빈을 분배
- 국제회 지원을 위해 텍스트 메세지를 해석
- 이미지등의 자원을 로딩하는 범용적인 방법을 제공
- 리너스로 등록되어 있는 빈에게 이벤트를 발행할 수 있음
- ApplicationContext 구현 클래스
  - ClassPathXmlApplicationContext : 클래스 경로에 있는 xml 파일로 부터 context 정의를 로딩하면, context 정의를 클래스 경로에 있는 자원으로 취급
  - FileSystemXmlApplicationContext : 파일 시스템에 잇는 xml파일로 부터 context정의를 로딩함
  - XmlWebApplicationContext : 웹 애플리케이션에 포함되어 있는 xml파일로 부터 context정의를 로딩함 



## SpringDI

- sample1 

  - DI 개념을 이해함
  - is ~ a 관계를 이용한 구도를 학습
- sample2

  - has ~ a 관계를 이용해 DI를 이용할 수 있음
- sample3

  - setter, constructor injection의 이해도를 구현
- sample4 

  - 추상클래스의 패턴과 싱글톤 패턴을 이해하고 구현
- sample5

  - setter injection으로 의존성 주입
- sample6

  - 다형성을 이용하여 생성자로 is~a(상속) 관계의 객체 값을 이용하여 의존성 주입
- sample7

  - xml 설정파일을 import하고 나열하고 호출한 결과를 확인
- sample8, sample9

  - 상속관계가 아닌 단일 클래스값을 이용하여 의존성 주입
- sample10 
  - 다형성 객체를 선언하고 자바코드로 빈팩토리를 생성해서 각각의 동적 바인딩을 구현
- sample11
  - lazy-init 사용, depends-on = "참조되는 객체의 id"(sample2/applicationContext2.xml)
  - 빈팩토리를 이용해서 스프링에서 설정을 하고 난 후 객체를 id로 getBean()으로 호출을 하게 되면 메모리에 올려진 객체들이 선언된 순서대로 생성된 것을 확인 가능
  - 기본적으로 스프링 컨테이너가 시작할 때 싱글톤에 의해 bean에 대해 모든 인스턴스화 한다
  - 만약 특정 싱글톤유형에 대한 bean을 컨테이너가 시작할 때 인스턴스화 시키지 않고 해당 bean을 사용하고자 하는 시점에 인스턴스화 하고 싶을 때 사용하는 속성 키워드가 **lazy-init="true"**
  - depends-on
    - bean이 초기화 되기 전에 먼저 초기화 되는 키워드 
    - 특정 bean이 초기화 되기 전에 초기화 되어야 하는 bean을 명시적으로 선언 
    - 생성자와 setter 메소드로 값을 전달하기 이전에 메모리에 생성되는 객체가 정의 되어 메모리 체크를 할 때 많이 사용되는 키워드 
    - 미리 서버를 구동 시켜놓고 특정 리소스에 대한 작업을 체크할 때 사용됨 
    - [NoSuchBeanDefinitionException] : No bean named 'myaddr03' available
  - getBean -> lazy-init
  - 참조되는 객체를 호출하기 전에 메모리에 올림 -> depends-on
- sample12
  - java.util의 컬렉션[list, set, map...] 값을 inject할 수 있음 
  - [7.4.2](https://docs.spring.io/spring/docs/4.3.27.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-properties-detailed)
- sample13
  - abstract = "true" 일반 클래스를 추상 클래스로 선언하는 기능
  - merge = "true" collections 병합하는 키워드 
- sample14
  - message -> MessageSource를 이용해서 속성값을 찾아옴 
  - 번들값, 속성값등을 참조할 때 사용
  - [*MessageSource*](https://docs.spring.io/spring/docs/4.3.27.RELEASE/javadoc-api/org/springframework/context/MessageSource.html) : getMessage()
  - [*MessageSourceAware*](https://docs.spring.io/spring/docs/4.3.27.RELEASE/javadoc-api/org/springframework/context/MessageSourceAware.html) : setMessageSource(MessageSource messageSource)
  - 키, 값을 가진 파일 생성 -> ResourceBundleMessageSource 로 파일을 읽어온다 -> MessageSourceAware를 상속받은 클래스는 setMessageSource()로 메세지를 관리한다 -> MessageSource의 getMessage()로 키를 지정해서 값을 리턴받는다
- sample15 : autowire 속성
  - byName 
    - id -> favoriteFood setFavoriteFood 자동 호출 
    - 참조변수의 이름과 같은 해당객체의 메서드가 있을 때, 자동 할당
  - byType 
    - 참조 변수의 Type과 같은 메서드의 param이 선언되어 있으면 자동할당
    - setFavoriteFood(Food f)
  - contructor
    - byType과 같은 내용을 생성자를 통해서 같은 type이 있으면 할당
  - autodetect
    - 생성자에 할당할 type이 있는지를 먼저 확인한 후, 없으면 메서드에서 type이 있는 지를 확인하여 할당 
    - 실제 class에서 @autowired라는 옵션으로 많이 쓰이고 있는 내용 
- sample16 : **SPRING **(spring 2.5 / spring 3.0 [표준] / spring 4.0) + **DB** 
  - spring + jdbc [java] java.sql
  - spring + jdbc [datasource] javax.sql
  - spring ver 4.0
    - springJDBC : org.springframework.jdbc.datasource.SimpleDriverDataSource
  - spring + ORM(Object-Relational Mapping) [Hibernate, ibatis(ver2.0, ver3.0), mybatis(ver 5.0)]
    - .xml로 쿼리를 작성한 파일을 객체로 맵핑 
  - spring ver 5.0 + JSR
- sample17 
  - spring + jdbc [datasource] javax.sql을 기준으로 db를 세팅하는데 환경설정파일 [beans]을 .java로 연동하는 방법 



> 싱글톤 패턴(Singleton Pattern)
>

- static 메소드와 private 생성자를 만들어서 단하나의 인스턴스만 연동할 수 있도록 구현하는 패턴 

- 자바에서 카렌다 클래스, 그래픽 클래스등이 이에 속함 

  ```java
  class Test{
    private Test(){}
    public static Test getInc(){return new Test();}
  } => Test t1 = Test.getInc(); -> 싱글톤 
  ```

  

### sample12 예제

> FooTestApp.java

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class FooTestApp {
	public static void main(String[] args) {
		ApplicationContext factory = new ClassPathXmlApplicationContext("sample12/applicationContext.xml");
		Foo foo = factory.getBean("foo", Foo.class);
		System.out.println("******* start ********");
		foo.fooMethod();
	}
}

```

> 실행결과

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ggqaza7prcj31660u0n82.jpg" alt="image-20200714114227817" style="zoom:50%;" /> 

- applicationContext.xml에서 생성한 객체가 메인에서 호출되지 않았음에도 객체가 생성되어 메모리에 올라감





> Foo.java

```java
import java.util.Date;
import java.util.List;
import java.util.Map;
import java.util.Set;

public class Foo {
	private Bar bar;
	
	public Foo() {
	}
	
	public Foo(Date date) {
		System.out.println("[ 생성자 "+date+" ]");
	}
	
	public void setBar(Bar bar) {
		this.bar = bar;
		System.out.println("[ setBar 호출 ]");
	}

	public void setDate(Date date) {
		System.out.println("[ setDate() 메서드 호출 "+date+" ]");
	}

	public void setNumber(int number) {
		System.out.println("[ setNumber() 메서드 호출 "+number+" ]");
	}

	public void setInfo(List<String> info) {
		System.out.println("[ setInfo() 메서드 호출 ]");
		for(String i : info) {
			System.out.println(i);
		}
	}

	public void setStudents(String[] students) {
		System.out.println("[ setStudents() 메서드 호출 ]");
		for(String s : students) {
			System.out.println("[ 학생 : "+s+" ]");
		}
	}

	public void setEvent(Set<String> event) {
		System.out.println("[ setEvent() 메서드 호출 ]");
		for(String e : event) {
			System.out.println("[ 이벤트 응모 : "+e+" ]");
		}
	}

	public void setTelNumber(Map<String, String> telNumber) {
		System.out.println("[ setTelNumber() 메서드 호출 ]");
		for(String tel : telNumber.keySet()) {
			System.out.println("[ "+tel+" 의 전화번호 : "+telNumber.get(tel)+" ]");
		}
	}

	public void fooMethod() {
		System.out.println("[ Foo 클래스의 fooMethod() 메서드... ]");
		bar.pr();
	}
}

```

> Bar.java

```java
public class Bar {
	public Bar() {
		System.out.println("[ Bar 객체생성 ]");
	}
	public void pr() {
		System.out.println("[ Bar 클래스의 pr() 메서드... ]");
	}
}

```

> applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:c="http://www.springframework.org/schema/c"
	xmlns:p="http://www.springframework.org/schema/p"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd">
	<bean class="sample12.Bar" id="bar"/>
	<bean class="java.util.Date" id="date"/>
	<bean class="sample12.Foo" id="foo" p:bar-ref="bar" c:date-ref="date"
	p:number="25">
	<property name="info">
		<list>
			<value>영어</value>
			<value>수학</value>
			<value>국어</value>
		</list>
	</property>
	<property name="students">
		<list>
			<value>강민</value>
			<value>다훈</value>
			<value>다빈</value>
		</list>
	</property>
	<property name="event">
		<set>
			<value>둘리</value>
			<value>고길동</value>
			<value>또치</value>
			<value>고길동</value>
		</set>
	</property>
	<!-- setTelNumber(Map<String,String>) -->
	<property name="telNumber">
		<map>
			<entry key="1" value="현이"/>
			<entry key="2" value="종철이"/>
			<entry key="3" value="수영이"/>
			<entry key="4" value="정연이"/>
		</map>
	
	</property>
	</bean>
	
</beans>

```

