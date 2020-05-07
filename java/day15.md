# Day15



## 학습목표

1. java.util 클래스를 이용하여 정렬을 사용할 수 있다.
2. java.io를 이용하여 탐색기 구현의 기본 클래스를 활용 할 수 있다.
3. byte, char, object 타입을 사용하여 java.io의 클래스를 이용하여 입출력을 구현할 수 있다. 
4. 문자열 분할 작업을 할 수 있다



## 정렬

- 유니코드(2byte)를 기준으로 정렬을 지정함 

```java
package com.chap11.sec02;


import java.util.Arrays;
import java.util.Comparator;

public class Person implements Comparable<Person> {

   private int id;
   private int age;
   private String name;

   public int getId() {
       return id;
   }
  

   public int getAge() {
       return age;
   }

   public String getName() {
       return name;
   }

   public Person(int id,  int age, String name) {
       this.id = id;     
       this.age = age;
       this.name = name;
   }

   @Override
   public int compareTo(Person m_p) {      
       return (this.id - m_p.id);
   }

   @Override

   public String toString() {
       return "[id=" + this.id +  ", age=" + this.age + ", name=" +
               this.name + "]";
   }

   
   public static Comparator<Person> IdComparator = new Comparator<Person>() {
       @Override
       public int compare(Person p1, Person p2) {
           return (p1.getId() - p2.getId());
       }
   };
   
   public static Comparator<Person> AgeComparator = new Comparator<Person>() {	  
       @Override
       public int compare(Person p1, Person p2) {
           return p1.getAge() - p2.getAge();
       }
   };

	public static void main(String[] args) {
		
		Person[] perArr = new Person[3];
		perArr[0] = new Person(111, 25, "루세");
		perArr[1] = new Person(333,  22, "루리");
		perArr[2] = new Person(222,  35, "루오");
		
		Arrays.sort(perArr);
        System.out.println("기본 정렬 :\n" + Arrays.toString(perArr));
         
   
        Arrays.sort(perArr, Person.AgeComparator);        
        System.out.println("나이순으로 정렬:\n"+Arrays.toString(perArr));
         
   
        Arrays.sort(perArr, Person.IdComparator);
        System.out.println("Id 순으로 정렬:\n"+Arrays.toString(perArr));

	}

}

```



### java.lang -> Interface Comparable<T>

- **int compareTo(T o)**
- Arrays.sort(Object[]);
- Arrays.sort(T[]); -> 호출하는 메서드 Comparable<MyClass>의 compareTo 

- compareTo

- java.util.ComparableTimSort.countRunAndMakeAscending 

  ```java
  MyClass[] m1 = new MyClass[] {new MyClass("홍길동",10), new MyClass("정길동",70), new MyClass("최길동", 20)};
  Arrays.sort(m1);
  ```

  - Arryas.sort(m1)할 때, 정렬의 기준점을 정의하지 않으면(compareTo) Exception 발생

  ![image-20200429101525899](https://tva1.sinaimg.cn/large/007S8ZIlgy1geaddbimo7j31kg06ajvn.jpg)



### java.util -> Interface Comparator<T>

- **int compare(T o1, T o2)**
- collections
- public static <T extends Comparable<? super T>> void sort(List<T> list)
  - 반드시 T가 Comparable을 재정의하여 정렬기준이 있어야 함 
- public static <T> void sort(List<T> list, Comparator<? super T> c) 
  - T 정렬 기준이 없는 타입을 때 사용
  - void sort(대상 컬렉션 객체 , 정렬 기준)



## 자바의 입출력(I/O) - 단일 쓰레드(IO), 다중 쓰레드(NIO, NIO2)

### 자바 I/O

- 스트림(Stream) : 데이터 흐름, 바이트(byte) 단위, 바이트를 전송하는 통로
- 한 문자(char)씩 읽음 

#### 노드 스트림

#### 필터 스트림



### 단일 스레드 : java.io -> byte, char, Object

- byte, char, Object
- 네트워크 통신(ex 채팅 )



### 다중 스레드 : java.nio(New IO) -> Buffer

- Buffer
- java.nio.file (New IO2)
- ftp, mailling









## 문자열 분철 작업 

1. public String[] java.lang.String.split(String regex)
   - 문자열 분철 후 배열 처리
2. java.util.StringTokenizer(String)
   - 객체 단위로 값을 리턴 후 출력하거나 분철시 사용 
3. java.util.Scanner(input).useDelimiter(String regex)
   - 각 데이터의 리턴 타입으로 추출 할 수 있음 nextDataType();

### 정규표현식

