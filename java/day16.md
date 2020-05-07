# Day16

## 학습 목표

1. java.io의 클래스들을 이해 할 수 있다.
   - stream: byte 단위로 데이터를 처리하는 경로 
   - byte(InputStream, OutputStream), char(Reader, Writer), Object = 공통 메소드 read() write() close()



## java.io



### is a / has a 

- FileInputStream 확인: FileReader의 선조인 InputStreamReader(InputStream) 호출
- FileReader -> InputStreamReader(InputStream) -> FileInputStream 호출 

![image-20200506163524725](https://tva1.sinaimg.cn/large/007S8ZIlgy1geirorpp7sj31c20m2k4a.jpg)

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geirxm9ttvj30gw0cc0u2.jpg" alt="image-20200506164355060" style="zoom:50%;" /> 



- 

![image-20200506164003082](https://tva1.sinaimg.cn/large/007S8ZIlgy1geirtlxre1j31760qaans.jpg)