# Day49

## 학습목표

1. was를 이용해서 웹프로젝트를 생성할 수 있다. 
   1. java + was = web (servlet/jsp)
2. was의 원리를 이해할 수 있다.
3. servlet의 상속구조와 인터페이스를 활용하여 http 프로토콜을 통해 값을 C -> S 구조로 전달 할 수 있다. 



## was를 이용해 웹프로젝트 생성

> java로 클래스를 만들자 com.test.MyTest.class 만들어서 web으로 출력하고 싶다.

1. was를 설치 -> tomcat
2. com.test.MyTest.class를 만듦
   1. web.xml : 웹페이지의 정보를 설정하고 저장하는 곳 
   2. MyTest -> /test
3. http: 프로토콜을 통해서 연동할 수 있는 클래스를 찾아서 상속 구조를 만듦
4. HttpServlet 클래스를 상속받음 



### 로딩 순서

1. Day49 -> was[servlet/jsp]

2. /Day49/WebContent/META-INF/context.xml[web application project 설정파일] -> /Day49/WebContent/WEB-INF/web.xml[웹페이지 설정파일]

3. http:// 프로토콜을 통해 페이지 요청 응답할 준비를 함(servlet)

4. 원형 : servlet = java 코드 안에 html 코드가 들어 있음

   ​		jsp = html 코드 안에 java 코드가 들어 있음



## servlet

- servlet?
  - https://docs.oracle.com/javaee/7/tutorial/servlets001.htm#BNAFE
- ServletContext
  - jsp에서는 application 내장 객체로 사용함 
- Interface ServletRequest
  - 모든 정보를 가지고 있어서 넘겨줌 
- Interface ServletResponse
  - 화면에 뿌려줌 
- Interface HttpServletRequest
  - 브라우저를 요청했을 때 갖고 있는 데이터를 받아옴 
  - 
- redirect 와 forward 차이 
  - redirect:  페이지간의 주소 이동 방식, 새로운 페이지에서는 request와 response 객체가 새롭게 생성
  - forward: 페이지 이동만하고 주소가 바뀌지는 않는다. 그래서 웹브라우저는 다른 페이지로 이동한 것을 모른다. 그래서 최초에 호출한 URL만 표시되고 이동 페이지의 URL 정보를 볼 수없다. 동일한 웹 컨테이너에 있는 페이지로만 이동 가능하다. 
  - 현재 실행 중인 페이지와 forward에 의해 호출된 페이지는 request와 response 객체를 공유



### servlet의 데이터 타입 

- MIME Type()

  <ol>
   <li>explicitly per request using <a href="../../javax/servlet/ServletResponse.html#setCharacterEncoding-java.lang.String-"><code>setCharacterEncoding(java.lang.String)</code></a> and
      <a href="../../javax/servlet/ServletResponse.html#setContentType-java.lang.String-"><code>setContentType(java.lang.String)</code></a></li>
   <li>implicitly per request using <a href="../../javax/servlet/ServletResponse.html#setLocale-java.util.Locale-"><code>setLocale(java.util.Locale)</code></a></li>
   <li>per web application via the deployment descriptor or
       <a href="../../javax/servlet/ServletContext.html#setRequestCharacterEncoding-java.lang.String-"><code>ServletContext.setRequestCharacterEncoding(String)</code></a></li>
   <li>container default via vendor specific configuration</li>
   <li>ISO-8859-1</li>
   </ol>

- getOutputStream/getInputStream

- buffer 



























