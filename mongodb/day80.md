# DAY80

## 학습목표

- 공간좌표 처리
- Replication
- Sharding



## 공간 좌표 데이터 저장 

1. 좌표 : 좌표를 나타내는 데이터, 주로 평면상의 점 표시 {loc : [10,20]}

2. GeoJSON Objects : 지구 표면에 위도 또는 경도를 다루는데 사용한다.

   <field>: { type: <GeoJSON type> , coordinates: <coordinates> }

   {loc : {type:"Point", coordinates: [10,20]}}

   | Name                                                         | Description                                                  |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [`$geoIntersects`](https://docs.mongodb.com/manual/reference/operator/query/geoIntersects/#op._S_geoIntersects) | Selects geometries that intersect with a [GeoJSON](https://docs.mongodb.com/manual/reference/glossary/#term-geojson) geometry. The [2dsphere](https://docs.mongodb.com/manual/core/2dsphere/) index supports [`$geoIntersects`](https://docs.mongodb.com/manual/reference/operator/query/geoIntersects/#op._S_geoIntersects). |
   | [`$geoWithin`](https://docs.mongodb.com/manual/reference/operator/query/geoWithin/#op._S_geoWithin) | Selects geometries within a bounding [GeoJSON geometry](https://docs.mongodb.com/manual/reference/geojson/#geospatial-indexes-store-geojson). The [2dsphere](https://docs.mongodb.com/manual/core/2dsphere/) and [2d](https://docs.mongodb.com/manual/core/2d/) indexes support [`$geoWithin`](https://docs.mongodb.com/manual/reference/operator/query/geoWithin/#op._S_geoWithin). |
   | [`$near`](https://docs.mongodb.com/manual/reference/operator/query/near/#op._S_near) | Returns geospatial objects in proximity to a point. Requires a geospatial index. The [2dsphere](https://docs.mongodb.com/manual/core/2dsphere/) and [2d](https://docs.mongodb.com/manual/core/2d/) indexes support [`$near`](https://docs.mongodb.com/manual/reference/operator/query/near/#op._S_near). |
   | [`$nearSphere`](https://docs.mongodb.com/manual/reference/operator/query/nearSphere/#op._S_nearSphere) | Returns geospatial objects in proximity to a point on a sphere. Requires a geospatial index. The [2dsphere](https://docs.mongodb.com/manual/core/2dsphere/) and [2d](https://docs.mongodb.com/manual/core/2d/) indexes support [`$nearSphere`](https://docs.mongodb.com/manual/reference/operator/query/nearSphere/#op._S_nearSphere). |

   점, 직선, 다각형 등으로 타입을 지정해서 집합 데이터를 연관시켜 필드 값을 관리한다. 

   $near : 점과 거리 좌표 (2d, 2dsphere)

   $geoWithin : 점, 선, 다각형 (2dsphere)

   $geoIntersects : 점, 선, 다각형(2dsphere)

원형

	2dsphere : 좌표
	
	2D : 평면계산(하나의 인덱스로 평면 데이터를 필드와 타입을 지정해서 사용한다.)
	
	GeoJson : 위치상관없이 인덱스를 사용해서 필드와 값을 지정한다. (복합인덱스)

ex)

	1) 점
	
			{$geometry : {type:"Point", coordinates: [lon,lat]}}
	
	2) 직선
	
			{$geometry : {type: "LineString", coordinates: [lon,lat,lon,lat]}}			
	
	3) 다각형
	
			{$geometry : {type:"Polygon", coordinates: [lon,lat,lon,lat, ...]}}

GeoJSON의 집합 $ geometry : {type : "GeometryCollection"geometries:[...]}

좌표 점 [x1, y1]

좌표 사각 $ box : [x1,y1,x2,y2]

좌표 다각형 $polygon : [x1,y1,x1,y2,x2,y2,x2,y1]

좌표 원형 $center : [x1,y1,r]

좌표 지구 표면에 원형 $centerSphere : [x,y,radius]





> 1) 컬렉션에 데이터를 입력한 다음 연산자를 이용해서 $box 영역의 데이터를 출력해보자 

