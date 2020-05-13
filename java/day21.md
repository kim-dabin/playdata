



## 학습목표 

1. java의 환경 설정 값을 살펴보고 활용할 수 있다. 
2. java.net의 클래스들을 이용하여 udp, tcp 구조를 설계할 수 있다. 
3. java.io 스트림을 이용하여 메시지 전달을 Server와 Client간에 주고 받을 수 있다. 



## java의 환경설정값

### Tomcat의 환경설정 

- catalina.properties[단일 속성 값 ] - java.util.Properties , 
- server.xml[태그를 이용해서 엘리먼트를 가진 다중 속성값]
- ==> 파일 경량, 서버 기종에 호환



### java.util.Properties

- loading key/value pairs into a Properties object from a stream,
  - 메서드 넣기 
- retrieving a value from its key,
  - 메서드 넣기 
- listing the keys and their values,
  - 메서드 넣기 
- enumerating over the keys, and
  - 메서드 넣기
- saving the properties to a stream.
  - 메서드 넣기 



- 다음과 같이 명시된 xml만 loadFromXML()로 읽어 올 수 있음 

  ```xml
  <!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
  ```



#### DTD for properties

```xml-dtd
<!ELEMENT properties ( comment?, entry* ) >

<!ATTLIST properties version CDATA #FIXED "1.0">

<!ELEMENT comment (#PCDATA) >

<!ELEMENT entry (#PCDATA) >

<!ATTLIST entry key CDATA #REQUIRED>
```


#### java.sql.DriverManager

- getConnection(String url, Properties info);

  

#### URLEncoder

- URLEncoder.encode(args[1], "UTF-8");



## java의 네트워크(java.net)

- java.net.URL
- java.net.URI
- java.net.InetAddress

### Socket

- java.net.Socket(InetAddress address, int port)

#### UDP(일괄 전송 데이터 처리)

- 예) 고지서, (실시간이 아닌) 라디오, (옛날) 네비게이션, 예약(통보) 문자
- java.net.DatagramSocket, java.net.DatagramPacket
- 주고 받을 때 : receive(DatagramPacket p)/send(DatagramPacket p)



#### TCP(실시간 처리)

- 예) 채팅

- java.net.Server(Client 역할), java.net.ServerSocket(Server 역할)

- Server

  ```java
  ServerSocket server = new ServerSocket(port);
  ```

- Client

  ```java
  Socket s = new Socket("ip", port);
  ```

- Server가 먼저 실행되고 -> Client가 실행되어야 함 

  1. Server 실행

     ```java
     ServerSocket server = new ServerSocket(port);
     ```

  2. Client 실행

     ```java
     Socket s = new Socket("ip", port);
     ```

  3. Server가 승인 

     ```java
     Socket s = server.accept();
     ```

  4. ```java
     OutputSteam out = s.getOutputStream();
     ```

  5. ```java
     InputStream is = s.getInputStream();
     ```

     

