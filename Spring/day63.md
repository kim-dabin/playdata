# DAY63

## 학습목표

- Spring JDBC



## springJDBC

**NOTE: Within special class loading environments such as OSGi, this class is effectively superseded by [`SimpleDriverDataSource`](https://docs.spring.io/spring/docs/4.3.27.RELEASE/javadoc-api/org/springframework/jdbc/datasource/SimpleDriverDataSource.html) due to general class loading issues with the JDBC DriverManager that be resolved through direct Driver usage (which is exactly what SimpleDriverDataSource does).**

OSGI : Java 모듈의 동적 추가 및 실행을 관리하기 위한 기반 시스템

- OSGI Alliance 는 1999년에 "Open Service Gateway Initiative" 명칭으로 설립
- 게이트웨이 장체이서 실행되는 서비스 프로그램의 실행 기분을 목적으로 함 
  - java + db [jdbc] = simple
- java -> spring + db[jdbc] = Drivdhh
  - [가이드](https://docs.spring.io/spring/docs/4.3.27.RELEASE/spring-framework-reference/htmlsingle/#jdbc-JdbcTemplate)

### 실습

#### test01/test02

1. SpringBeans.xml

   org.springframework.jdbc.datasource.DriverManagerDataSource를 이용해서 DB 드라이버 연결 

2. JdbcDaoSupport의 setDataSource()로 DB를 연결

3. JdbcTemplate 

   getJdbcTemplate() 연결한 상태에서 쿼리 객체를 리턴하는 메소드를 호출

4. 리턴받은 JdbcTemplate의 객체를 통해 update()등의 메소드로 쿼리를 실행

5. JdbcTemplate.update() / update/insert/delete

6. JdbcTemplate.query() / select / update / insert/ delete의 쿼리를 받아서 실행

7. JdbcTemplate.queryforObject()/select 형의 object

8. JdbcTemplate.queryforInt()/ query의 return type이 int



#### test03[ORM (xml, class[@], class 객체 메소드)]

- goods-mapping.xml[sql]
- mybatis-config.xml[환경설정]
- beans.xml
- jdbc.properties



#### test04

- @, query로 매퍼를 작성 
  - [참고](https://mybatis.org/mybatis-3/ko/java-api.html)



### ORM Object Relational Mapping (ORM) Data Access

- [20.가이드](https://docs.spring.io/spring/docs/4.3.27.RELEASE/spring-framework-reference/htmlsingle/#orm)

- 환경설정.xml - config.xml

  - db 연결코드

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
    PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-config.dtd">
  <configuration>
    <environments default="development">
      <environment id="development">
        <transactionManager type="JDBC"/>
        <dataSource type="POOLED">
          <property name="driver" value="${driver}"/>
          <property name="url" value="${url}"/>
          <property name="username" value="${username}"/>
          <property name="password" value="${password}"/>
        </dataSource>
      </environment>
    </environments>
    <mappers>
      <mapper resource="org/mybatis/example/BlogMapper.xml"/>
    </mappers>
  </configuration>
  ```

- jdbc.properties

- SpringBeans.xml

- sql구문.xml(SQLMapXML.xml)

- case 1 

  - (jdbc.properties + SpringBeans.xml)
  - 환경설정.xml - config.xml, sql구문.xml(SQLMapXML.xml)

- case 2 :

  - jdbc.properties 
  - 환경설정.xml - config.xml
  - sql구문.xml 

- case3 : 