```javascript
//컬렉션에 원하는 좌표를 입력한다 
//원형(인덱스)를 지정한다
//조건에 맞는 데이터를 추출한다
db.createCollection("location")

db.location.save({_id:"A", position:[0.001,-0.002]})
db.location.save({_id:"B", position:[1.0,1.0]})
db.location.save({_id:"C", position:[0.5,0.5]})
db.location.save({_id:"D", position:[-0.5,-0.5]})

//원형을 색인으로 지정함 
db.location.ensureIndex({position:"2d"})
db.location.stats()

//조건 연산자로 지정함 
db.location.find({position:{$near:[0,0],$maxDistance:0.75}})
//공간 추출 연산자
db.location.find({position:{$within:{$box:[[0.25,0.25],[1.0,1.0]]}}})

```



> 1-2) x=0.2 y=0.25 에서 가장 근거리 0.1 근처에 있는 좌표를 출력해보자

```javascript
db.location.find({postion:{$near:[0.2, 0.25], $minDistance:0.1}})
db.location.find({postion:{$near:[0.2, 0.25], $maxDistance:1.2}})
```



> 플레이데이터 좌표: 37.486435,127.0184989
>
> 스타벅스 좌표:37.4860531,127.0199516
>
> 2) 플레이데이터 좌료를 구해서 근접 좌표를 랜덤으로 구한 다음 컬렉션에 저장하자 

```javascript
var baseLng = 127.0184989;
var baseLat = 37.486435;
var categories = ['커피','은행','편의점'];
for(var i = 1; i<=100; i++){
  var myLng = baseLng+(Math.random()*0.001);
  var myLat = baseLat+(Math.random()*0.001);
  var myCategory=categories[Math.floor(Math.random()*categories.length)];
  db.places.save({ location:[myLng, myLat], category:myCategory });
}
```



> 2-1) 2d 인덱스를 작성하자 

```javascript
db.places.ensureIndex({location:"2d", category:1})
```



> 2-2) 편의점을 찾아 출력하자

```javascript
db.places.find({category:"편의점"})
```



> 2-3) 은행을 찾아 출력하자 

```javascript
db.places.find({category:"은행"})
```



> 2-4) 커피의 개수를 출력하자

```javascript
db.places.find({category:"커피"}).count()
```



> 2-5) 플레이데이터 근처(가장 가까운)ㄴ에 있는 편의점과 은행 10개를 찾아서 출력하자 

```javascript
db.places.find({location:{$near:[127.0184989,37.486435]},category:{$in:["편의점","은행"]}}).limit(10);
```

![image-20200824110427112](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi1oaeop24j31si0egtvb.jpg)



> 2-6) 2-5번에서 찾은 제일 첫번째 은행의 좌표에서 가장 가까운 편의점, 은행, 커피 전문점을 추출해보자 

```javascript
db.places.find({location:{$near:[127.01873356555983, 37.48644179594182 ]}}).limit(5);
```



> 2-7) 플레이데이터 근방 5km 이내의 편의점을 $centerSphere를 통해서 찾아라 
>
> 특정한 위치를 기준으로 일정 거리를 찾을 때는 $centerSphere를 사용함 
>
> ```javascript
> {
>    <location field>: {
>       $geoWithin: { $centerSphere: [ [ <x>, <y> ], <radius> ] }
>    }
> }
> ```
>
> 

```javascript
db.places.find({
 location:{$geoWithin:{$centerSphere:[[127.0184989,37.486435],3.10686/3963.2]}},
 category:{$eq:"편의점"}
})
```



>  GeoJson 포맷을 이용할 때에는 $geometry 연산자를 사용, 2dsphere 인덱스 사용
>
> ```javascript
> {
>    <location field>: {
>      $near: {
>        $geometry: {
>           type: "Point" ,
>           coordinates: [ <longitude> , <latitude> ]
>        },
>        $maxDistance: <distance in meters>,
>        $minDistance: <distance in meters>
>      }
>    }
> }
> ```
>
> 

> $minDistance와 maxDistance를 미터 단위로 설정하여 검색 거리를 조절함 
>
> 레거시 좌표점을 이용하는 경우, radian 값으로 설정할 수 있으나, $near는 minDistance의 설정이 불가능 하다



> 2-8) 플레이 데이터의 근방 5km 이내의 편의점을 가까운 순서대로 찾아라 

```javascript
db.places.ensureIndex({location:"2dsphere",category:1});
db.places.find({
  location:{
    $nearSphere:{$geometry:{type:'Point', coordinates:[127.01873356555983, 37.48644179594182]},
                 $maxDistance:5000}},
  category:{$eq:"편의점"}
})
```



