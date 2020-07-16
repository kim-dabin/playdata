# DAY62

## 학습목표

- Spring AOP



## Spring AOP

### sample01 : 자바 코드 작성을 위한 처리 흐름

### sample02 : sample01 을 aop 로 적용

- AOP(Aspect Oriented Programmin) 란?
  - OOP 의 문제를 개선하기 위해 적용된것
    - Core concerns : 해당 시스템의 핵심 가치와 고유의 목적이 그대로 드러난 관심 영역
    - Crosscutting Concerns : 로깅, 보안, 트랜잭션 관리 등과 같이 여러 모듈간의 공통적으로 적용되는 공통 관심 영역
       	
  - AOP 의 장점 : 기존의 코드를 수정하지 않고도 필요한 공통 기능 모듈 적용 가능
  - AOP 의 툴 4가지
    - 자바 언어를 확장한 언어를 사용하는 툴 : AspectJ
      제록스 연구소 개발 -> Eclipse 에 기증 -> 현재 IBM 에서 지원을 받아 개발중
       			(Aspect, Pointcut, Advice 를 만들 수 있다)
    - 기존의 자바 언어만으로 AOP 구현 가능 : AspectWerkZ
    - Eclipse 프로젝트 (PointCut, Advice) 에서 xml 로 정의할 수 있다.
    - 대표적인 인터셉터 체인 방식의 구현 AOP 방식 : SpringAOP , JBossAOP
         - 최근에 등장한 프레임워크, 어노테이션으로 정의할 수 있다.
         - SpringDI 패턴에서 동작하는 인터페이스이다.
         - 기존의 클래스에서 byte 코드를 수정하지 않고 JDK 의 xml 에서 지정한 
              bean 의 객체를 통해 제어한다.

> Advisor = AOP advice ( action to take at a joinpoint) + advice ( such as a pointcut);
>
> PaointCut = A pointcut is composed of a ClassFilter and a MethodMatcher
>
> 각각의 역할에 맞게 매핑해줌 (핵심코드 알려줌)

- [Aop 의 주요 구성 요소 [sec.11]](https://docs.spring.io/spring/docs/4.3.27.RELEASE/spring-framework-reference/htmlsingle/#aop-introduction-defn)

  - joinpoint : 프로그램 의 제어흐름 중 한 시점을 의미한다.

    - (메소드 호출 시점, 메소드 호출 결과가 리턴되는 시점, 예외 던져지는 시점)
    - 각각의 joinpoint 들은 전후로 Crosscutting Concerns 의 기능이 AOP 에 의해 자동으로 추가되어 동작되는 후보 지점이다.

  - pointcut : AOP 를 선별하는것

    - value = "execution(public * * (..)) " -> pointcut 을 정의한것
    - AOP 의 전체 코드에서 실행되어지는 메소드 유형을 정의한다

  - 기본 표현식 -	set* (..) : 메소드 명이 set 으로 시작하고 0개 이상의 매개인자를 가진 메소드
    			*main(..) : 메소드 명이 main 이고
    			return type 이 any type(모든타입) 이고 0개 이상의 매개인자를 가진 메소드
    		
    타입 매핑 형식 - java.io.*;
    	org.com.test..* 
    				 org.com.test 내의 서브 패키지에 속한 모든 하위요소
    	Number +
    			 	Number 또는 Number 서브타입의 모든 타입
    			 	

    	!(Number + ) 
    				Number 또는 Number 서브타입의 모든 타입이 아닌것
    	int || Integer 
    				int 또는 Integer 형
    	
    	org.com.test .. * && !Serializable +
    			-> org.com.tes 패키지 또는 하위 서브 패키지 내에 존재하면서
    				Serializable 의 타입이 아닌 모든 요소
    	
    	접근 지정에 따른 형식
    			-public static void main(..)
    			-!private * *  (..) 
    				리턴타입이 모든 타입이고, 0개이상의 매개인자를 가진 메소드중 접근제한자가 private 가 아닌 메소드
    			-* main(..) 
    				접근 지정자 명시하지 않으면 public 접근 제한자
    			-* main(*, ..) 
    				리턴 타입이 모든 타입이고 최소 1개의 모든 타입을 가진 메소드
    			-* main(*, .. , String , *)
    				리턴타입이 모든 타입이고 최소 3개이상의 매개인자를 가지며
    				끝에서 두번째는 반드시 String 타입이여야 한다.
    	생성자 형식
    			new ( .. ) 
    					0개 이상의 모든 타입을 가진 생성자
    			Account.new ( .. ) 
    					0개 이상의 모든 타입을 가진 Acocunt 클래스의 생성자 
    					
    			execution 또는 call 
    					특정 메소드나 생성자 실행시점을 정의한다.
    			
    			execution( * main(..) )
    			call (* main(..))
    			get (Collection+) - 멤버변수에 선언
    			set (Collection+) - 멤버변수에 선언
    			
    	Exception 처리 
    		handler(RuntimeException + )
    			RuntimeException 하위에 있는 Exception 을 모두 catch 하는 시점
    	
    	whitin 처리
    		특정 유형의 joinpoint 를 정의하는 시점
    	whitincode 
    		특정 메소드 또는 생성자 내에 정의된 코드를 정의하는 시점
    	this 
    		해당 joinPoint 를 정의하는 시점
    	target
    		대상 객체의 타입을 정의하는 시점
    		ex) call(* * (..)) && target(MyTest)
    		MyTest 라는 클래스 내의 모든 메소드를 호출

    3. advice (@after, @before~~)
    		각 joinoint에 삽입 되어 동작할 수 있는 코드
    	
    4. Weaving (내부적으로 일어남)
    		Core concerns 랑 Crosscuttion Concerns 를 엮어서 
    		동작하도록 수행한다는 의미 
    		target 객체에 aspect 를 적용해서 새로운 프록시 개체를 생성하는 절차
    5. aspect 
    		다수의 클래스에서 공통적으로 구현하는 것으로 모듈화를 말한다 
    		어디서 (pointcut) 무엇을 할지 (Advice) 를 합쳐놓은것
    		aspect = pointcut + advice
    	*advice 는 각 joinpoint 에 삽입되어서 동작할 수 있는 코드
    	
    6. 프록시 
    	어드바이스를 target 객체에 적용하면 생성되는 객체				

