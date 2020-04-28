# Day12



## 학습목표

1. 클래스 구현, 상속, 추상화 -> 다형성(동적 바인딩)을 통해서 OOP의 개념을 숙지할 수 있다.

2. interface 추상화 작업을 할 수 있다. 

3. 원하는 클래스 구조를 만들 수 있다.

4. GUI가 가진 특성을 가지고 샘플을 구현할 수 있다. 

5. Exception을 할 수 있다. 

   

## 추상화

**예시) 자동차 회사** -> 5가지의 경우를 하나로 묶어서 패키지로 만듦

1. 부서장 회의 : **interface**(상수값, 추상 메소드, default)

   (TD) 기동 - 3.5 중형 public static final double TD = 3.5;

   (PR) 가격 - 5000

   (OD) 출하시점 - 12/25

   컬러? public abstract void Color();

   홍보? public abstract void SBT();

   공정? public abstract void FA();

2. 1을 공유하면서 각 부서에서 2차 회의: **abstract class** 

   ```java
   class abstract 홍보부{
     public abstract void SbT_Rate();
     public void SBT(){}
   }
   ```

3. 작업 : 1,2번에서 정의한 것을 구현 : **class**

4. 출하시점 12/25 : **Exception**

   상대방 회사에서 12/24에 신차를 출하함 -> 그럴 줄 알고 12/25일날 중형,  소형, 대형도 출하 

5. 회사가 망함 : **_Error** 

> 추상클래스는 어느정도 구현을 해놓은 상태 
>
> 인터페이스는 완전 계획 단계



## 인터페이스

### 정의

- 완벽한 추상화를 구현하는 키워드 

- [선언]

  ```java
  public interface UserName{} // -> UserName.class
  ```

- 멤버 : 상수, 추상메소드, default 메소드 (1.8 이상)

- 다중 상속을 허용 -> interface - 동그라미/ class - 네모

  <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ge4woqt4k8j32ae0rsh81.jpg" alt="image-20200424165024697" style="zoom:33%;" />

- 생성자가 없고 abstract class와 문법이 동일하다.

### 문법

- 인터페이스가 가진 메소드를 재정의 하지 않으면 추상 클래스가 됨 

- 인터페이스는 new 연산자로 객체 생성이 불가능 BUT 단 주소는 참조 가능

- implements 키워드로 클래스가 다중 상속을 ,(컴마)로 나열해서 선언

  ```java
  public class Vector<E>
  extends AbstractList<E>
  implements List<E>, RandomAccess, Cloneable, Serializable
  ```

- 생성자가 없어서 this 가 없음 

- static final을 안적어도 기본적으로 static final

  ```java
  package com.test;
  
  public interface IA {
  	public int a = 100;
  	public static final int b = 200;
  	static final int c = 300;
  	final int d = 400;
  	static int f = 20;
  }
  
  ```

  <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ge4kpt15g8j306e08mgls.jpg" alt="image-20200424095503617" style="zoom: 50%;" /> 

- 추상메소드, default 접근 제한자 메소드 -> 재정의 해서 호출

  - default는 그냥 이 인터페이스가 어떤 건지 알려주는 용도임

  ```java
  package com.test;
  
  public interface IB {
  	public void Prn();
  	void Prn02();
  	public abstract void Prn03(String str);
  	int getA();
  	String getName(String nic_name);
  	public default void Prn04() {
  		System.out.println("default라서 됨!");
  	}
  	
  }
  ```

  <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ge4kumerzvj308c09smym.jpg" alt="image-20200424100056046" style="zoom: 50%;" /> 



### 특징

- interface를 구현한 class를 사용해 그 클래스에서 재정의한 메서드를 가져다 쓰면 interface의 모든 추상 메소드를 구현할 필요가 없음

- 추상클래스-인터페이스 

  - 인터페이스는 완전한 추상화
  - 추상클래스는 인터페이스를 가지고 재정의

- 익명 클래스 : 클래스의 이름이 없는 클래스

  - 주로 추상클래스나 인터페이스를 구현할 때 쓰임 
  - 주로 일회성으로 사용할 때 씀 
  - 자유영역 공간에 주소값을 한번만 올림(객체를 생성하는 게 아님)

  ```java
  addWindowListener(new WindowAdapter() { //public abstract class WindowAdapter
  			@Override
  			public void windowClosing(WindowEvent e) {
  				System.exit(0);
  			}
  		});
  ```

- 이너 클래스: 

  ```java
  public class My03 extends Frame {
  	
  	class MyEvent extends WindowAdapter {//이너 클래스  가장 권장됨 
  		@Override
  		public void windowClosing(WindowEvent e) {
  			System.exit(0);
  		}
  	}
  	public My03() {
  		// TODO Auto-generated constructor stub
  		super("title is none");
  	}
  	public void go() {
  		addWindowListener(new MyEvent());
  		setSize(500, 500);
  		setVisible(true);
  	}
  	
  ```

  