> {$geoNear:{<geoNear options>}}을 이용해서 aggregation을 사용해보자 
>
> ex) 가까운 거리도 구하고 장소도 찾을 때 사용함 
>
> 옵션: 
>
> - distanceField: 거리를 출력할 필드명
> - includeLocs: 거리를 계산할 좌표값
> - key: 인덱스가 설정한 필드
>
> 2-9) 플레이데이터의 5km 이내의 편의점을 가까운 순으로 검색하고 각각의 거리를 구하자

```javascript
db.places.aggregate([
   {
     $geoNear: {
       spherical:true,
       limit:10,
       maxDistance:5000,
        near: { type: "Point", coordinates: [127.01873356555983, 37.48644179594182] },
        distanceField: "distance",
        key: "location",
        query: { category: "편의점" },
     }
   }
]);
```

![image-20200824121424565](https://tva1.sinaimg.cn/large/007S8ZIlgy1gi1qb47xazj32960d4ayy.jpg)



> 2-10) 플레이데이터의 5km 이내의 편의점을 가까운 순으로 검색하고 각각의 거리를 구한 결과를 places_res라는 컬렉션으로 저장한 다음 places_res.json 이름으로 /Users/kimdabin/playdata/file-source 에 내보내자 

```javascript
var places_res = db.places.aggregate([
   {
     $geoNear: {
       spherical:true,
       limit:10,
       maxDistance:5000,
        near: { type: "Point", coordinates: [127.01873356555983, 37.48644179594182] },
        distanceField: "distance",
        key: "location",
        query: { category: "편의점" },
     }
   }
]);


```



## Replication



## Sharding이란

- [참고 문서](https://docs.mongodb.com/manual/replication/)
- 데이터를 여러 서버에 분할, 데이터의 분할은 MongoDB가 분할 된 데이터는 상황에 따라 각 Shard 이동 MongoDB 이용자(앱)은 여러 서버를 의식하지 않아도 됨
- 복제가 동일한 데이터의 사본을 여러 DB에 있는 반면, Sbarding은 다른 데이터를 여러 DB에 가짐 
- 따라서 보통의 운용은 Sharding + ReplicaSet(복제) 로 구성됨

1. 샤드
   - 실제로 데이터가 저장되어 있는 mongod 프로세스 
   - 하나의 문서는 하나의 샤드에 저장되어 샤드 간의 데이터 복제를 하지 않음 
   - 복제 구성하는 것을 권장
2. config 서버 
   - 샤드 메타 데이터를 관리하는 mongod 프로세스
   - 단일 장애 지점이 되므로, 여러 config 서버로 구성하는 것을 추천
3. mongos 서버
   - 샤드의 라우팅 프로세스
   - 샤드와 클라이언트를 연계시킴 필요하다면 여러 mongos 서버를 가짐 
   - Mongod 프로세스가 아니기 때문에 상태와 데이터를 가지고 있지 않음
4. 샤드 키 
   - 데이터를 분산하는 범위의 키로 복수 지정할 수 있음 
   - 키에 어떤 범위의 데이터가 어떤 샤드에 저장되는지는 MongoDB가 관리하고 데이터의 편차에 따라 자동으로 조정함
5. 청크(chunk) 
   - 샤드의 덩어리는 분산 데이터의 단위 
   - 구체적으로는 컬렉션의 연속된 범위의 데이터에서의 여러 문서
   - 청크의 최대 크기에 도달하면 분할된 샤드가 가지고 잇는 청크 수에 따라 필요한 경우 다른 샤드로 이동됨
   - 청크의 최대 크기는 변경 가능(수평 개념을 가짐)
   - 청크는 MB 단위
   - 최소는 1MB, 기본은 64MB
6. 데이터베이스에 부하가 생길 경우 Vertical Scaling과 Horizontal Scaling을 사용함 
   - 수직 스케일링은 하나의 머신에 더 많은 RAM과 더 많은 코어 등을 추가하는 방법
   - 수평 확장은 여러 대의 머신을 구성하는 방법
   - 데이터베이스를 구성하면서 수평 확장하는 방법은 복제본을 늘리는 것임
   - ex) mysql: 읽기 복제본을 여러 대 생성하여 쓰기는 마스터에서 실행하고 읽기 작업은 복제된 노드에서 실행하여 부하를 분산함 
   - 몽고는 샤딩을 사용하여 매우 큰 데이터 세트와 높은 처리량 작업으로 배포를 지원하고 시스템이 부하를 견디지 못할 때, 샤딩을 통해 가용성을 늘려주고 버틸 수 있는 처리량도 늘려줌 