### sample03 

- @AspectJ, SpringAOP
- [참고](https://www.eclipse.org/aspectj/doc/released/runtime-api/index.html)
  			

### sample05

- 순수 XML 기반의 설정 파일

  - 개발 시점에 편집기에서 xml과 java(POJO:Plain Old JAva Object 방식) 파일을 오가면서 작성

  - 유지 보수 시점에서 xml 파일만 보면 Bean들 사이의 관계를 쉽게 알수 있음

    

### sample06

- Annotation 기반의 XML 설정 파일 
  - 일부 java파일은 스프링프레임워크에 의존
  - 개발시점에 xml파일을 한번 설정 한 후에는 다시 볼 일X
  - 유지 보수 시점에 xml 파일만 봐서는 Bean들 사이의 관계를 알 수 없음
  - 모든 java파일을 열어서 확인해봐야 정확한 구조를 파악 가능(Annotation 확인)



### sample07

#### 예제

- 0으로 나눌 때 Exception 발생

- 소스 

  ```java
  package sample07;
  
  public class CoreEx {
  	public void zeroMethod(int a, int b) {
  		try {
  			System.out.println(a+"/"+b+" = "+(a/b));
  		} catch (Exception e) {
  			throw new RuntimeException("0으로 나눌 수 없습니다.");
  		}
  	}
  }
  
  ```

  

  ````java
  package sample07;
  
  import org.aspectj.lang.ProceedingJoinPoint;
  import org.aspectj.lang.annotation.After;
  import org.aspectj.lang.annotation.AfterReturning;
  import org.aspectj.lang.annotation.AfterThrowing;
  import org.aspectj.lang.annotation.Around;
  import org.aspectj.lang.annotation.Aspect;
  import org.aspectj.lang.annotation.Before;
  
  @Aspect
  public class AdviceEx {
  	@Before("execution(* zeroMethod(..))")
  	public void before() { 
  		System.out.println("JoinPoint 앞에서 실행되는 Advice");
  	}
  	
  	@After("execution(* zeroMethod(..))")
  	public void after() {
  		System.out.println("JoinPoint 뒤에서 실행되는 Advice");
  	}
  
  	@AfterThrowing(pointcut = "execution(* zeroMethod(..))", throwing = "e")
  	public void afterThrowing(Throwable e) {
  		System.out.println("예외가 실행될 때 호출되는 Advice");
  		System.out.println(e.getMessage());
  	}
  	
  	@AfterReturning(pointcut = "execution(* zeroMethod(..))", returning = "o")
  	public void afterReturing(Object o) {
  		System.out.println("JoinPoint가 정상 종료 후 실행되는 Advice");
  	}
  
  	@Around("execution(* zeroMethod(..))")
  	public Object around(ProceedingJoinPoint jp) throws Throwable {
  		System.out.println("JoinPoint 전역에서 실행되는 Advice : "+ jp.getTarget());
  		return jp.proceed(); // 실행할 메서드(zeroMethod)를 묶음 
  	}
  	
  }
  
  ````

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
  	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  	xmlns:aop="http://www.springframework.org/schema/aop"
  	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
  		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd">
  
  	<aop:aspectj-autoproxy />
  	<!-- 타겟(pointcut) -->
  	<bean name="core" class="sample07.CoreEx" />
  	<!-- Advice -->
  	<bean name="advice" class="sample07.AdviceEx"/>
  </beans>
  
  ```

  

- 예외 발생O

```java
package sample07;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Mtest {
	public static void main(String[] args) {
		ApplicationContext context = new ClassPathXmlApplicationContext("sample07/beans.xml");
		CoreEx core = context.getBean("core", CoreEx.class);
		core.zeroMethod(1,0);
	}
}
```

![image-20200716142208708](https://tva1.sinaimg.cn/large/007S8ZIlgy1ggsqu22vk7j316w08gtci.jpg)



- 예외 발생X

```java
package sample07;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Mtest {
	public static void main(String[] args) {
		ApplicationContext context = new ClassPathXmlApplicationContext("sample07/beans.xml");
		CoreEx core = context.getBean("core", CoreEx.class);
		core.zeroMethod(10,2);
	}
}

```

![image-20200716142836410](https://tva1.sinaimg.cn/large/007S8ZIlgy1ggsr0pslsvj312c07u40m.jpg)





### sample10

- proxy 

  - 타겟을 감싸서 요청을 대신 받아주는 랩핑 클래스

  - 클라이언트가 실제로 사용하려는 대상인 것처럼 위장해서 클라이언트의 요청을 받아, 보조 업무를 처리하는 대리자

  - Spring에서는 Proxy를 이용해 객체지향의 5대원칙 중 하나인 OCP(Open-Close Principal : 개방폐쇄의 원칙)을 적용

    > OCP (Open-Close Principal : 개방 폐쇄의 원칙)
    >
    > - 개방-폐쇄 원칙(OCP, Open-Closed Principle)은 '소프트웨어 개체(클래스, 모듈, 함수 등등)는 확장에 대해 열려 있어야 하고, 수정에 대해서는 닫혀 있어야 한다.'는 프로그래밍 원칙
    >
    > - 클라이언트의 권한 : Read-Only

- ThrowsAdvice : 예외가 발생했을 때 
  - try{...}catch{} 를 따로 구현하지 않고 
  - org.springframework.aop.ThrowsAdvice 구현체에서 처리함 
- org.springframework.aop.framework.ProxyFactoryBean[AOP의 bean자체를 말함]
  - proxyInterfaces : 문자열 인터페이스 이름의 배열
  - interceptorNames : 적용할 Advisor나 인터셉터, 다른 어드바이스 이름의 문자열 배열
  - singleton: getObject() 메서드를 얼마나 많이 호출했는지 상관 없이 팩토리가 하나의 객체를 반환해야 하는지를 결정



#### 소스



```java
package sample10;

import java.lang.reflect.Method;

import org.springframework.aop.ThrowsAdvice;

public class MyThrowsAdvice implements ThrowsAdvice {
	//4번째 선언된 메소드 형식 
	public void afterThrowing(Method method, Object[] args, Object target, RuntimeException ex) {
		System.out.println(":::오류 발생 객체 : "+target.getClass().getName());
		System.out.println(":::오류 발생 메서드 : "+method.getName());
		System.out.println(":::오류 발생 메시지 : "+ex.getMessage());
		System.exit(0);
	}
	
}
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ggstez20xbj30rs062ta9.jpg" alt="image-20200716155130416" style="zoom:50%;" /> 

```java
package sample10;

import java.lang.reflect.Method;

import org.springframework.aop.ThrowsAdvice;

public class MyThrowsAdvice implements ThrowsAdvice {
	//4번째 선언된 메소드 형식 
	public void afterThrowing(Method method, Object[] args, Object target, RuntimeException ex) {
		System.out.println(":::오류 발생 객체 : "+target.getClass().getName());
		System.out.println(":::오류 발생 메서드 : "+method.getName());
		System.out.println(":::오류 발생 메시지 : "+ex.getMessage());
		System.exit(0);
	}
	/* 원래 Exception */
	public void afterThrowing(ArithmeticException ex) { //Exception > RuntimeException > ArithmeticException
		System.out.println("===========> "+ex);
		System.exit(0);
	}
	
}
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ggstdxjujij30rs04kt9u.jpg" alt="image-20200716155030696" style="zoom:50%;" /> 

