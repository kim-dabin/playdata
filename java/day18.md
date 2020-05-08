# Day18

## 학습목표

1. String exam 
2. 스레드 개념을 이해하고 활용할 수 있다. 



## String exam 활용

### java_application[exe, jar]

- Exception, thread를 활용해서 exe를 생성할 수 있다

- API를 생성할 수 있다

- 기획안 문서[기획의도, 제안서, 클래스 다이어그램, db다이어그램, 명세서 등]를 작성할 수 있어야 한다. 

  

### 데이터 처리 

1. 각언어로 .txt .json, .xml .sql .csv .tsv 파일 입출력을 할 수 있다. 
2. 언어간의 호환을 할 수 있다. ex) python csv -> java 입출력을 할 수 있다. 
3. 각 언어로 전송할 수 있어야 한다. ㄴ
4. 검증파일[jar]을 작성할 수 있어야 한다.  





## 스레드

### public class Thread extends Object implements Runnable

```java
java.lang.
  public class Thread 
    extends Object
    implenments Runnable

```

- **Thread란**? **메소드 단위**로 실행하는 모듈 
- **java.lang.Process** ? **exe 단위**로 실행하는 모듈(.class)
- *하나의 프로세서는 여러개의 스레드[다중 스레드-> 메소드가 여러개 돌아감 마치 exe처럼]를 가질 수 있다.
  -  -> 하나의 실행 클래스는 여러개의 메소드를 가질 수 있다. 

### user가 만든 코드를 스레드로 실행하는 방법

> run() 메소드를 통해 Thread클래스로 실행시킨다.

1. Runnable을 implements한 후 run() 메소드를 재정의 한다. 

2. Thread를 extends 한 후 run() 메소드를 재정의 한다 
   - [스레드에 대한 정보를 속성과 함께 메소드로 리턴]
   - 주요 메소드 - start(), sleep(), join(), yield()
   - public void start() : 자동으로 run() 메소드를 호출 

3. (1),(2)의 방법으로 실행하고 싶은 코드를 run() 안에다 작성하고 Thread 클래스의 start()로 실행한다.  
   - 코드 실행시 
   - run() 메소드 단위 -> Thread로 인식 -> start()
   - 학생 [공부, 잠, 영화] 이중 하나만 하는 것 = **단일 스레드** 
   - **다중 스레드**
     - 학생이 공부하면서 과자를 먹는다 = **다중 스레드**
     - 기능 별로 만들어 놓은 메소드를 동시에 실행 
     - 한사람이 문자를 주고 받는다. 

### 예제

![image-20200508142143592](https://tva1.sinaimg.cn/large/007S8ZIlgy1gekz2ar8p0j314y0qotjv.jpg)

![image-20200508141837625](https://tva1.sinaimg.cn/large/007S8ZIlgy1gekyz4ydz8j32240s47c8.jpg)

![image-20200508141935448](https://tva1.sinaimg.cn/large/007S8ZIlgy1gekz02cz7cj314c0u0q9l.jpg)

