# Day58

## 학습목표

1. jQuery의 이벤트를 사용하여 레이아웃을 설계 및 제어 할 수 있다.

-jQuery01.html : jquery의 이벤트 연동 구현
					click / bind / on
-jQuery02.html : css 핸들링	
					addClass / removerClass
-jQuery03.html : show / hide

-jQuery04.html	: innerHTML / innerText (js)

-jQuery05.html	: innerHTML / innerText (jQuery)

-jQuery06.html	: mouseover / mouserout / attr	

-jQuery07.html	: animate 연동 	

-jQuery08.html	~ jQuery10.html : json 연동 


​			
2. jquery의 JSON 사용법을 숙지 할 수 있다.
3. ajax+jquery를 이용하여 서버페이지와 통신할 수 있다.



## ajax

- Ajax(Asynchronous Javascript + XML)
- javascript에 의한 비동기적인(Asynchronous) 통신으로 XML기반의 데이터를 클라이언트인 웹브라우저와 서버 사이에서 교환하는 방법 

### Ajax의 4가지 구성요소

- 자바스크립트, XMLHttpRequest, DOM, CSS

1. 자바 스크립트 
   - 유저 이벤트 처리 
   - 이벤트를 연동하게 되면 XMLHttpRequest 객체를 사용해서 웹서버에 요청을 전송
   - XMLHttpRequest 객체로부터 응답이 오면 DOM, CSS등을 사용해서 화면을 조작
2. XMLHttpRequest:
   - 서버와 데이터 교환
   - 웹서버와 통신을 담당 
   - requset, response를 가짐 
3. DOM(Document Object Model):
   - 문서 정보(폼 등)
   - 문서의 구조를 나타냄
   - 폼등의 정보나 화면 구성을 조작할 때 사용
4. CSS :
   - UI 구성 
   - 글자색, 배경색, 위치, 투명도 등 UI와 관련된 부분을 담당



> XMLHttpRequest?
>
> - Ajax에서 서버와 클라이언트간에 통신을 담당하는 것으로 가장 중요한 객체
> - 서버의 응답결과가 HTML이 아닌 XML 또는 단순 텍스트 
> - 페이지 이동 없이 결과가 해당 화면에서 바로 반영됨 



### 객체의 프로퍼티를 이해

- onreadystatechange 
  - 상태의 변경이 발생했을 때 이벤트를 처리하기 위해 이벤트 핸들러를 기술
  - 일반적으로 이벤트 핸들러는 자바스크립트의 함수로 기술
- requestText
  - 문자열로 이루어진 서버의 응답을 받음 
- status
  - 서버로부터 응답받는 HTTP상태코드로 숫자를 리턴ㄴ
  - 200: 정상(ok)
  - 404: 요청한 페이지를 찾을 수 없음(page not found)
  - 500: 서버에러(Internal Server Error)
  - 대표적으로 2xx는 성공을, 4xx는 클라이언트 오류, 5xx는 서버의 오류를 나타냄
- readyStatus : 요청객체의 상태를 리턴
  - 0: uninitialized - open() 메서드가 아직 호출되지 않은 상태
  - 1: open - send() 메서드가 아직 호출되지 않은 상태 
  - 2: sent - send() 메서드의 호출이 끝나서, 헤더와 status의 사용이 가능한 상태이며 로드완료
  - 3: receiving - 일부 데이터를 받을 수 있는 상태로 처리 중
  - 4: loaded - 모든 데이터를 받을 수 있는 상태로 완료 
- requestXML : XML로 이뤄진 서버의 응답을 받음 
  - 파신이 가능한 W3C DOM 노드 트리 구조의 메소드와 프로퍼티를 XML 문서 객체로 리턴 받음
- statusText : 서버로부터 HTTP상태를 문자로 리턴 받음
  - 예를 들어 "Not Found", "OK"

### XMLHttpRequest를 이용한 웹 서버 요청 기술 

- open() 함수 : 요청의 초기화, get, post 선택, 접속할 URL 입력 

- send() 함수 : 웹서버에 요청 

- ex) get 방식 전달 

  ```javascript
  hr = XMLHttpRequest();
  hr.open("GET", "/text.txt", true);
  //or
  hr.open("GET","/test.jsp?id=raeok&pwd=1234",true);
  hr.onload=function(){...}
  hr.send(null);
  ```

- ex) post 방식 전달

  ```javascript
  hr = XMLHttpRequest();
  hr.open("POST", "/text.txt", true);
  hr.onload=function(){...}
  hr.send("id=raeok&pwd=1234");
  ```

  