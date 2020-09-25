# Spark



## MapReduce/Hive 장단점

### 장점 

- 빅데이터 시대를 열어준 선구적인 기술
- 거대한 데이터를 안정적으로 처리 
- 많은 사람들이 이용 중

### 단점

- 오래된 기술이다보니, 발전이 느리다 
- 불편한 점이 많다

### MapReduce 문제점

- MapReduce는 Map의 입출력 및 Reduce의 입출력을 매번 HDFS에 쓰고, 읽는다  

- MapReduce 코드는 작성하기 불편하다 

  

## RDD(Resilient Disributed Data)

- 탄력적으로 분산된 데이터셋
- 클러스터에 분산된 메모리를 활용하여 계산되는 List
  - 인메모리(메모리 안에 메모리를 만들어서 작업 )
- 데이터를 어떻게 구해낼지를 표현하는 Transformation을 기술한 Lineage(계보)를 interactive하게 만들어낸 후, Action을 통해 lazy하게 값을 구해냄
- 클러스터 중 일부의 고장 등으로 작업이 중간에 실패하더라도, Lineage를 통해 데이터를 복구



## Spark Stack

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gj1q5tv8p1j30k00a9dif.jpg" alt="Apache Spark 소개 - 스파크 스택 구조" style="zoom:50%;" /> 

## 장점

- 시간과 비용을 아껴줌
  - 수십대의 Hadoop Cluster를 10대 이하의 Cluster로 대체할 수 있음
  - 수십분 기다려야 하던 작업이 1분만에 완료됨
- 작업 능률 향상
  - MR 작업 코드 만들고 패키징하고 submit하고 하던 복잡한 과정이 shell에서 코드 한 줄 치는 것으로 대체됨
  - 처음 접하는 사람도 배우기 쉬움
- 다양한 제품을 조합해야 했던 작업이 Spark로 다 가능 



## 예제

![image-20200925102706993](https://tva1.sinaimg.cn/large/007S8ZIlgy1gj2n1cc98rj31040f6124.jpg)

```python
from pyspark.sql import SQLContext, Row
from pyspark.sql.types import *
import json, os, datetime, collections

mytext= sc.textFile("hdfs://192.168.56.105:8020/user/myspark/click_data_sample.csv")
header = mytext.first()

res = mytext.filter(lambda x:x != header).map(lambda line: line.split(",")).map(lambda line:[datetime.datetime.strptime(line[0].replace('"',''),'%Y-%m-%d %H:%M'), int(line[1]), line[2].replace('"','')])
res.take(3)
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gj2mx6a4tfj30ea030aat.jpg" alt="image-20200925102304737" style="zoom:33%;" /> 

![image-20200925102626108](https://tva1.sinaimg.cn/large/007S8ZIlgy1gj2n0m8dwqj31h8030ju5.jpg)



```python
#user schema  생성  
fields = [StructField("access_time", TimestampType(), True),
           StructField("userID", IntegerType(), True),
          StructField("campaignID", StringType(), True)]
schema = StructType(fields)

res_df= sqlContext.createDataFrame(res, schema)  #data  맵핑  
res_df
print( res_df.count())
print (res_df.printSchema())
print (res_df.dtypes)
print (res_df.show(5))
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gj30fey9b0j30de02st99.jpg" alt="image-20200925181024229" style="zoom:33%;" /> 

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gj30fruakvj30my09aq60.jpg" alt="image-20200925181047221" style="zoom:33%;" /> 

<img src="../../../Library/Application Support/typora-user-images/image-20200925181237050.png" alt="image-20200925181237050" style="zoom:33%;" /> 

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gj30serfkdj30km0eewkf.jpg" alt="image-20200925182255971" style="zoom: 33%;" />  



- csv 파일

```python
res_df_2 = sqlContext.read.format("com.databricks.spark.csv").option("header", "true").load("hdfs://192.168.56.105:8020/user/myspark/click_data_sample.csv")
res_df_2 
print (res_df_2.printSchema())
print (res_df_2.show(5))

>>res_df_3 = sqlContext.read.format("com.databricks.spark.csv").option("header", "true").option("inferSchema", "true").load("/user/myspark/click_data_sample.csv")
>>print (res_df_3.printSchema())
```

