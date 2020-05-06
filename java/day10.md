# Day10

[TOC]



##  난수 발생

### java.lang.Math.random()

### java.util.Random()

- 가장 큰 기본 자료형인, double을 기본으로 세팅함
- 메모리를 가장 많이 잡아먹기 때문에 Math 클래스의 random() 메서드를 쓰는게 좋음 



## String, == , public boolean equals(Object obj)

- 리터럴을 만나면 값을 비교
- 객체 변수를 만나면 주소 비교
  - 객체에는  변수에 값이 들어가는게 아니라, 주소값이 들어가니까

```java
String str = "aaa";
String str02 = "aaa";
String str03 = new String("aaa");
String str04 = new String("aaa");
System.out.println(str==str02); //주소 비교
System.out.println(str02==str03);// 주소비교
System.out.println(str03==str04);//주소 비교 
System.out.println(str03.equals(str04));//재정의 한 다음 값비교
```

![jvm_String](https://tva1.sinaimg.cn/large/007S8ZIlgy1ge2aj25w6sj32za0rkawq.jpg)

-  Stirng str = new String("aaa");

  - 1) stack에 str 변수 공간 생성
  - 2) heap에 공간 할당
  - 3) 1번의 str 변수가 2번의 heap 공간을 참조
  - 4) 상수풀에 "aaa" 문자열이 있는 지 확인
  - 4-1) 있으면 5번으로 
  - 4-2) 없다면 상수풀에 생성 후 5번으로 
- 5) 상수풀의 주소값으로 2번에 생성한  heap의 공간을 초기화
  
  

## wrapper class

- 래퍼 클래스를 사용하는 이유는, 기본 자료형을 Object로 받을 때 문제가 생기기 때문임

  - https://docs.oracle.com/javase/tutorial/java/data/numberclasses.html 참고

  <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ge2b4bu1n2j30n0070dh6.jpg" alt="image-20200422105308948" style="zoom:33%;" /> <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ge2b5t7j4uj30q6084402.jpg" alt="image-20200422105434041" style="zoom:33%;" />

- toString()은 Object가 주소를 리턴하는 메소드지만, 객체를 생성해서 호출하게 되면 자동으로 호출되는 메서드

- 재정의 할 경우 여러가지의 경우로 정의를 하게 되는데, 래퍼 클래서 타입의 toString()은 재정의 했어도 기본 타입과 자동 형변환이 되기 때문에 자동 호출 되지 않음  

```java
int a = 10;
int b = 10;
System.out.println(a==b); //true
System.out.println("====================");
Integer ir = new Integer(10); 
System.out.println(ir == a); //객체와 값의 비교지만, true 
ir = a;
b = ir;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ge2agkhipoj30e004it8r.jpg" alt="image-20200422103015075" style="zoom: 33%;" />