7. mongodb의 shared cluster는 3가지 구성요소로 구성(샤드, 몽고, 설정 서버)
   - shard는 sharded cluster 안에서 sharded data의 서브셋을 사용
   - 클러스터의 조각들은 존재하는 데이터를 합하면 원본의 데이터가 됨
   - shard는 반드시 복제 세트를 구성해야 함 

> 클러스터 구성요소
>
> 1. Primary : 복제 마스터
> 2. Secondary : 복사본 유지
> 3. Arbiter: 2번의 서버가 자동으로 fail over 하게 되면 새 기본 노드를 선택
> 4. Config : 샤드 구성 정보와 분포를 유지 시킴
> 5. Mongos: 데이터 요청에 대한 라우팅을 실시함



### rs.status()

#### stateStr

- STARTUP: 노드간에 설정을 공유하고 조정
- PRIMARY: 기본
- SECONDARY: 보조. priority가 0보다 크다 
- RECOVERING: 장애 복구 및 새로운 노드의 추가 등으로 인해 일시적으로 읽고 쓸 수 없음을 나타냄 
- FATAL: 네트워크는 연결되어 있지만, ping 응답이 없는 것
- STARTUP2: 데이터 파일의 동기화가 진행 중
- UNKNOWN: 네트워크가 연결되어 있지 않음
- ARBITER: 아비타 
- DOWN: 액세스 가능하지만, ping 응답이 없는 것
- ROLLBACK: 롤백 처리 중



### Shard, Config, Router 서버 

- 중요한 3가지 요소
- 실행 순서 config -> router -> shard

#### Router 서버 

- 기본적으로 라우터라고 불리는 mongos 서버
- 사용자의 요청 쿼리를 어떤 샤드 서버에 전달해야할 지 결정 및 쿼리를 전송
- 샤드 서버로부터 받은 결과를 합쳐서 다시 사용자에게 반환하는 역할을 수행(Proxy 역할 수행)
- 샤딩이 활성화된 mongodb 에서는 하나의 컬렉션이 chunk단위로 데이터가 분리되어 저장이 됨
- 이 정보는 config 서버에서 기록
- mongos는 이 메타 정보를 캐싱된 데이터 또는 config서버를 호출하여 어떤 샤드로 정보를 요청해야할지 조회함 



#### Config서버(mongod)

- 이름과 같이 config 서버는 샤딩된 클러스터를 관리하는 데 있어 필요한 메타정보를 전부 관리
- databases: 샤드 클러스터의 데이터베이스 목록
- chuncks: 샤딩된 컬렉션의 청크 정보
- shards: 샤드 서버의 정보 저장
- settings: 청크의 밸런싱 작업 설정(샤드 서버에 최대한 균등하게 데이터를 저장하기 위해 밸런싱 작업을 진행함)
- mongos: mongos상태 정보 
- version: 샤드 클러스터의 메타 데이터 버전 정보(청크 또는 컬렉션 같은 데이터가 벼경 될 때 기록되는 버전정보)
- lockping: 각 샤드 클러스터의 멤버와 config 서버와 연결 상태 확인 정보 
- locks: 여러 mongos의 동시작업의 동기화를 위한 컬렉션 
- changelog: config 메타 정보 변경 이벤트에 대한 로그를 남기는 컬렉션 



#### Shard 서버(mongod)

- 3.6버전 이상부터 하나 이상의 replicaset(primary, secondary로 구성)가 필요함
- replicates: 동일한 데이터를 여러군데에 관리하여 손실을 막을 수 있고, 여러 node를 통해 조회를 제공함으로서 쿼리이 분산을 구현함 
  - Primary, Secondary, Arbiter서버로 구성 
  - Primary: replicaset에서 유일하게 데이터 변경을 처리할 수 있는 서버 
    - replicaset에서 primary는 장애상황(네트워크 오류, 서버다운)등에 따라 Secondary가 Primary로 지정됨 
  - Secondary: primary 에서 처리된 데이터를 가져와서 변경된 데이터를 동기화함 
    - Secondary가 직접 데이터 변경 요청을 처리할 순 없으며, Read Preference옵션을 이용해서 read query를 실행함 
  - Arbiter: primary에 이상이 생겼을 때, 어떤 Secondary가 Primary로 될지 사용되며 데이터는 저장하지 않음 (ver3.2이상)







