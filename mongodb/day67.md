



# DAY67



> mongodb 버전별 
>
> - ver 2.0 + hadoop 1.x + spring 2.5x
> - ver 3.0 + hadoop 2.x + spring 4.0x
> - ver 4.0 + haoop 3.x + spring 4.0x & 5.0



## 몽고 DB 쿼리 

1. 질의 : 하나의 쿼리를 명시하는 키워드 mongoDB는 6개정도의 질의를 구현함 
   1. 키 - 값 질의 : 특정 필드와 맵핑되는 값을 포함하는 문서를 말함
      1. 주 key에 대한 값을 리턴하는 경우를 말함
   2. 범위 : 특정 범위에 포함되는 값을 말함(비교 연산자)
   3. 공간 질의 : 선, 원, 다각형 등에 대한 공간 근사값 [선박, 항공, 제조업, 구글, 포털 사이트]
   4. 문자열 탐색 질의 : 논리 연산자를 통해서 특정 문자열에 결과값
   5. 집합 질의: 그룹 함수를 지칭하며 count, min, max, average 등을 이용한 결과값
   6. mapreduce query: java script로 표현 되는 복잡한 데이터를 데이터 베이스에 시행해 반환하는 질의를 말함 
2. 파일 입출력 및 변환 작업
   1. csv [tsv] <-> json <-> sql
3. 복제 및 샤딩 작업 [index]



### 몽고 DB document

#### 외부적인 상태

1. mongod.lock : 서버의 프로세스 ID를 저장함 
2. .0 파일(.ns) : 메타데이터를 네임스페이스 단위로 저장
3. 2번의 크기는 ns 16M를 넘을 수 없음 - 28000개의 네임스페이스 
   1. 하나의 데이터베이스는 컬렉션과 색인의 개수를 최대 28000개를 생성할 수 있음
   2. --nssize arg(=16) 사이즈는 늘릴 수 잇으나 16M 단위로 처리함 
4. test.0(64M), test.1(128M) 등의 파일은 순수 데이터 파일을 저장함 
   1. 파일의 용량은 2GB까지가 최대 [collection-0--7213171893261642407.wt]
5. 몽고는 데이터저장소의 크기를 정적으로 관리함 
6. wt[WiredTiger]: 스토리지 엔진 + DB 엔진(mysql)로 구현될 수 있게 분리되는 프레임워크로 몽고의 DB엔진을 말함 
7. 스파크에 연동되어 집계 또는 스트리밍 작업을 원할하게 해줌



#### 내부적인 상태를 확인 -- db.stats(1024);

- 자료를 입력했을 경우 몽고 드라이버의 동작 
  1. mongodb에 삽입되는 문서의 고유번호 ID인 _id로 필드와 값을 생성함
  2. 문서를 mongodb의 bson으로 변환함
  3. 네트워크 소켓을 통해서 데이터베이스로 데이터를 전달함 

- ObjectId("5f3a12c6debfda47423a3e23")
  - 5f3a12c6 : 타임스탬프<4byte> 1970.1.1 표준타임
  - debfda : 서버 ID <3byte> 서버측 식별자 
  - 4742 : 프로세스 ID <2byte> 
  - 3a3e23 : 로컬 카운터 <3byte> 

- 문서에 컬렉션이 추가되거나 문서가 추가될 때마다 ID가 생성되며 프로세스가 생성된 ID를 카운팅해서 관리함



### 컬렉션을 생성한 다음 키에 대한 필드 식별자

1. $로 시작할 수 없음
2. 255크기내에 작성함 
3. 연산자를 포할 수 없음
4. null(공백)이 중간에 들어 갈 수 없음
5. 필드 이름은 하나의 컬렉션 내에서 유일한 값으로 존재함
6. 전체 문서의 크기가 16M 제한적임(네트워크 대역폭)
7. 만일 문서가 대용량(16M 이상)이면 GridFS api를 사용해서 구현



```sql
db.Score.insert({name:"aaa",kor:90,eng:80,mat:98,test:"midterm"})
db.Score.insert({name:"bbb",kor:100,eng:100,mat:76,test:"final"})
db.Score.insert({name:"ccc",kor:90,eng:70,mat:58,test:"midterm"})
db.Score.insert({name:"ddd",kor:68,eng:77,mat:100,test:"final"})
db.Score.insert({name:"eee",kor:77,eng:80,mat:38,test:"final"})
db.Score.insert({name:"fff",kor:87,eng:100,mat:76,test:"midterm"})
db.Score.insert({name:"ggg",kor:89,eng:70,mat:58,test:"midterm"})
db.Score.insert({name:"hhh",kor:65,eng:77,mat:100,test:"final"})
```



> 1. Score의 전체 내용을 출력함 

```sql
db.Score.find().pretty()
```

> 2. Score의 이름만 출력하되, _id는 출력하지 않음 

```sql
 db.Score.find({},{_id:0, name:1})
```

> 3. 수학 점수 중 70점 이상만 출력하자

```sql
db.Score.find({mat:{$gte:70}})
```

