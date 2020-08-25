# DAY70

## 학습목표



## Mongodb v1.x

- **[MongoTemplate](https://docs.spring.io/spring-data/mongodb/docs/1.9.9.RELEASE/api/org/springframework/data/mongodb/core/MongoTemplate.html#MongoTemplate-com.mongodb.Mongo-java.lang.String-)**(com.mongodb.Mongo mongo, [String](https://docs.oracle.com/javase/6/docs/api/java/lang/String.html?is-external=true) databaseName)
  - [MongoDbFactory](https://docs.spring.io/spring-data/mongodb/docs/1.9.9.RELEASE/api/org/springframework/data/mongodb/MongoDbFactory.html) 은 권한 설정 -> 샤딩 작업할 때 불필요해서 2,3버전은 지원 안함 -> 사용 자제 등  



```java
Person [id=5f3dcda5560dc2acfbf07e56, name=aaa, age=20, address=[Address [email=a@a.com, addr=aaa, tel=aaaa, addressType=CHECKING], Address [email=222@2.com, addr=bbb, tel=bbbb, addressType=SAVINGS], Address [email=c@c.com, addr=ccc, tel=cccc, addressType=CHECKING]], book=[com.DTO.Book@13b3d178, com.DTO.Book@24c4ddae, com.DTO.Book@37fb0bed]]
```



- [*Example 37. Using a PagedResourcesAssembler as controller method argument*](https://docs.spring.io/spring-data/mongodb/docs/1.9.9.RELEASE/reference/html/#repositories.query-streaming)

```java
@Controller
class PersonController {

  @Autowired PersonRepository repository;

  @RequestMapping(value = "/persons", method = RequestMethod.GET)
  HttpEntity<PagedResources<Person>> persons(Pageable pageable,
    PagedResourcesAssembler assembler) {

    Page<Person> persons = repository.findAll(pageable);
    return new ResponseEntity<>(assembler.toResources(persons), HttpStatus.OK);
  }
}
```



- [*Example 38. Data defined in JSON*](https://docs.spring.io/spring-data/mongodb/docs/1.9.9.RELEASE/reference/html/#repositories.query-streaming)

```javascript
[ { "_class" : "com.acme.Person",
 "firstname" : "Dave",
  "lastname" : "Matthews" },
  { "_class" : "com.acme.Person",
 "firstname" : "Carter",
  "lastname" : "Beauford" } ]
```

- [*Example 39. Declaring a Jackson repository populator*](https://docs.spring.io/spring-data/mongodb/docs/1.9.9.RELEASE/reference/html/#repositories.query-streaming)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:repository="http://www.springframework.org/schema/data/repository"
  xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/data/repository
    http://www.springframework.org/schema/data/repository/spring-repository.xsd">

  <repository:jackson2-populator locations="classpath:data.json" />

</beans>
```





## 파일처리

1. mongofiles 명령을 이용해서 원하는 db에 파일을 처리할 수 있음
2. put, get, list, search, delete를 options과 함께 사용할 수 있음
   - -d(db) -v(이중화)
3. 파일처리를 할 수 있는 크기는 16M
   - 16M가 넘어가면 GridFS로 처리됨
4. GridFS는 파일처리를 Mongo로 데이터 분할 수 복제 다음 분산 작업으로 입출력 함



### 단일 파일 올리고 내리기 

- 파일 입력 

  ```tex
  $ mongofiles -v -d images put a.jpeg 
  ```

  ![image-20200820142707319](https://tva1.sinaimg.cn/large/007S8ZIlgy1ghx7o0mi8rj31am06o44e.jpg)



- 파일 확인

  ```tex
  $ mongofiles -d images list
  ```

  <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ghx7oz9ewzj30u605iacq.jpg" alt="image-20200820142806159" style="zoom:50%;" /> 



- fs.chunks, fs.files

  ![image-20200820143159157](https://tva1.sinaimg.cn/large/007S8ZIlgy1ghx7t11k71j31pc0fe7g1.jpg)

  - fs.files : meta data
  - fs.chunks: 파일 내용

- 파일 내려받기

  ```tex
  $ mongofiles -v -d images get a.jpeg
  ```

  ![image-20200820144516443](https://tva1.sinaimg.cn/large/007S8ZIlgy1ghx86uog08j31bk0aujyy.jpg)

- 파일 삭제

  ```tex
  $ mongofiles -v -d images delete a.jpeg
  ```

  ![image-20200820144642672](https://tva1.sinaimg.cn/large/007S8ZIlgy1ghx88ck1esj31bk06owky.jpg)

- 파일 검색

  ```tex
  $ mongofiles -v -d images search a.jpeg
  $ mongofiles -v -d mytxt search a.txt
  ```

  ![image-20200820145231798](https://tva1.sinaimg.cn/large/007S8ZIlgy1ghx8eedbddj31bk0c048q.jpg)

 

### mongoimport

```tex
mongoimport
	-h (mongolab으로 지정되는 URL)
	-d testdb
	-c test
	--file (현재 JSON 파일)
	--type json,csv,tsv
	--field
```

- json, csv[comma - separated values], tsv[tab-separated values]를 처리함

```tex
mongoimport
-h[--host], -u[--username] -p[--password] -d[--db]
-c[--collection], -f[--fields] --file --drop --out
```



> 1) myScore db에 있는 Score컬렉션을 score.json으로 내려받자

```tex
$ mongoexport --db myScore -c Score --out mscore.json
```

![image-20200820153048010](https://tva1.sinaimg.cn/large/007S8ZIlgy1ghx9i873q0j31qw0kwx1a.jpg)



> 2) myScore db에 있는 Score 컬렉션을 score.json으로 내려받자

```tex
$ mongoexport --db test -c person --out person.json
```

![image-20200820153234204](https://tva1.sinaimg.cn/large/007S8ZIlgy1ghx9k766zij31re0agqge.jpg)

> 3) myScore db에 있는 Score 컬렉션을 score.csv으로 내려받자



> 4) myScore db에 있는 Score 컬렉션을 score.tsv으로 내려받자



> 5) myaddr.json을 newdbdml addr_test라는 컬렉션으로 import한다

```tex
$ mongoimport -d newdb -c addr_test --file myaddr.json
```



> 6) addr_test라는 컬렉션을 myexport.json으로 export 한다

```tex
$ mongoexport -d newdb -c addr_test --out myexport.json
```



> 7) addr_test라는 컬렉션을 mytsv.tsv로 export 한다.

```tex
$ mongoexport -d newdb -c addr_test --out myexport.tsv
```



> 8) myScore 의 Score 컬렉션의 필드 "name"만 myS.tsv로 내려받자 

```tex
$ mongoexport -d myScore -c Score --out myS.tsv -f name
```



> 9) myScore의 Score 컬렉션의 이름과 영어만 myS02.tsv로 내려받자 

```tex
$ mongoexport -d myScore -c Score --out myS02.tsv -f name,eng
```



> 10) $ mongoexport -d myScore -c Score -o myS03.json --jsonArray



> 11) myS.tsv를 myScore db에 resScore로 올리고 resScore.json으로 내려받자 

```tex
$ mongoimport -d myScoredb -c resScore --file myS.tsv
$ mongoexport -d myScoredb -c resScore --out resScore.json
```



> 12) myaddr.json을 확장자만 myaddr.txt로 저장한 다음 newdb에 myAddress로 올려보자 

```tex
$ mongoimport -d newdb -c myAddress --file myaddr.txt 
```



### Mongo DB 관리자) admin, config, local

- mongdb system 확인

  <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ghx8gxgunrj30v00as43b.jpg" alt="image-20200820145458086" style="zoom:50%;" /> 