> 1) mongos > 접속해서 샤드 상태로 청크를 체크함 2번째 청크가 시작되는 id를 삭제한 다음 샤드 상태와 청크 상태를 확인 후 개수 확인 + RS:PRIMARY > 개수 확인 



> 2) mongos > user에 데이터를 추가 마지막 청크에 끝나는 ID값을 이후의 ID로 추가함(2개 추가후)
>
>  샤드 상태와 청크 상태를 확인 후 개수 확인

```javascript
mongos> db.user.find().sort({"userId":-1})
```



```javascript
for(var i = 1; i<=2; i++){
  var user = {
    userId :999999+i,
    name:"userName"+(999999+i),
    createAt:new Date().getTime()
  }
  db.user.save(user);
}
```



> 3) RS:PRIMARY > user에 데이터를 1개 추가함 

```javascript
var user = {
    userId :222,
    name:"userName"+222,
    createAt:new Date().getTime()
  }
  db.user.save(user);
```



> 6) rs 삭제후에 기존 컬렉션은 user는 4개의 청크를 기억하고 있다. 새로운 컬렉션 추가후 샤드, 청크 확인

```javascript
for(var i = 0; i<10000; i++){
  db.logs.insert({ "uid" : i ,"value":Math.floor(Math.random()*10000+1)})}
```



> 7) 샤드를 분할하자 

```javascript
mongos> sh.splitAt("blog.user",{userId:20000})
mongos> sh.status(true)
mongos> db.user.getShardDistribution()
mongos> db.adminCommand({flushRouterConfig:"blog.user"})
```



> 8) use config

```javascript
mongos> db.settings.save({_id:"chunksize",value:3})
```



> 9) db mydb에 가서

```javascript
for(var i = 0; i<10000; i++){
  db.logs.insert({ "uid" : i ,"value":Math.floor(Math.random()*10000+1)})}

mongos> sh.enableSharding("mydb") //db 샤딩
mongos> sh.shardCollection("mydb.logs",{_id:1}) //컬렉션 샤딩
mongos> db.logs.getShardDistribution()
```



<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gi30ujvw86j312a0figrm.jpg" alt="image-20200825150430328" style="zoom:50%;" /> 

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gi30usmjj3j312a0i8n53.jpg" alt="image-20200825150452640" style="zoom:50%;" /> 

 

-- 로그 날려버리기



```javascript
var config = {_id:'RS', members: [{_id:0, host:'localhost:27030'},{_id:1, host:'localhost:27031'},{_id:2, host:'localhost:27032'}] };
```

```shell
 #!/bin/bash
mongod --shardsvr --dbpath /usr/local/var/mydb/mydata02 --logpath /usr/local/var/mydb/mydata02/log/monod1.log --port 27030 --nojournal --replSet RS |  
mongod --shardsvr --dbpath /usr/local/var/mydb/mydata03 --logpath /usr/local/var/mydb/mydata03/log/monod2.log --port 27031 --nojournal --replSet RS | 
mongod --shardsvr --dbpath /usr/local/var/mydb/mydata04 --logpath /usr/local/var/mydb/mydata04/log/monod3.log --port 27032 --nojournal --replSet RS
```



```shell
 #!/bin/bash
mongod --configsvr --dbpath /usr/local/var/mydb/mongoc1 --logpath /usr/local/var/mydb/mongoc1/log/mongoc.log --port 27011 --replSet CRS | 
mongod --configsvr --dbpath /usr/local/var/mydb/mongoc2 --logpath /usr/local/var/mydb/mongoc2/log/mongoc.log --port 27012 --replSet CRS | 
mongod --configsvr --dbpath /usr/local/var/mydb/mongoc3 --logpath /usr/local/var/mydb/mongoc3/log/mongoc.log --port 27013 --replSet CRS 
```



```javascript
var conf = {_id:'CRS',version: 1, members: [{_id:0, host:'localhost:27011'},{_id:1, host:'localhost:27012'},{_id:2, host:'localhost:27013'}] };
```