![image-20200817151521987](https://tva1.sinaimg.cn/large/007S8ZIlgy1ghts7vyexhj31pg086ajn.jpg)

> 4. 이름과 국어를 출력하되 국어가 80점 이상만 출력하자 

```sql
db.Score.find({}{kor:{$gte:80},name:1,kor:1})
db.Score.find({ $and: [ { kor: { $gte: 80 } }, { kor: { $exists: true } } ] })



var sr = db.Score.find({kor:{$gte:80}}, {name:1,kor:1, _id:0});
var tot = 0;
while(sr.hasNext()){
	res = sr.next();
	print(res.name + " : "+res.kor);
	tot +=res.kor;
	print("tot = "+tot);
}
print("tot = "+tot);
```



> while

```javascript
var sr = db.Score.find({kor:{$gte:80}}, {name:1,kor:1, _id:0});
sr.forEach(function(x){print(x.name+ " : "+ x.kor);})
while(sr.hasNext()){
  print(x.name+ " : "+ x.kor);
}
```

> printjson을 이용해서 내용을 출력해보자 

```javascript
var sr = db.Score.find({kor:{$gte:80}}, {name:1,kor:1, _id:0});
sr.forEach(printjson);
```

![image-20200817153353845](../../../Library/Application Support/typora-user-images/image-20200817153353845.png)

> print를 사용해서 출력해보자[객체 주소 리턴]

```javascript
var sr = db.Score.find({kor:{$gte:80}}, {name:1,kor:1, _id:0});
sr.forEach(print);
```

![image-20200817153329873](../../../Library/Application Support/typora-user-images/image-20200817153329873.png) 



> Score의 내용을 전체 출력하자. 단 이름과 test가 final인 것만 출력해보자 

```javascript
var sm = db.Score.find();
sm.forEach(function(x){
  if(x.test=="final")	print(x.name + " : " + x.test);
  if(x.kor>=80)	print(x.name+" : "+x.kor);
});

db.Score.find({test:{$eq:"final"}},{name:1,test:1,_id:0});

```



> Score의 내용을 전체 출력하자 
>
> 단, test가 midterm 인 것만 출력하고 midterm의 수학만 합을 구하자

```javascript
var res = db.Score.find()
var tot = 0;
res.forEach(function(x){
  if(x.test=='midterm')	tot+=x.mat;
  
});
print("tot = "+tot);
```



> 이름이 a로 시작되는 사람의 전체 내용을 출력하자 

```sql
db.Score.find({name:/^a/})
```

```javascript
var res = db.Score.find();
res.forEach(function(x){
  if(x.name.startsWith('a')) print(x.name)
});
```



> 이름이 a로 시작하거나 e로 시작되는 전체 내용을 출력하자

```sql
db.Score.find( { $or: [ {name:/^a/} , {name:/^e/} ] } )
```



> 이름이 a로 시작하거나 e로 시작되는 사람의 명수를 출력

```sql
db.Score.find( { $or: [ {name:/^a/} , {name:/^e/} ] } ).count();
```



> 중복 데이터 제거

```sql
db.Score.distinct(key, query, <optional params>)
db.Score.distinct("name");
```



> test의 중복데이터 제거후 출력하되 (단일 데이터 출력) 영어 점수가 80점 이상인 학생들을 출력해보자 

```sql
db.Score.distinct('test',{eng:{$gte:80}});
```

 

> 이름 중복데이터를 제거 후 출력하되 (단일 데이터 출력) 영어 점수가 80점 이상인 학생들을 출력해보자 

```sql
db.Score.distinct('name',{eng:{$gte:80}});
```



> 이름과 test를 출력하되 이름을 내림차순으로 출력하자

```sql
db.Score.find({},{name:1,test:1}).sort({name:-1})
```



> 최대값 최소값에 대한 것을 sort(), limit() 메소드를 이용해서 출력해보자 
>
> 영어점수가 가장 높은 레코드 1만 출력해보자

```javascript
var res = db.Score.find();

res.forEach(function(x){
  x.
});
```



```sql
db.Score.find({}).sort({eng:-1}).limit(1);
```



> 국어 점수가 가장 낮은 이름과 점수를 출력해보자

```sql
db.Score.find({},{name:1,kor:1,_id:0}).sort({kor:1}).limit(1);
```



> 두 개 건너 뛰고 3개를 출력해보자

```sql
db.Score.find({}).limit(3).skip(2);
```

![image-20200817161323691](https://tva1.sinaimg.cn/large/007S8ZIlgy1ghttvnrad4j31ow0aigxs.jpg)



> - db : exam
>
> - collection : exam01
>   - no, value
>   - 1, 100
>   - 2, [10,20]
>   - 3, [[10,20],[30,40]]



```sql
db.exam01.insert({no:1,value:100})
db.exam01.insert({no:2,value:[10,20]})
db.exam01.insert({no:3,value:[[10,20],[30,40]]})
db.exam01.insert({no:4,value:30})
db.exam01.insert({no:5,value:[[40,20],[40,40]]})

-----


re = new Array([[10,20],[30,40]])
re[0]
re[0][0]
re[0][1]
```

```javascript
var res = db.exam01.find();

res.forEach(function(x){
  var re = x.value;
  var arr = new Array();
  //if(re.includes(30)||re.includes(40))	print(x.no);
  if(re.length>1){
	var find = re.reduce((acc, value, index, array) => {
  // print(acc+" "+value);
    arr = acc+","+value;
    return arr;
  });
  	//print(find);
    if(find.includes(30)||find.includes(40))	print(x.no);
 }
 
});

var res = db.exam01.find();
res.forEach(function(x){
	var re = x.value;
  var arr = new Array();
 	arr += re;
  if(arr.includes(30)||arr.includes(40)){
    var len = "";
    len+=("no : "+x.no+" , value : ");
    var words = arr.split(',');
  	words.forEach(function(y){
      if(y==30||y==40){
        len+=(y+" ");
      }//if end     
    });//foreach end 
    print(len);
  }//if end 
});//foreach end 

```

```sql
db.exam01.find({ value: { $all: [30, 40] } } )
db.exam01.find( { value: [30, 40] } )
db.exam01.find({ value: { $in: [[10, 20]] } } )
```

