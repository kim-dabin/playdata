# Day 19 

## 학습목표

1. 스레드의 주기를 살펴 보고 블록킹(blocking)에 대해서 이해할 수 있다. 
2. 네트워크의 구조를 서버와 클라이언트로 이해할 수 있다. 
3. java.net을 이용해서 서버 클라이언트 통신을 구현할 수 있다. 



## 스레드간의 실행 약속(join, sleep, wait & notify)

![image-20200511100610824](https://tva1.sinaimg.cn/large/007S8ZIlgy1geo8jdus7qj31im0rs1d0.jpg)

- join, sleep은 중단된 스레드를 재실행 약속을 할 수 없음
- 여러 스레드가 실행 될 경우, 단일 스레드 부분 처리가 완료 될 때, 다른 스레드가 대기하고 싶은 경우  java.lang.Object 클래스의 wait/ notify/ notifyAll 메소드를 사용함



### wait()

- wait 메소드는 notify 또는 notifyAll 메소드가 호출 될 때까지 처리를 대기하는 메소드 
- wait 메소드를 호출하는 스레드가 해당 개체의 잠금을 획득해야 함



### notify()/ notifyAll()

- notify 및 notifyAll 메소드는 wait 메소드에 의해 대기 상태에 있는 스레드의 실행을 시킴
- notify 메소드는 wait pool에서 하나의 스레드를 실행
- 실행되는 thread는, Java 가상머신에 의해 임의로 선정 프로그램에서 지정할 수 없음
- notifyAll 메소드는 wait pool의 모든 스레드를 실행 
- notify 및 notifyAll 메소드를 실행하기 위해서는, wait 메소드의 경우와 같이 해당 객체의 락을 취득해야함 



### synchronized[베타제어] 

- 여러개의 스레드가 동시에 실행되는 것을 막기 위한 키워드

- 메소드 선언부분

  ```java
  public void prn(){
    //thread 실행
  }
  ```

- 메소드 구현 부분

  ```java
  public void prn(){
    synchronized(this){
    	//thread 실행
    }
  }
  ```

  

### join()

- 나를 제외한 나머지를 우선 중지함
- 예제 

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geo8m7nibkj30ua0qm0xq.jpg" alt="image-20200511100857116" style="zoom:33%;" /> 

- 모든 메서드가 동시에 join()을 걸면 데드락(Dead Lock)상태에 빠짐 
  - <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geo8npukr8j316m0u0thb.jpg" alt="image-20200511101023292" style="zoom:33%;" /> 

- 시간으로 제어하면 각각 join() 실행 시작점을 조절할 수 있음
  - <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geo8ojf285j315s0u0djj.jpg" alt="image-20200511100827040" style="zoom:33%;" /> 

### 실습

```java
package com.test03;

public class ThreadTest {
	public static void main(String[] args) {
		ShareData sd = new ShareData();
		Thread t1 = new Thread(new Producer(sd),"First Thread");
		Thread t2 = new Thread(new Consumer(sd),"Second Thread");
		t1.start();
		t2.start();
	}
}

```



```java
package com.test03;

class Producer implements Runnable{
	
	ShareData sd;
	public Producer(ShareData sd) {
		this.sd = sd;
	}
	@Override
	public void run() {
		for(int i=0; i<100; i++) {
			char c = (char) (Math.random()*26+'A');
			sd.push(c);
		}
		
	}//run() end 
}//Producer end 

class Consumer implements Runnable {
	ShareData sd;

	Consumer(ShareData sd) {
		this.sd = sd;
	}

	@Override
	public void run() {
		for (int i = 0; i < 100; i++) {
			sd.pop();
		}
	}
}//Consumer end 

public class ShareData {
	int stackPointer; // 배열의 인덱스를 지정하는 변수
	char[] stack = new char[100];// 문자 100개 정도 받을 수 잇는 변수

	public ShareData() {
		// TODO Auto-generated constructor stub
	}

	public synchronized char push(char data) {
		this.notify();
		stack[stackPointer] = data;
		stackPointer++;
		System.out.println(Thread.currentThread().getName() + " push : " + data);
		return data;
	}

	public char pop() {
		synchronized (this) {
			if(stackPointer<1) {
				try {
					System.out.println("stack 비었음!");
					this.wait();
				} catch (InterruptedException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			
			stackPointer--;
			char data = stack[stackPointer];
			System.out.println(Thread.currentThread().getName() + " pop : " + data);
			return data;

		}

	}

}//ShareData end 

```

#### 실행결과

```tex
stack 비었음!
First Thread push : Z
First Thread push : C
First Thread push : X
First Thread push : W
First Thread push : U
First Thread push : E
First Thread push : S
First Thread push : H
First Thread push : S
First Thread push : Y
First Thread push : R
First Thread push : H
First Thread push : M
First Thread push : H
First Thread push : U
First Thread push : D
First Thread push : F
First Thread push : S
First Thread push : M
First Thread push : E
First Thread push : I
First Thread push : D
First Thread push : B
First Thread push : T
First Thread push : N
First Thread push : D
Second Thread pop : D
Second Thread pop : N
Second Thread pop : T
Second Thread pop : B
Second Thread pop : D
Second Thread pop : I
Second Thread pop : E
Second Thread pop : M
Second Thread pop : S
Second Thread pop : F
Second Thread pop : D
Second Thread pop : U
Second Thread pop : H
Second Thread pop : M
Second Thread pop : H
Second Thread pop : R
Second Thread pop : Y
Second Thread pop : S
Second Thread pop : H
Second Thread pop : S
Second Thread pop : E
Second Thread pop : U
Second Thread pop : W
Second Thread pop : X
Second Thread pop : C
Second Thread pop : Z
stack 비었음!
First Thread push : R
Second Thread pop : R
stack 비었음!
First Thread push : U
First Thread push : K
First Thread push : F
First Thread push : M
First Thread push : E
First Thread push : E
First Thread push : K
First Thread push : Z
First Thread push : W
First Thread push : L
First Thread push : I
First Thread push : Z
First Thread push : Z
First Thread push : O
First Thread push : T
First Thread push : N
First Thread push : M
First Thread push : F
First Thread push : I
First Thread push : T
First Thread push : V
First Thread push : F
First Thread push : C
First Thread push : N
First Thread push : O
First Thread push : I
First Thread push : P
First Thread push : H
First Thread push : Z
First Thread push : G
First Thread push : C
First Thread push : A
First Thread push : I
First Thread push : H
First Thread push : L
First Thread push : N
Second Thread pop : N
Second Thread pop : L
Second Thread pop : H
Second Thread pop : I
Second Thread pop : A
Second Thread pop : C
Second Thread pop : G
Second Thread pop : Z
Second Thread pop : H
Second Thread pop : P
Second Thread pop : I
Second Thread pop : O
Second Thread pop : N
Second Thread pop : C
Second Thread pop : F
Second Thread pop : V
Second Thread pop : T
Second Thread pop : I
Second Thread pop : F
Second Thread pop : M
Second Thread pop : N
Second Thread pop : T
Second Thread pop : O
Second Thread pop : Z
Second Thread pop : Z
Second Thread pop : I
Second Thread pop : L
Second Thread pop : W
Second Thread pop : Z
Second Thread pop : K
Second Thread pop : E
Second Thread pop : E
Second Thread pop : M
Second Thread pop : F
Second Thread pop : K
Second Thread pop : U
stack 비었음!
First Thread push : D
Second Thread pop : D
stack 비었음!
First Thread push : B
First Thread push : K
First Thread push : B
First Thread push : Z
First Thread push : S
First Thread push : Z
First Thread push : D
First Thread push : D
First Thread push : O
First Thread push : Q
First Thread push : W
First Thread push : Q
First Thread push : O
First Thread push : U
First Thread push : S
First Thread push : J
First Thread push : E
First Thread push : R
First Thread push : T
First Thread push : X
First Thread push : A
First Thread push : U
First Thread push : E
First Thread push : A
First Thread push : O
First Thread push : Z
First Thread push : Z
First Thread push : N
First Thread push : B
First Thread push : M
First Thread push : H
First Thread push : W
First Thread push : V
First Thread push : J
First Thread push : W
First Thread push : R
Second Thread pop : R
Second Thread pop : W
Second Thread pop : J
Second Thread pop : V
Second Thread pop : W
Second Thread pop : H
Second Thread pop : M
Second Thread pop : B
Second Thread pop : N
Second Thread pop : Z
Second Thread pop : Z
Second Thread pop : O
Second Thread pop : A
Second Thread pop : E
Second Thread pop : U
Second Thread pop : A
Second Thread pop : X
Second Thread pop : T
Second Thread pop : R
Second Thread pop : E
Second Thread pop : J
Second Thread pop : S
Second Thread pop : U
Second Thread pop : O
Second Thread pop : Q
Second Thread pop : W
Second Thread pop : Q
Second Thread pop : O
Second Thread pop : D
Second Thread pop : D
Second Thread pop : Z
Second Thread pop : S
Second Thread pop : Z
Second Thread pop : B
Second Thread pop : K
Second Thread pop : B
```

