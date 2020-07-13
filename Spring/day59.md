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

- 싱글톤 패턴(Singleton Pattern)

  - static 메소드와 private 생성자를 만들어서 단하나의 인스턴스만 연동할 수 있도록 구현하는 패턴 

  - 자바에서 카렌다 클래스, 그래픽 클래스등이 이에 속함 

    ```java
    class Test{
      private Test(){}
      public static Test getInc(){return new Test();}
    } => Test t1 = Test.getInc(); -> 싱글톤 
    ```

    