# DAY64

## 학습목표

- Spring MVC 



## Spring MVC

### 실습

#### TestMVC [Project명]

- 형태

```tex
-src
	-controller
-webContent
	-META-INF
	-WEB-INF //하위에다가 jsp페이지를 만든다.
		-web.xml[servlet설정]
		-mvc-servlet.xml[서블릿네임-servlet.xml]
		-view
			-hello.jsp
		-view02
			-a.jsp
			-b.jsp
			-c.jsp
			-d.jsp
	-index.html
	
-pom.xml
```

#### TestMVC02 [Project명]

```tex
-src
	-controller
-webContent
	-META-INF
	-WEB-INF //하위에다가 jsp페이지를 만든다.
		-web.xml[servlet설정]
		-mvc2-servlet.xml[서블릿네임-servlet.xml]
		-view
			-hello.jsp
		-view02
			-a.jsp
			-b.jsp
			-c.jsp
			-d.jsp
	-index.html
	
-pom.xml
```





### MVC패턴

![MVC](https://tva1.sinaimg.cn/large/007S8ZIlgy1gh0pja0cofj30p00bhta1.jpg)

- web.xml 설정파일은 "웹, 비지니스 계층으로 분리됨"
- <listener>라는 태그를 이용해서 설정파일에 연동되는 bean을 등록하는 파일을 지정함 
- mvc2-servlet.xml과 applicationContext.xml 파일 두개로 지정
- applicationContext에서 정의된 bean을 mvc2-servlet.xml에서 사용할 것이라,
  - 순서를 applicationContext.xml 먼저 등록 후 mvc2-servlet.xml를 나중에 등록 
- ContextLoaderListener 클래스는 기본으로 applicationContext.xml파일을 읽음
- javax.servlet.ServletContext 인터페이스를 구현한 클래서 WAS가 로딩할 때 

#### Model 

- 객체값을 변수 = 값; 으로 전달 
- view의 객체 요청시까지 소멸하지 않고 값을 가짐



### 스프링 웹프로젝트 설정후 메모리 호출 순서

1. WebApplicationInitializer
   - web.xml
   - 페이지 설정, 프로젝트 초기값, 이벤트, 필터, 서블릿을 설정

2. ContextLoader
   - 웹프로젝트가 실행되면 가장 먼저 메모리에 로드
   - 프로젝트 초기값을 이벤트[ContextLoaderListener]를 이용해서 호출
3. org.springframework.web.servlet
   - View, [ModelAndView](https://docs.spring.io/spring/docs/4.3.27.RELEASE/javadoc-api/org/springframework/web/servlet/ModelAndView.html), [DispatcherServlet](https://docs.spring.io/spring/docs/4.3.27.RELEASE/javadoc-api/org/springframework/web/servlet/DispatcherServlet.html)

