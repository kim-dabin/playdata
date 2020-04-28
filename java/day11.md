# Day11

[TOC]



## 학습 목표

1. 클래스의 상속 개념을 이해할 수 있다.
2. 단일 상속을 구현하고 활용할 수 있다.
3. 추상클래스의 개념을 이해하고 구현할 수 있다.
4. 재정의를 구현하고 다형성을 구현할 수 있다.



## 자바의 상속

- 

- 단일 상속을 원칙으로 구현

  <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ge3f5juvt4j31f00rs7e7.jpg" alt="image-20200423095813987" style="zoom: 25%;" /> 

- 생성자는 상속되지 않음

- 선조와 자식 클래스로 호칭하고 super, this 키워드를 사용

  - super. this. -> 해당 객체의 주소를 지칭 
  - b -> b 바로 불러옴 / this.b -> 나의 클래스에 있는 b를 불러옴

- 선조의 생성자를 호출 할 때 후손은 super() 라는 키워드를 사용

- 선조는 후손의 객체 주소를 인지할 수 없음

  <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ge3sgfjek7j31do0rs4qp.jpg" alt="image-20200423173831046" style="zoom:25%;" /> 

- **후손 extends 선조 {}** 라는 키워드로 상속을 선언할 수 있음

- static, final 키워드를 가진 클래스는 상속받을 수 없고 abstract 키워드를 가진 클래스는 상속시 abstract가 선언된 메소드를 재정의 해야함 

- private 멤버는 상속이 안됨 

- 상속시, 객체 생성 : 선조가 먼저 생성되고 후손 객체가 생성

- 상속시, 객체 소멸 : 후손 객체가 소멸되고 선조 객체가 소멸됨 

- 상속 특징 : 선조의 기능(메소드)를 갖고 와서, 그 **기능을 확장**

- Override : 선조와 후손이 동일한 메서드가 있을 경우, jvm에서는 선조의 메소드의 주소를 히든(선조의 메소드 주소는 있긴 함) 처리함  -> 후손의 메서드가 호출 됨 

  - 객체 : 기능들(메소드)을 엮어 놓은 것
  - 객체를 생성할 때 **재정의**(override)를 함

  <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ge3og7pr22j31a80rstoz.jpg" alt="image-20200423151954541" style="zoom: 25%;" /> 

  - 오버라이딩한 경우에는 참조 변수의 타입에 상관없이 항상 실제 인스턴스의 오버라이딩 된 메서드가 호출 됨 

    <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ge3t4equ4pj30rs0yfao0.jpg" alt="image-20200423180133643" style="zoom: 33%;" /> 

- 상속시 protected 접근제한자를 만나면 후손에게만 메소드를 호출할 수 있도록 공개 
  - 같은 패키지, 다른 패키지에 있는 후손은 호출이 가능함
  - 객체 생성은 같은 패키지에 있어야 호출이 가능함 



## 추상 클래스

- 상속시 강제로 메소드를 정의 시키는 클래스

- 선언

  ```java
  public abstract class UserName{}//추상 클래스 
  public abstract class Number{}//예시 
  ```



### 특징

- 추상클래스는 추상메소드가 없어도 abstract 키워드만 선언 되면 추상 클래스가 됨 

- 추상클래스를 상속받는 후손 클래스는 추상 클래스가 가진 추상 메서드를 재정의 하지 않으면 추상 클래스가 됨

- 추상클래스는 new 연산자를 이용한 객체 생성이 불가능함(메서드의 실행 부분이 없으니)

- 매개인자 등의 주소 번지는 대입 가능 

- 추상클래스에만 선언되는 추상 메서드는 메서드의 바디 부분은 없고 선언 부분만 있음

  - 이래서 객체 생성이 안됨 

- 추상메서드를 선언하게 되면 일반 클래스도 추상 클래스가 됨  

- 재정의, 메서드명의 통일 

- 상속의 재정의는, 선조도 있고 후손도 있지만, 추상클래스는 객체로 생성이 안되기 때문에 후손만 있음

- 추상 클래스를 상속하지 않으면 static 멤버만 사용 가능

- 동적 바인딩 - 실행 시 선조의 주소로 메모리 할당을 하면서 메서드를 동적으로 연동하는 방식

  ```java
  Base cat = new Cat();
  cat.start();//동적 바인딩 
  ```

  - 장점
    - 코드 절약, 실행속도 향상, 행위 은닉

  

## 다형성

- 하나의 참조변수에 여러가지 객체를 참조할 수 있는 것  

```java
AA[] opList = new AA[2];
opList[0] = new Hap(10, 23);
opList[1] = new Mul(10, 21);
		
for(AA op : opList) {
	System.out.println(op.getCalc());
}// for end 
		
System.out.println("----------------------");
System.out.println("3) Hap a = 1000, b = 250 변경후 전체 출력");
for(AA op : opList) {				
	if(op instanceof Hap) { //op 변수가 Hap의 주소를 갖고 있는지 확인 
		((Hap) op).setB(250); 
		op.setA(1000);
		}
System.out.println(op.getCalc());
		}// for end 
```

> A instanceof B : A가 B의 주소를 가지고 있는 지(참조변수가 참조하는 인스턴스의 실제 타입) 확인 

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ge3s4hgwlpj318l0rsqhs.jpg" alt="image-20200423172702115" style="zoom:33%;" /> 

