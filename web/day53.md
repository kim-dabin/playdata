# DAY53

## 학습목표

1. jsp 내장객체를 선언하고 활용할 수 있다. 
2. jsp를 이용해서 액션태그인 <<jsp:userbean/>>을 사용할 수 있다. 
3. 세션과 쿠키 객체를 이용해서 세션 트래킹을 할 수 있다. 
4. DB연동 [web + db]/MVC를 구현할 수 있다. 



## jsp 내장객체 선언 및 활용

- 내장 객체는 jsp 요청 범위를 제한하여 선언할 때 사용하거나 객체를 통한 값을 리턴 받을 때 영역 내에서 호출하여 사요함 

### SCOPE

- page : "javax.sevlet.jsp.PageContext" 현재 jsp 페이지 영역 
- request : "javax.servlet.Http.HttpServletRequest" 요청하는 범위 내에서 제어권을 가진 상태의 영역 
- session : "java.servlet.http.HttpSession" 클라이언트의 컨텍스트를 브라우저 요청시 서버가 관리하는 객체의 영역[장바구니, 비회원 처리, 자회사로 접속했을 때 생성되는 모든 기록]
  - session X -> 금융회사, 국세청 등등의 보안처리 모듈이 설치된 사이트  
- application : "javax.servlet.ServletContxt" 응용프로그램 전체 영역 
- servlet/jsp -> page -> request -> session -> context



1. com.test.MyTest -> Servlet get() /mytest

   web.xml 등록

2. MyTest class -> test/a.jsp 처럼 각 객체에 값을 담는다 

3. 결과값을 보기 위해 /test02/a_res.jsp로 주소를 요청함



## jsp를 이용해서 액션태그인 <<jsp:userbean/>>을 사용

- vo를 만들어서 객체를 태그로 선언 생성 후 setter & getter를 속성에 자동 맵핑할 때 사용함 
- 단, vo에 멤버변수의 대문자는 수용하지 X
- vo 기본 생성자가 반드시 존재 해야함 
- 로직에서 사용하는 vo 객체를 서버 페이지에서 beans로 변환해서 사용함 

```jsp
com.vo.Emp myEmp = new com.vo.Emp();
<jsp:useBean id="myEmp" class="com.vo.Emp" scope="영역" beanName="별칭"></jsp:useBean>
ex) private String ename; myEmp.setEname("홍길동");

<jsp:setProperty property="ename" or "*" name="myEmp" param="변수" value="값"/>
"*"-> 모든 set 메서드를 호출 
myEmp.getEname();
<jsp:getProperty property="ename" name=""/>
```



## 세션과 쿠키 객체를 이용해서 세션 트래킹 가능

### 세션 [java.servlet.http.HttpSession]

- 세션 [java.servlet.http.HttpSession] : jsp (session) 에서는 내장객체로 접근하고 servlet에서는 java.servlet.http.HttpServletRequest가 리턴해주는 getSession()이라는 메소드로 객체를 리턴 받아 사용함 
- 객체가 생성이 되면 요청한 url을 가진 브라우저별로 ID가 생성되어 식별됨 
- 클라이언트의 요청정보를 객체로 생성해서 서버에서 관리하는 객체



### 쿠키

- 클라이언트가 요청한 정보를 생성해서 저장한 다음 클라이언트가 지정한 폴더로 저장관리됨 