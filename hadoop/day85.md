# DAY85



## Step01 : 도착 지연 프로그램

### java 프로그램

```tex
package   : com.airline
class   : AirlineDTO
Mapper   : DelayCountMapper
Reducer   : DelayCountReducer

class   : MyCount   : main()

클래스
Mapper
   (입력)   라인넘버, 항공운항 통계 데이터
   (출력)   운항년도 / 운항월, 출발 지연 건수
Reducer
   (입력)   운항년도   / 운항월, 출발 지연건수
   (출력)   운항년도   / 운항월, 출발 지연건수 합계
```



- AirlineDTO

  ```java
  package com.airline;
  
  import org.apache.hadoop.io.Text;
  
  public class AirlineDTO {
  	private int year;
  	private int month;
  	private int arriveDelayTime = 0;
  	private int departureDelayTime = 0;
  	private int distance = 0;
  	private boolean arriveDelayAvailable = true;
  	private boolean departurDelayAvailable = true;
  	private boolean distanceAvailable = true;
  	private String uniqueCarrier;
  	
  	public AirlineDTO(Text text) {
  		try {
  			String[] columns = text.toString().split(",");
  			year = Integer.parseInt(columns[0]);
  			month = Integer.parseInt(columns[1]);
  			uniqueCarrier = columns[8];
  			
  			//출발 지연 
  			if(!columns[15].equals("NA"))	departureDelayTime = Integer.parseInt(columns[15]);
  			else	departurDelayAvailable = false;
  			
  			//도착지연
  			if(!columns[14].equals("NA"))	arriveDelayTime = Integer.parseInt(columns[14]);
  			else arriveDelayAvailable = false;
  			
  			//거리 
  			if(!columns[18].equals("NA"))	distance = Integer.parseInt(columns[18]);
  			else distanceAvailable = false;
  
  			
  			
  			
  		} catch (Exception e) {
  			// TODO: handle exception
  			System.out.println("error parsing a record : "+e.getMessage());
  		}
  	}
  	
  	
  	public int getYear() {
  		return year;
  	}
  	public void setYear(int year) {
  		this.year = year;
  	}
  	public int getMonth() {
  		return month;
  	}
  	public void setMonth(int month) {
  		this.month = month;
  	}
  	public int getArriveDelayTime() {
  		return arriveDelayTime;
  	}
  	public void setArriveDelayTime(int arriveDelayTime) {
  		this.arriveDelayTime = arriveDelayTime;
  	}
  	public int getDepartureDelayTime() {
  		return departureDelayTime;
  	}
  	public void setDepartureDelayTime(int departureDelayTime) {
  		this.departureDelayTime = departureDelayTime;
  	}
  	public int getDistance() {
  		return distance;
  	}
  	public void setDistance(int distance) {
  		this.distance = distance;
  	}
  	public boolean isArriveDelayAvailable() {
  		return arriveDelayAvailable;
  	}
  	public void setArriveDelayAvailable(boolean arriveDelayAvailable) {
  		this.arriveDelayAvailable = arriveDelayAvailable;
  	}
  	public boolean isDeparturDelayAvailable() {
  		return departurDelayAvailable;
  	}
  	public void setDeparturDelayAvailable(boolean departurDelayAvailable) {
  		this.departurDelayAvailable = departurDelayAvailable;
  	}
  	public boolean isDistanceAvailable() {
  		return distanceAvailable;
  	}
  	public void setDistanceAvailable(boolean distanceAvailable) {
  		this.distanceAvailable = distanceAvailable;
  	}
  	public String getUniqueCarrier() {
  		return uniqueCarrier;
  	}
  	public void setUniqueCarrier(String uniqueCarrier) {
  		this.uniqueCarrier = uniqueCarrier;
  	}
  	
  	
  }
  ```

- DelayCountMapper

  ```java
  package com.airline;
  
  import java.io.IOException;
  
  import org.apache.hadoop.io.IntWritable;
  import org.apache.hadoop.io.LongWritable;
  import org.apache.hadoop.io.Text;
  import org.apache.hadoop.mapreduce.Mapper;
  //(입력) 라인넘버,항공운항 통계 데이터/ (출력) 운항년도/ 운항월, 출발 지연 건수
  public class DelayCountMapper extends Mapper<LongWritable, Text, Text, IntWritable> {
  	private final static IntWritable outputValue = new IntWritable(1);
  	private Text outputKey = new Text();
  	
  	@Override
  	protected void map(LongWritable key, Text value, Mapper<LongWritable, Text, Text, IntWritable>.Context context)
  			throws IOException, InterruptedException {
  		// TODO Auto-generated method stub
  		AirlineDTO parser = new AirlineDTO(value);
  		outputKey.set(parser.getYear()+","+parser.getMonth());
  		if(parser.getDepartureDelayTime()>0) {
  			context.write(outputKey, outputValue);// (출력) (운항년도/운항월), 출발 지연 건수
  		}//if end 
  		
  	}//map end 
  	
  	
  }
  ```

- DelayCountReducer

  ```java
  package com.airline;
  
  import java.io.IOException;
  
  import org.apache.hadoop.io.IntWritable;
  import org.apache.hadoop.io.Text;
  import org.apache.hadoop.mapreduce.Reducer;
  
  public class DelayCountReducer extends Reducer<Text, IntWritable, Text, IntWritable> {
      private IntWritable result = new IntWritable();
  
      public void reduce(Text key, Iterable<IntWritable> values, Context context)
            throws IOException, InterruptedException {
         int sum = 0;
         for (IntWritable val : values) {
            sum += val.get();
         }
         result.set(sum);
         context.write(key, result);
      }
   }
  ```

  

