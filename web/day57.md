# DAY57

## 학습목표

1. servlet/jsp의 이벤트를 구현할 수 있다
2. context의 Filter를 이용해서 페이지 요청에 가로 채기를 할 수 있다
   1. 설정파일에 선언하는 방법 web.xml, 주석으로 선언하는 방법 @
3. ServletContext, ServletConfig, HttpSession, Request의 객체에 발생되는 이벤트을 활용할 수 있다 
4. ajax, jquery를 이용해서 비동기 통신을 할 수 있다



## servlet/jsp의 이벤트

- 내장객체가 연동될 때 이벤트가 발생됨 
- Filter를 사용하여 요청시에 중단작업을 할 수있음 



## Filter

- Filter? 클라이언트의 요청이 서블릿에 닿기전에 가로채서 추가적으로 필터에 있는 내용을 수행하는 것을 말함
- 설정파일에 선언하는 방법 web.xml, 주석으로 선언하는 방법 @
- 단일 필터 작성
  - request -> filter -> response
  - a.jsp -> filter -> web 화면 결과



### 웹 보안을 처리하는 객체

1. Authentication Filters 

   - 인증(사용자 인증)

   - 서버 -> 웹서버 -> 웹 어플리케이션 -> 페이지 연동 

2. Logging and Auditing Filters

   - 로깅 및 감사 필터

   - 인증값이 1번을 통해서 페이지 권한을 받고 
   - 다른 페이지 요청을 할 경우 유효성 검사 
   - php -> html -> (전페이지의 인증값을 검증) -> jsp

3. Image conversion Filters

   -  이미지 변환 

   - 보안인증 코드(QR코드)등 페이지 요청 보안 

4. Data compression Filters

   - 데이터 압축

5. Encryption Filters

   - 암호화 필터

   - 툴을 이용해서 필터를 기반으로 연동하는 프로그램을 호출 

6. Tokenizing Filters

   - 토크나이징(Tokenizing) 필터

   - 페이지 요청 get||post 방식으로 폼 데이터가 전송되거나 url에 데이터가 param값을 가지고 전송될 때 사용하는 필터
   - ex) http://localhost:8787/Test?no=3&name=%234% 

7. Filters that trigger resource access events

   - URL 및 기타 정보들을 캐시하는 필터

8. XSL/T filters 

   - XML 컨텐츠를 변형하는 XSLT 필터

9. Mime-type chain Filter

   - Mime-Type 체인 필터

### 어노테이션

```java
import javax.servlet.Filter;
import javax.servlet.annotation.WebFilter;
import javax.servlet.annotation.WebInitParam;

@WebFilter(filterName = "TimeOfDayFilter",
urlPatterns = {"/*"},
initParams = {
    @WebInitParam(name = "mood", value = "awake")})
public class TimeOfDayFilter implements Filter {
    ...
```



### Specifying Filter Mappings

![img](https://tva1.sinaimg.cn/large/007S8ZIlgy1ggdr5u1286j307b056jrc.jpg) 

- Recall that a filter chain is one of the objects passed to the `doFilter` method of a filter. This chain is formed indirectly by means of filter mappings. The order of the filters in the chain is the same as the order in which filter mappings appear in the web application deployment descriptor.
- [설명](https://docs.oracle.com/javaee/7/tutorial/servlets006.htm)





## ServletContext, ServletConfig, HttpSession, Request

### ServletContext

- ServletContextListener

  - 웹 어플리케이션이 시작되거나 종료될 때 호출할 메서드를 정의한 인터페이스

  - public void contextInitialized(ServletContextEvent sce) : 웹어플리케이션을 초기화할 때 호출

  - public void contextDestroyed(ServletContextEvent sce) : 웹 어플리케이션을 종료할 때 호출

  - class 구현

    ```java
    package com.test02;
    
    import javax.servlet.ServletContextEvent;
    import javax.servlet.ServletContextListener;
    
    public class MyListener implements ServletContextListener {
    
    	@Override
    	public void contextDestroyed(ServletContextEvent sce) {
    		System.out.println("[ contextDestroyed : "+ sce.getServletContext().getServletContextName() +" ]");
    	}
    
    	@Override
    	public void contextInitialized(ServletContextEvent sce) {
    		System.out.println("[ contextInitialized : "+ sce.getServletContext().getServletContextName() +" ]");		
    	}
    	
    }
    ```

  - web.xml에 설정

    ```xml
      <listener>
      	<display-name>MyListener</display-name>
      	<listener-class>com.test02.MyListener</listener-class>
      </listener>
    ```

  - 결과

    ![image-20200703113246461](https://tva1.sinaimg.cn/large/007S8ZIlgy1ggdky2pdoxj31sw0r2guq.jpg)



## WebApplication

- WAS (Tomcat 5.0) [/http: /ftp:] api, db
  - 왜 Tomcat을 쓰는가??
  - 10) [java(JDBCTemplate)](http://tomcat.apache.org/tomcat-8.5-doc/jndi-datasource-examples-howto.html)
  - 09) [JNDI](http://tomcat.apache.org/tomcat-8.5-doc/jndi-resources-howto.html)
  - 32) [JDBC Connection Pool](http://tomcat.apache.org/tomcat-8.5-doc/jdbc-pool.html)
- /lib /cfg /bin /webapps /work /logs
- sever.xml -> context.xml -> web.xml
  - 하나의 server는 여러개의 context를 갖고 있고 
  - 하나의 context는 여러개의 web을 갖고 있음 



## 비동기 통신

- 비동기 통신 VS 동기 통신
  - 신호를 보내면 신호를 받아야 프로세스가 진행이되면 **동기화** (request <-> response)
  - 신호를 보내고 신호가 돌아오는 동안 또 다른 신호를 보내는 통신은 **비동기화**
- 서버페이지(정적페이지 제외한 동적페이지-servlet/jsp)는 하나의 클래스 단위로
- 웹서버가 멀티 스레딩을 연동해서 다중 통신을 사용할 수 있게함 
- 즉, 동기화 통신을 프로세스를 가진 WAS가 관리함 
- 서버페이지에서 또 다른 서버페이지 및 프로세스 제외한 프로세스간의 통신을 할 때 사용되는 것이 비동기 통신[ajax]



### jQuery

- [css3 + html + js] + ajax = html5 + Ajax

-  JSP -> controller[session, request] -> JSP

- JSP -> servlet > JSP

- ajax, jquery

  - jsp -> js (쉽다)
  - js -> jsp  (고민)

  

  

  