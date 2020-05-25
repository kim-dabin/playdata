# Day28

## 학습목표

1. JDBC connection을 살펴벼고 각 흐름을 숙지할 수 있음
2. Join을 사용하여 Entity의 조건 병합에 대해서 살펴보고 활용할 수 있음
3. 분산함수를 살펴보고 활용할 수 있음





## JDBC connection

### 순서

1. Class.forName() -> 드라이버 연결
2. Connection conn = DriverManager.getConnection() -> db연결
3. Statement stmt = conn.createStatement() -> 명령 준비
4. 쿼리를 실행해서 결과를 리턴
   - 리턴 타입 : [boolean, int[], ResultSet, int]
   - boolean  stmt.execute(String sql) 
     - 지정된 쿼리의 실행 유무를 리턴
   - int[] stmt.executeBatch()  
     - 추가된 쿼리들을 일괄[insert,delete, update]로 실행한 결과를 리턴 
   - ResultSet stmt.executeQuery(String sql) 
     - 지정된 쿼리 중 SELECT 쿼리를 테이블의 데이터로 저장해서 리턴 
   - int stmt.executeUpdate(String sql)
     - [insert,delete, update] 실행결과 
5. SELECT 쿼리를 ResultSet의 getDataType형 메소드로 결과를 컬럼형으로 리턴 받음



### JDBC + MVC 

- Model : 연산, 디비처리등의 비지니스 로직 (biz+dao)
- View : 화면 설계단 (회원가입화면, 게시판 화면)
- Controller : view에서 입력받은 값들을 조건에 따라 모델로 전달한 후 결과를 다시 view로 리턴하는 기능[데이터 검증 후 로직을 제어]

![image-20200525101415281](https://tva1.sinaimg.cn/large/007S8ZIlgy1gf4fg3ziw9j321o0rs1ii.jpg)