### hadoop

- hadoop02 서버에서 실행

  ```bash
  $ bunzip2 1987.csv.bz2 
  $ ls
  $ cat 1987.csv 
  $ ls -al
  $ pwd
  $ sed -e '1d' 1987.csv > 1987_new.csv 
  $ hadoop fs -mkdir /myinput
  $ hadoop fs -ls /
  $ hadoop fs -put *_new.csv /myinput
  $ hadoop fs -ls /myinput
  $ ls
  $ cd mywork/
  $ head 1987_new.csv 
  $ ls
  $ cd mywork/
  $ ls
  $ hadoop jar MWordCount.jar com.airline.MyCount /myinput /myresult
  $ hadoop fs -cat /myresult/part-r-00000
  ```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1giukc0y996j30w0058di2.jpg" alt="image-20200918104857597" style="zoom:50%;" /> 



## Step02 : 출발 지연 프로그램

```bash
$ hadoop jar MWordCount2.jar com.airline.MyCount02 /myinput /myresult02
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1giulqqhol4j317g0amwl1.jpg" alt="image-20200918113749029" style="zoom: 33%;" /> 





## Step03 : 타입 지정

[getRemainingArgs](https://hadoop.apache.org/docs/r1.0.4/api/org/apache/hadoop/util/GenericOptionsParser.html)

```bash
$ hadoop jar MWordCount3.jar com.airline.MyCount03 -D workType=arrival /myinput /myresult04
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1giumq35ijdj317g096wkl.jpg" alt="image-20200918121147601" style="zoom:33%;" /> 

```bash
$ hadoop jar MWordCount3.jar com.airline.MyCount03 -D workType=departure /myinput /myresult05
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1giun138gjyj317g096dls.jpg" alt="image-20200918122221772" style="zoom:33%;" /> 



```tex
1,100
2,30
3,300
4,100
5,15
6,25
7,55
8,77
9,90
```



```bash
wget https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-amd64.zip
```



```xml
<property> 
	<name>hbase.rootdir</name>
  <value>hdfs://hadoop01:8020/hbase</value>
</property>
<property>
	<name>hbase.zookeeper.quorum</name>
  <value>hadoop01,hadoop02,hadoop03</value>
</property>
<property>
	<name>hbase.zookeeper.property.dataDir</name>
  <value>/home/hadoop/var/zookeeper</value>
</property>
<property> 
  <name>hbase.cluster.distributed</name> 
  <value>true</value>
 </property>
<property>
	<name>dfs.replication</name> 
  <value>1</value> 
</property>
<property> 
  <name>hbase.zookeeper.property.clientPort</name> 
  <value>2181</value>
</property>
```



```bash
scp hbase.tar.gz hadoop@hadoop02:/home/hadoop 
scp hbase.tar.gz hadoop@hadoop03:/home/hadoop 
scp hbase.tar.gz hadoop@hadoop04:/home/hadoop

ssh hadoop@hadoop02 "cd /home/hadoop; tar xvfz hbase.tar.gz" 
ssh hadoop@hadoop03 "cd /home/hadoop; tar xvfz hbase.tar.gz" 
ssh hadoop@hadoop04 "cd /home/hadoop; tar xvfz hbase.tar.gz"

ssh hadoop@hadoop02 "cd /home/hadoop; ln -s hbase-1.4.13/ hbase" 
ssh hadoop@hadoop03 "cd /home/hadoop; ln -s hbase-1.4.13/ hbase" 
ssh hadoop@hadoop04 "cd /home/hadoop; ln -s hbase-1.4.13/ hbase"

ssh hadoop@hadoop02 "mkdir /home/hadoop/var/hbase"
ssh hadoop@hadoop03 "mkdir /home/hadoop/var/hbase"
ssh hadoop@hadoop04 "mkdir -p /home/hadoop/var/hbase"
```



```shell
Test_Agent.sources  = TestSource_SpoolSource
Test_Agent.channels = TestChannel_Channel
Test_Agent.sinks    = TestSink_LoggerSink

Test_Agent.sources.TestSource_SpoolSource.type = spooldir
Test_Agent.sources.TestSource_SpoolSource.spoolDir = /home/hadoop/working/batch-log
Test_Agent.sources.TestSource_SpoolSource.deletePolicy = immediate
Test_Agent.sources.TestSource_SpoolSource.batchSize = 1000

Test_Agent.channels.TestChannel_Channel.type = memory
Test_Agent.channels.TestChannel_Channel.capacity = 100000
Test_Agent.channels.TestChannel_Channel.transactionCapacity  = 10000

Test_Agent.sinks.TestSink_LoggerSink.type = logger
Test_Agent.sources.TestSource_SpoolSource.channels = TestChannel_Channel
Test_Agent.sinks.TestSink_LoggerSink.channel = TestChannel_Channel
```



```shell
--username
big5
-P
admin1234
--connect
jdbc:oracle:thin:@192.168.0.15:1521:xe
--query
"SELECT no, title, author, publish_date FROM books WHERE $CONDITIONS"
--target-dir
books
--split-by
NO
--m
1
```