```javascript
sh.addShard('RS/localhost:27030,localhost:27031,localhost:27032');
```

```javascript
var conf = {_id:'TS',version: 1, members: [{_id:0, host:'localhost:27033'}] };
```







```javascript
for(var i = 1001; i<1000000; i++){
  var user = {
    userId :i,
    name:"userName"+i,
    createAt:new Date().getTime()
  }
  db.user.save(user);
}
```



```html
<div id="searchBox">
			<h2 class="screen_out">검색</h2>
			<form id="searchForm" action="/ajax/search" method="post">
				<fieldset class="fld_search">
					<legend class="screen_out">검색어 입력폼</legend>
					<div class="box_inp_search">
						<div class="select_title">제목+내용</div><!--//select_title -->
						
						<div class="select_option">
							<ul>
								<li>
								
								<label for="titleNcontent" class="opt_select">제목+내용</label>
									<input id="titleNcontent" 
						 class="hidden" checked type="radio" name="searchOpt" value="titleNcontent"/>
									
									
								</li>
								<li>
									<label for="searchTitle" class="opt_select">제목</label>
									<input id="searchTitle" class="hidden" type="radio" name="searchOpt" value="title"/>
									
								</li>
							
								<li>
								<label for="searchNickname" class="opt_select">닉네임</label>
									<input id="searchNickname" class="hidden" type="radio" name="searchOpt" value="nickname"/>
								
								</li>
							</ul>

						</div><!--//select_option -->
						
					</div><!--//box_inp_search -->

   					 
   					 
					<div class="box_inp_txt">
						<label class="screen_out">검색어 입력</label>
						<input name="text" class="inp_txt" type="text" placeholder="검색어를 입력해주세요" />
						 <button id="searchBtn" class="btn_search"> <span class="screen_out">검색하기</span> <i class="fa fa-search"></i> </button>
					</div><!--//box_inp_txt -->
					
				</fieldset>	

			</form><!--//searchForm -->
		</div><!--//searchBox -->
```



- 샤드는 





```javascript
for(var i=1002; i<=501002;i++){
  db.user.remove({ "userId" : i })
}
```

```shell
mongod --shardsvr --dbpath /usr/local/var/mydb/mydata05 --logpath /usr/local/var/mydb/mydata05/log/monod4.log --port 27033 --nojournal --replSet TS
```



```javascript
for(var i = 0; i<5000; i++){
  db.logs.insert({ "uid" : i ,"value":Math.floor(Math.random()*5000+1)})}
```





> 1) TS 샤드 추가 

```shell
$ mongod --shardsvr --dbpath /usr/local/var/mydb/mydata05 --logpath /usr/local/var/mydb/mydata05/log/monod4.log --port 27033 --nojournal --replSet TS
```

```shell
$ mongo localhost:27033
```

```javascript
MongoDB Enterprise > use admin
switched to db admin
MongoDB Enterprise > var conf = {_id:'TS',version: 1, members: [{_id:0, host:'localhost:27033'}] };
MongoDB Enterprise > rs.initiate(conf)
//나갔다가 다시 들어오기
MongoDB Enterprise TS:PRIMARY>
```

```javascript
mongos> sh.addShard("TS/localhost:27033");
```

```javascript
 mongos> sh.status()
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gi32kcdismj318e0i8jyp.jpg" alt="image-20200825160351285" style="zoom: 33%;" /> 



> 2) myTest db 추가 후 샤딩한 뒤 데이터 5000개 넣기 

```shell
mongos> use myTest
mongos> sh.enableSharding("myTest")
```

```javascript
mongos> for(var i = 100000; i<200000; i++){ db.logs.insert({ "uid" : i ,"value":Math.floor(Math.random()*200000+1)})}
```



> 3) 컬렉션 샤드 한 뒤 chunck 사이즈 1로 변경 후 샤드 확인

```shell
mongos> sh.shardCollection("myTest.logs",{_id:1})
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gi32ny7qt7j312y0i6dnx.jpg" alt="image-20200825160726947" style="zoom: 33%;" />  

```javascript
mongos> use config
mongos> db.settings.save({_id:"chunksize",value:1})
```

```javascript
mongos> use myTest
mongos> db.logs.getShardDistribution()
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gi32qap2ssj314i0ek7ax.jpg" alt="image-20200825160945202" style="zoom: 33%;" />  