# Day20

## 학습목표

1. 서버와 클라이언트 구조를 이해할 수 있다.
2. Web Server와 Web Application Server를 구별 할 수 있고 설명할 수 있다. 
3. java.net의 클래스들을 이용하여 udp, tcp 구조를 설계할 수 있다. 
4. 스트림을 이용하여 메세지 전달을 Server와 Client간에 주고 받을 수 있다. 



# WAS

- handler : 이벤트를 수행하는 기능 

```xml
handlers = 1catalina.org.apache.juli.AsyncFileHandler, 2localhost.org.apache.juli.AsyncFileHandler, 
3manager.org.apache.juli.AsyncFileHandler, 
4host-manager.org.apache.juli.AsyncFileHandler, 
java.util.logging.ConsoleHandler
```



### WAS 구조

- bin : 톰캣 실행 파일

- conf: 환경설정 파일 

  - server.xml[서버]
  - context.xml[프로젝트 설정파일]
  - web.xml[웹페이지 설정파일]

- lib : jar 파일 -> java를 확장한 클래스들을 패키지로 만들어 참조 파일로 구성 

  - 종류: was를 연동, .java -> class -> 웹프라우저 연동(servlet/jsp), 소켓 통신 연동, 태크 연동 등

- logs : 기록 파일 

  - conf/logging.properties에 의해 설정된 파일 형식이 저장됨 
  - ex) 속성파일에 핸들러 추가 후 사용자 로그파일을 생성하게 됨

- temp : 임시폴더 

- webapps : 웹 어플리케이션 저장소 

  - webapps/WEB-INF/web.xml
  - webapps/WEB-INF/lib/추가되는 라이브러리
  - webapps/META-INF/context.xml
  - webapps/img
  - webapps/js

- work : tomcat의 컨테이너가 있는 곳(자기 스스로 일하는 곳) 

  

## Web Server 와 Web Application Server

- web project를 생성할 때, 브라우저로 요청되는 페이지 

  - 정적 페이지 - Web Server
  - 동적 페이지 - WAS

- Web Server의 종류

  <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gepphwa7ymj30g207ojsa.jpg" alt="image-20200512163834130" style="zoom:33%;" /> 

- WAS 종류 

  - BEA - Web Logic, IBM - Web Sphere, T-max - Jeus, Apache - Tomcat



## Java.net

### URI/URL

- java.net.URI

  - Uniform Resource Identifier (URI,통합 자원 식별자)

  - 인터넷에 있는 자원을 나타내는 유일한 주소

  - ```tex
    [scheme:][//authority][path][?query][#fragment]
    ```

  - https://127.0.01:8080/MyTest/login.jsp?id=big5&pwd=admin1234#a.java

  - file://Library/Tomcat/webapps/MyTest/b.html

- java.net.URL

  - Uniform Resource Locator (URL)
  - 브라우저에서 Http 프로토콜로만 접근(URI의 하위개념) 



- 웹페이지 java.net.URL 클래스로 읽어오는 방법 openConnection()

  - ```java
    public static void main(String[] args) throws MalformedURLException, IOException {
    		URLConnection urlc = new URL("http://localhost:8080/").openConnection();
    		BufferedReader bffer = new BufferedReader(new  InputStreamReader(urlc.getInputStream(),"UTF-8"));
    
    		int byteRead;
    
    		while ((byteRead = bffer.read()) != -1) {
    			System.out.print((char) byteRead);
    		} // while end
    
    		bffer.close();
    
    	}// main() end
    ```

    

### InetAddress

- java.net.InetAddress
- DNS서버가 가진 정보를 가진 클래스 
- Inet4Address, Inet6Address

#### 주요 메소드

- public static InetAddress[] getAllByName(String host)
- public static InetAddress getByAddress(byte[] addr)

​                                  