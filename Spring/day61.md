# DAY61

## 학습목표

1. 스프링 어노테이션

   

## SpringDI02

1. com.test01

### com.test04 

- @Component("userService") 

  ```xml
  <bean id="userService" class="UserService"></bean>
  ```

  

- @Resource : 의존하는 빈 객체를 전달할 때 사용함 

  ```java
  @Resource(name="myuser")
  private MyUser mytest;
  /*MyUser myuser = new MyUser(); mytest = myuser; 와 같은 의미*/
  ```

- Stereotype-annotation

  - @Component : 자동인식이 되는 일반 컴포넌트 <bean/>
  - @Service : 비즈니스 서비스
  - @Repository : DAO(DBException 자동처리)
  - @Controller : MVC 컴포넌트

### com.test05

- new AnnotationConfigApplicationContext(); 을 사용해서 @Configuration을 선언 후 호출

- 특정 bean을 호출할 때 사용하는 어노테이션

- @Scope("prototype | singleton") : nonstatic | static

  - prototype : 하나의 bean 정의에 대해서 다수의 객체가 존재할 수 있음

  <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ggrgwcyjayj30f806qab3.jpg" alt="image-20200715115252527" style="zoom:50%;" /> 

  - singleton(기본) : 하나의 Bean 정의에 대해서 Spring IoC Container 내에 단 하나의 객체만 존재

  <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ggrgx6q562j30f806q75b.jpg" alt="image-20200715115339382" style="zoom:50%;" /> 

- @Inject(JSR -330)

  - type을 움직임 
  - 자바에 있는 거라 종속 X
  - @Named를 이용해서 특정 bean을 사용 
  - 참조 하고자 하는 bean을 직접 injection 하지 않고 클래스 타입에 Object<T> 대한 injection을 함 
  - 객체 값을 리턴하는 get() 메소드에서 사용됨(JSR330)

  ```java
  class Test{
    @Inject
    private Mydate<Address> mydate;
    public void setMydate(Mydate<Address> d){
      this.mydate = d;
    }
    public Address getMydate(){
      /*
      	Mydate<Address> m = {Address(0),...}
  			m.get(0); m.get() 전체를 리턴하는 구조를 가진 메소드     
      */
      return mydate.get();
    } 	
  }
  ```

  > DefaultListableBeanFactory의 DependencyProvider라는 클래스의 구현체만 사용할 수 잇음

- @Autowired 

  - type을 움직임 
  - 스프링에만 있어서 종속 O
  - 서로 다른 bean간의 의존성을 위한 어노테이션
  - 입력 파라미터가 1개인 setter, 일반 메소드, 생성자, 멤버변수, 배열, 컬렉션등
  - @Qualifier(강제가 아님)를 사용해서 특정 BEAN을 지정(admin 페이지, 일반 페이지 구분) 

  ```java
  class Test{
    @Autowired
    @Qualifier("abc") // --> abc의 bean이 선언도니 Date로 inject 됨 <bean id="abc" class="Date" p:year="2020" />
    private Date mydate;
    
  }
  ```

- @Resource(JSR -250) 

  - name을 움직임 

  - 종속 X

  - 선언이 한정적

  - 멤버 변수와 setter에만 선언되는 또 다른 bean을 설정

    ```java
    class Test{
      @Resource("d1") // -> <bean id="d1" class="Date" p:year="2040" /> -> set메서드를 불러와서 초기화 안해줘도 됨
      private Date mydate; 
      /*
      	public void setDate(Date mydate){
      	this.mydate = mydate;
      	}
      */
      
      @Resource("myaddr") //private Address address; 를 참조함 
      public void setAddress(Address addr){
      	this.mydate = mydate;
      }
      
    }
    ```

- @Configuration
  - 어노테이션으로 환경설정 기반의 스프링 어노테이션 방법을 구현



### com.test06

- beans.xml -> AppConfig로 변환 후 MTest02에서 호출



### com.test07

- 초기화, 소멸화 메소드를 명시할 수 있음

  ```xml
  <bean init-method="" destroy-method=""/>
  ```
  - @PostConstruct : 초기화 메소드
  - @PreDestroy : 소멸화 메소드

- Auto-Detecting 속성 지정

  - <context:component-scan 속성>

  - base-package

  - user-default-filters : Stereotype-annotation 등록시 기본 필터를 사용하지 않고자 할 때 filter

  - annotation-config : Stereotype-annotation을 제외한 어노테이션을 처리하지 않을 때 false

  - scope-resolver : Bean Scope Annotation 이 아닌 다른 방법으로 정의 시 클래스를 지정

    - ex) <context:component-scan scope-resolver="com.test.Rest"/>

  - scoped-proxy: Non-singleton bean을 singleton bean이 참조할 때 사용

    - no| interface [자바의 다형성 객체를 만들어서 참조]|targetClass [클래스]

    > name-generator : Stereotype-annotation 을 적용한 클래스를 bean으로 등록할 때 BeanName을 지정하지 않을 경우 자동을 생성되는 BeanName의 형식을 BeanNameGenerator을 구현하여 사용
    >
    > ex) <context:component-scan name-generator="com.test.Controller" />
    >
    > ex) vo -> entity -> dto

- ```xml
  <context:component-scan> inner tag 
  	- 현재 사용되는 지정된 패키지에 추가하거나 빼고 싶은 대상을 지정
  	- type: annotation | assignable | regex | aspectj
  	<context:exclude-filter type="regex" expression="*.*Stub"/>
  	<context:include-filter type="annotation" expression="org.springframework.stereotype.Repository"/>
  </context:component-scan>
  ```

  

## 스프링 어노테이션

- 내부적으로 호출할 수 있는 비즈니스 로직에는 안걸고 bean에 건다 

### Lazy

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ggrd9sxleaj30xw0fsdkw.jpg" alt="image-20200715094720402" style="zoom:50%;" /> 

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ggrdb76a0wj31jc0f8wjl.jpg" alt="image-20200715094844411" style="zoom: 50%;" /> 

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ggrdc2wgu8j30fg078my5.jpg" alt="image-20200715094934913" style="zoom: 50%;" /> 

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ggrdd378mvj31j40ei43j.jpg" alt="image-20200715095032393" style="zoom:50%;" />![image-20200715095109024](https://tva1.sinaimg.cn/large/007S8ZIlgy1ggrddpwy9hj30hq08kgn9.jpg)

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ggrdd378mvj31j40ei43j.jpg" alt="image-20200715095032393" style="zoom:50%;" />![image-20200715095109024](https://tva1.sinaimg.cn/large/007S8ZIlgy1ggrddpwy9hj30hq08kgn9.jpg)