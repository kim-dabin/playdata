# DAY70

1.pretty() : 컬렉션을 row 단위로 출력
	db.Score.find().pretty()
	
2. db.Score.find()({"test",:"term"}).pretty()
		
>db.Score.find()({"test",:"midterm"}).pretty()
		
3. ensuerIndex()

db.Score.ensuerIndex({"test":1,"description":-1})
indexSpec : 인덱스를 작성할 문서의 필드를 지정
indexName : 이 인수는 인덱스의 이름을 지정, 이름을 지정하지 않으면 이름이 생성
expirefterSeconds : 선택적. 이 콜렉션의 문서가 삭제되는 경과 시간 (초)을 지정
exireAt : 선택적.
unique (true | false) : 인덱스가 고유한지 여부를 지정

indexOptions : 다음 옵션 중 하나를 포함할 수 있다. 
name : 덱스의 이름을 지정
array (true | false) : 요소 배열을 지정 
unique (true | false) : 인덱스가 고유한지 여부를 지정 


////////////////////////////////////////////

db.Score.createIndex(keypattern[,options])
db.Score.createIndexs(keypatterns[,<options>])
db.Score.dropIndex(index) - e.g.db.Score.dropIndex( "indexName" ) 
							or db.Score.dropIndex( {"indexKey" : 1 } )
db.Score.dropIndexes() : 복제 된 상태 또는 다중 속성의 인덱스 삭제 
db.Score.ensureIndex(keypattern[,options])
db.Score.explain().help() - show explain help
db.Score.reIndex() - 인덱스 변경 

////////////////////////////////////////////


ex) 'test' 필드에 오름차순으로 인덱스를 작성하자

1.데이터를 생성 
use IndexTest

db.Test01.insert({"user":"kim","score":90})
db.Test01.insert({"user":"lee","score":100})
db.Test01.insert({"user":"park","score":45})
db.Test01.insert({"user":"ji"})

2.score 필드를 색인으로 지정하자 
db.Test01.ensureIndex({score:1},{sparse:false})

3.색인을 확인하자. (기본 색인 _id와 score필드가 색인지정 확인)
db.Test01.getIndexes();

4.기존Score 컬렉션의 인덱스를 확인해보자.
use myScore
db.Score.getIndexes()

5.Test01의 score 필드를 사용해서 sort()를 해보자.
use IndexTest
db.Test01.find().sort({score:1})

---> spase:false 를 지정했을 경우 필드가 없는 문서에 null로 지정된다. 
		//ji는 score 값이 없기 때문에 null로 처리된다. 
---> unique와 함께 사용하게 되면 유일한 값을 이용해서 색인하게 된다. 		

6.score 필드에 지정한 인덱스를 삭제 해보고 확인 해보자. 
db.Test01.dropIndex({score:1})
db.Test01.getIndexes()//확인해보기 

7. 인덱스의 TTL로 지정해 놓자
db.log.ensureIndex({"status":1},{expireAfterSeconds:60})
db.log.insert({"status":new Date(),"logEvant":2,"logMessage":"Error occurred,404"})

	-TTL(Time to Live) 색인 : 특정시간이 지나면 문서를 자동 삭제할 경우 사용 
	  						로그데이터, 세션정보 등을 관리한다.
	  ex) 지정된 시간 이후 문서를 자동으로 삭제 하고싶다.



```tex
src 

com.test.controller 
com.test.dao
com.test.domain
com.testutillty[mongo설정파일]
	-DBUtility.java
	-SpringMongoConfig.java
config.properties[정형db설정파일]
```





```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:p="http://www.springframework.org/schema/p"
	xmlns:mongo="http://www.springframework.org/schema/data/mongo"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.3.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://www.springframework.org/schema/data/mongo http://www.springframework.org/schema/data/mongo/spring-mongo-1.8.xsd">
<context:annotation-config />
<mvc:annotation-driven/>
   <context:component-scan
      base-package="com.test.*">
      <context:exclude-filter type="annotation"
         expression="org.springframework.context.annotation.Configuration" />
   </context:component-scan>


   <bean id="mongoTemplate"
      class="org.springframework.data.mongodb.core.MongoTemplate">
      <constructor-arg name="mongo" ref="mongo" />
      <constructor-arg name="databaseName" value="test" />
   </bean>

   <bean id="mongo"
      class="org.springframework.data.mongodb.core.MongoFactoryBean">
      <property name="host" value="localhost" />
   </bean>

   <bean
      class="org.springframework.dao.annotation.PersistenceExceptionTranslationPostProcessor" />
</beans>

```

