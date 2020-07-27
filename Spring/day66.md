# DAY66



## HTTP OPTION

- PUT 
  - 서버에서 resource의 업데이트 하거나 resource가 없다면 새로운 resource를 생성해 달라고 요청한다.
  - 모든 필드를 호출 후 수정한다
  - 회원정보 수정 등 
  - [전체 메모리 로드 후 수정]

- PATCH 
  - 서버에게 resource의 업데이트를 요청한다.
  - [부분 데이터를 업데이트할 때 사용]
- DELETE
  - 서버에게 resource의 삭제를 요청할 때 사용.

- GET
  - 폼의 데이터를 쿼리 스트링과 함께 페이지에 요청한다.

- POST
  - 폼의 데이터를 쿼리 스트링 없이 바이너리로 변환하여 서버에 요청한다.



## @ResponsBody

- Http요청의 본문 body 부분이 그대로 전달됨 
- @ResponsBody가 메서드 레벨에 부여되면 메서드가 리턴하는 오브젝트 뷰를 통해 결과를 만들어내는 모델로 사용하는 대신, 메세지 컨버터를 통해 바로 HTTP응답의 메세지 본문으로 변환됨 
- 즉, 요청한 형태에 맞춰서 메세지 변환기를 통해 결과값을 반환
- [spring2.0~] ContentNegotiatingViewResolver와는 동작방식이 다름 
- ContentNegotiatingViewResolver는 등록되어 있는 ViewResolver중에서 Controller 메소드의 리턴값을 통해 등록된 ViewResolver 중에서 적합한 형태로 처리해서 반환함 
- @ResponsBody는 RequestBody가 선택한 형식으로 결과값을 변환하여 반환
- ex) 컨트롤러 메서드에 @ResponsBody 쓰여지면 메서드에서 View를 통하지 않고 HTTP의 Response Body에 직접 작성됨 
- 객체가 특정형태로 변함(문자열, json, xml, 파일 등)

> @RequestBody : HTTP 요청의 body 내용을 자바 객체로 매핑