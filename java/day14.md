# Day 14



## 학습목표 

1. java.util의 컬렉션 객체를 이해할 수 있다. 
2. 데이터 자료 관리를 원하는 객체를 통해 탐색(CRUD)할 수 있다. 
3. 컬렉션의 추상화를 설계할 수 있다.



## 문자열을 관리하는 클래스[index, value, key]

1) java.lang.String (static-불변 데이터)

2) java.lang.StringBuffer (가변)- 스레드에 안전함 (데이터를 임시 저장소에 담았다가 보내줌)

3) java.lang.StringBuilder (가변) - 스레드에 안전하지 못함(데이터를 바로 바로 보내줌)

> 스레드는 메서드 단위로 실행하는 것을 말함(메서드 단위로 실행 모듈을 만듬)

- 자료를 관리하는 것들은 **CRUD**를 할 수 있어야함
- **index, value, key** 이 세가지로 데이터를 관리함

### java.lang.StringBuffer 

- 스레드에 안전해서 네트워크 통신등에 유용
- index가 있음 -> 자료를 순차적으로 관리 

#### CRUD

- Create
  - StringBuffer()
  - StringBuffer(String)
  - StringBuffer(int) // capacity() -> 기본 용량은 16 글자 //capacity가 늘어나기 때문에 가변 가능 
  - append()
  - length()
- Read
  - toString()
- Update
  - insert()
- Delete
  - delete()
- trimToSize()



### java.lang.StringBuilder

- 문자열을 가변으로 받을 때 사용

- Create
  - StringBuilder()
  - StringBuilder(String)
  - StringBuilder(int)
  - append()
- Read
  - toString()
- Update
  - insert()
- Delete
  - delete()
- trimToSize()



## java.util의 컬렉션 객체 

![img](https://tva1.sinaimg.cn/large/007S8ZIlgy1ge9eo9o6tzg30b6042aa5.gif)

- 자료구조에 대한 클래스들을 자바의 컬렉션이라고 함 

  - 자료구조: 자료를 조직적, 체계적으로 구분하여 표현하는 것 

- Iterator, Enumeration :자료를 탐색하는 메소드를 가진 인터페이스 

- Collection 
  - utill 팩키지의 root class 
  - CRUD 메소드가 선언된 인터페이스 
  - add() -> [Create/Read] , clear() -> [Delete], remove() -> [Delete], size(), toArray(), iterator() -> [Read]
  
  

### 데이터를 관리하는 방법(index, value, key)

1. index : **List** Interface

   - [0],[1],[2],[3],...

   - 중복데이터 O, NULL O
   - ArrayList, Vector
   - get(index), set(index, Element)

2. value : **Set** Interface

   - 1,2,3,'a'
   - 중복데이터 X, NULL X

3. key : **Map** Interface

   - a = 'b', b = 'd', ex) {1:'3'} , {1 = '3'}
   - key는 중복 데이터 X, NULL X
   - value는 중복데이터 O, NULL O
   - key(Set), value(List)



### index로 데이터를 관리하는 List 객체

- ArrayList, Vector
- C : boolean add(E e)
- R : E get(int index), Iterator<E> iterator()
- U : E set(int index, E element)
- D : E remove(int index)



### value로 데이터를 관리하는 Set 객체

- HashSet
- C: boolean add(E e)
- R: Iterator<E> iterator()
- U: boolean add(E e)
- D: boolean remove(Object o)

```java
//Set을 이용한 대량 연산 자료 관리  
package com.chap11.sec02;

import java.util.HashSet;
import java.util.Set;

public class SetTest02 {

	public static void main(String[] args) {
	 Set<String> m1=new HashSet<String>();
	 Set<String>m2=new HashSet<String>();
	 
	 m1.add("A");
	 m1.add("B");
	 m1.add("C");
	 
	 m2.add("B");
	 m2.add("D");
	 m2.add("E");
	 
	 Set<String> m_union =new HashSet<String>(m1);
	 m_union .addAll(m2);
	 
	 Set<String> m_intersection =	new HashSet<String>(m1);
	 m_intersection.retainAll(m2);
	 
	 System.out.println("합집합 : " + m_union);
	 
	 System.out.println("교집합 : "+m_intersection);
		
		
	}

}

```



### Key로 데이터를 관리하는 Map 객체 

- HashMap
- C : V put(K key, V value)
- R : V get(Object key), Set<K> keySet(), Collection<V> values()
- U : Set<Map.Entry<K,V>> entrySet() 
  - value를 바꾸기 위함 
  - setValue(V value)
- D : V remove(Object key)



## 제네릭(Generic)

- 자바에서 제공하는 객체들을 자료로 관리할 수 있는 하나의 형식



## 기타

### 국가 리스트를 한글로 변환

```java
package com.chap11.sec03;

import java.util.Locale;
public class LocaleTest02 {
	public static void main(String[] args) {

		Locale locale = null;
		String[] countries = Locale.getISOCountries();
       System.out.println(countries.length);
	  
		for (String country : countries) {
			locale = new Locale("ko", country);
			System.out.println(locale.getDisplayCountry());
		}
	}
}

```



### 난수 발생을 이용한 프로그램

- (Math.random() * (최대값-최소값)+1) + 최소값

```java
package com.chap11.sec03;

public class RandomTest {

	public static void main(String[] args) {

		double x = Math.random();
		System.out.println("실수형값  0.0 ~ 1.0: x = " + x);

		int r1 = (int) (Math.random() * 10) ;
		System.out.println("정수형  -0 ~ 9: r1 = " + r1);

		int r2 = (int) (Math.random() * 6) + 3;
		System.out.println("정수형  3 ~  8:  r2 = " + r2);

	
			int r3 = (int) (Math.random() * 21) - 10;
			System.out.println("정수형  -10  ~  10: r3 = " + r3);
		
			
			/*for(int i =0;i<=100;i++){			
			int r4 = (int) (Math.random() * 6) + 10;
			System.out.println("정수형  10~  15:  r4 = " + r4);
			}*/

	}

}

```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geipaznlntj30ju056gm1.jpg" alt="image-20200506151255788" style="zoom:50%;" />  

### NumberFormat 클래스를 이용하여 원화표시, % 등을 구현

```java
package com.chap11.sec03;

import java.text.NumberFormat;

public class NumberFormatTest {

	public static void main(String[] args) {
		 long ex01 = 1000000L ;
         long ex02 = 2000000L ;
         double ex03 = 0.50D;
        NumberFormat nf1 = NumberFormat.getNumberInstance (); // (1) 범용 수치 포맷
        NumberFormat nf2 = NumberFormat.getCurrencyInstance (); // (2) 통화 포맷
        NumberFormat nf3 = NumberFormat.getPercentInstance (); // (3) 퍼센트 포맷

        System.out.println (nf1.format (ex01)); // (4) 
        System.out.println (nf2.format (ex02)); // (5) 
        System.out.println (nf3.format (ex03) ); // (6)
      

	}
}
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geipbfwwz2j30ju0563yk.jpg" alt="image-20200506151324322" style="zoom: 50%;" /> 