# Day17

## 학습 목표 

1. java.io.* 사용해서 입출력을 연동할 수 있다. 
2. byte, char, Object 타입을 입출력을 구분할 수 있다.
3. Object 입출력을 통해 직렬화를 이해할 수 있다. 



## java.io.* 을 사용한 입출력

### Byte 단위 입출력(Stream) 

- inputStream / OutputStream 두개의 추상 클래스가 있음 
- inputStream - int read()
- OutputStream - write(byte)
- 이미지, 동영상, 네트워크 자료전송



### try-with-resources Statement (Java SE 7부터 추가)

![image-20200507111142491](https://tva1.sinaimg.cn/large/007S8ZIlgy1gejny9ozvwj30sq06gdgj.jpg)

- [The try-with-resources Statement tutorial](https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html)
- try 가로 안에서 객체를 생성하면, try문이 끝나면 생성된 객체도 소멸됨 -> finally{**객체.close()**} 안해도 됨 
- 꼭 close() 메서드가 있는 클래스만 객체 생성해야함 

```java
package com.test;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;

public class ByteTest02 {
	public static void main(String[] args) {
		int readDt = 0;
		try (FileInputStream fi = new FileInputStream(new File("mycat.jpg"));
				FileOutputStream fo = new FileOutputStream(new File("mycat_res02.jpg"));) {
			while ((readDt = fi.read()) != -1) {
				fo.write(readDt);
			} // while end
		}//try end : try 가로 안에서 생성된 객체들(fi, fo)은 try 영역이 끝나면 소멸 됨 -> close 안해도 됨  
		catch (FileNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} // try ~ catch end

	}// main() end
}//ByteTest02 end 
```



### Data 리터럴

- data 리터럴을 쓰고 읽겠다 -> 지정된 값(data03.dat)을 연속적으로 처리할 때 사용하는 클래스 
- DataOutputStream(OutputStream), DataInputStream(InputStream) 

![image-20200507135352167](https://tva1.sinaimg.cn/large/007S8ZIlgy1gejsn29cthj30ul0rs4j6.jpg)