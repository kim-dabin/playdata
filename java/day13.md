# Day13

## 목표

1. 클래스, 상속, 다형성의 개념을 활용할 수 있다
2. Object 메소드를 활용할 수 있다 
3. Exception 구현 하는 방법을 숙지 할 수 있다 



## 복습

### 다형성

- 선조의 주소번지를 통해 동적으로 후손의 주소를 탐색하는 것 

### 상속

- 클래스간의 단일 상속, 인터페이스 다중 상속
- 선조의 기능 + 후손의 기능
- 접근 제한자 protected
  - 후손의 멤버에게만 선언된 제한자의 멤버를 오픈하겠다 
  - but, 다른 팩키지에서 객체를 생성 후 주소를 통해 참조가 불가능하다
  - com.test.My m1 = new com.test.My();
  - m1.prn();
  - 호출시 오류가 발생된다면 prn() 메소드는 다른 패키지의 My클래스의 protected가 선언된 선조 메소드 



### 클래스 

- 캡슐화(은닉된(private) 멤버변수에게 오픈된 메소드가 값을 전달 및 변경하는 구조)
  - 은닉된 멤버 변수 : private
  - 오픈된 메소드 : setter(void) & getter(return)



ArrayList(Collection<? extends E> c) : 네가 지정한 선조(E)의 후손(?)만 받겠다





## 클래스, 상속, 다형성



### has ~ a 관계

```java
public class Exam01 {
  public static void main(String[] args){
    MyTest m1 = new MyTest();
  }
}


import java.util.*;

public class MyTest {
	private Date date;
	public MyTest() {
		date = new Date();
	}
	
	public MyTest(Date date) {
		this.date = date;
	}
	
	public Date getDate() {
		return date;
	}
	public void setDate(Date date) {
		this.date = date;
	}
	
}

```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ge88dj1dw0j31960rsh1r.jpg" alt="image-20200427135132037" style="zoom:33%;" /> 





## Exception 구현

### java.lang.Throwable

- 프로그램 실행이 중단 될 경우, jvm이 자동 생성하는 클래스
- 컴파일 후 실행 시점으로 Throwable로 생성된 객체를 리턴 
-  Error / Exception 을 서브 클래스로 두고 있음 
- Exception은 value 때문에 프로그램이 중단되는 것이기 때문에 복구 가능(Error는 안됨)
  - try ~ catch라는 구문을 이용해서 jvm이 생성된 클래스를 코드로 잡아서 처리할 수 있도록 도와줌



### Exception

- 런타임시에 값(리터럴, 시스템 변수값)에 의해서 프로그램이 중단될 경우 슈퍼 클래스Throwable의 객체 생성 주소번지로 생성되어 리턴됨 

- ex) Throwable th = new ArithmeticException(); // jvm 생성 후 th를 실행시점으로 던져주게 됨 

- 선언 방법

  ```java
  try{
    
    예외가 발생된 코드;
     
     }catch(발생된예외클래스 변수){
    
    처리할 코드;
  
  	}finally{
    
    예외에 상관없이 무조건 실행하는 코드;
    
  }
  ```

- 하나의 try는 여러개의 catch를 후손 -> 선조로 지정할 수 있음 
- 중첩으로 사용 가능 
- 하나의 try는 마지막에 finally{} 키워드를 사용해서 반드시 처리할 문장 지정 가능 
  - ex) hwp, doc 중단되면 bak 파일을 만듦
  - ex) db 연결 후 db 해제 코드 
  - ex) 파일 작업 후 close();

- throw 

  - Exception 클래스를 생성할 때(예외발생시킬 때) 사용하는 키워드 
  - 강제로 예외를 발생시킴
  - throw 키워드를 주면 Exception() 클래스를 jvm이 생성한 것으로 보게 됨 -> 예외처리를 해줘야함 

  ```java
  public static void prn() throws Exception {
  		throw new Exception(); 
  	}
  ```

  - 예외처리

  ```java
  public static void prn02() throws Exception {
  		prn();
  	}
  ```

  ```java
  public static void prn02(){
  		try {
  			prn();
  		} catch (Exception e) {
  			
  		}
  	}
  ```

  

- throws

  - 메소드 뒤에 선언하는 키워드로 예외처리를 메소드를 호출하는 쪽으로 **위임**하는 키워드 
  - 예외처리를 자신이 하지 않고, 자신을 호출하는 메소드에게 책임을 전가 시킴 

```java
public class MTest02 {

	public static void prn() throws Exception {//예외처리 위임
		throw new Exception(); 
	}//prn() end 

	public static void prn02() throws Exception {//예외처리 위임
		prn();
	}//prn02() end 

	public static void prn03() throws Exception {//예외처리 위임
		prn02();
	}//prn03() end 

	public static void main(String[] args)  {
		try {//예외 처리
			prn03();
		} catch (Exception e) {
		}//try~catch
		
	}//main() end 

}//MTest02 end 

```



### Exception Method

1. RuntimeException
   - unchecked exception -> 강요하지 않음
   - 얘의 자손들은 예외가 발생할 때만 처리함
   - 그외의 Exception들은 checked exception -> 강제로 예외 처리해야함 
2. IOException
3. InterruptedException



### Custom Exception 

