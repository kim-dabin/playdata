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
  - web.xml[웹페이지 설정파일 ]

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

  