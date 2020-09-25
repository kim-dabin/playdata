# Flume

## 아키텍처

![img](https://tva1.sinaimg.cn/large/007S8ZIlgy1gj1o6wjtloj314q07yq3x.jpg) 



> 톰캣 logs

- ![image-20200924142918519](https://tva1.sinaimg.cn/large/007S8ZIlgy1gj1of0rodoj32ck09gk11.jpg)

- ![image-20200924143155343](https://tva1.sinaimg.cn/large/007S8ZIlgy1gj1ohr055bj32ck0mi1kx.jpg)

### Agent

#### Source

- 이벤트를 수집하여 채널로 전달
- 종류
  1. avro 
     - Avro 클라이언트에서 전송하는 이벤트를 입력으로 사용, Agent와 Agent를 연결해줄 때 유용
  2. netcat 
     - TCP로 라인 단위 수집
  3. seq 
     - 0부터 1씩 증가하는 EVENT 생성
  4. exec 
     - 명령행을 실행하고 콘솔 출력 내용을 데이터 입력으로 사용
  5. syslogtcp 
     - System 로그를 입력으로 사용
  6. spooldir 
     - 디렉토리에 새롭게 추가되는 파일을 데이터로 사용
  7. thirft 
     - Thrift 클라이언트에서 전송하는 이벤트를 입력으로 사용
  8. jms 
     - JMS 메시지 수집
  9. Customize

#### Channel

- 이벤트를 Source와 Sink로 전달하는 통로
- 종류
  1. memory
     - Source에서 받은 이벤트를 Memory에 잠시 저장 
     - 간편하고 빠른 고성능(High Throughput)을 제공하지만 프로세스가 비정상적으로 종료시 이벤트 유실 가능성 있음
  2. jdbc
     - JDBC로 저장
  3. file
     - 속도는 메모리 채널에 비해 느리지만, 프로세스가 비정상적으로 죽더라도 프로세스를 재처리하여 이벤트 유실이 없음
  4. Customize

#### Sink

- Channel로 부터 받은 이벤트를 저장 또는 전달
- Sink Processor: 한 채널에 여러 Sink를 그룹으로 묶을 때 사용 
- Sink Processor를 통해 Sink할 대상을 다중 선택하거나 여러개의 Sink를 하나의 그룹으로 관리하여 Failover(장애 극복)에 대응
- 종류
  1. null
     - 이벤트를 버림
  2. logger
     - 테스트 또는 디버깅을 위한 로깅
  3. avro
     - 다른 Avro 서버(Avro Source)로 이벤트 전달
  4. hdfs
     - HDFS에 저장
  5. hbase
     - HBase에 저장
  6. elasticsearch
     - 이벤트를 변환해서 ElasticSearch에 저장
  7. file_roll
     - 로컬 파일에 저장
  8. thrift
     - 다른 Thrift 서버(Thrift Source)로 이벤트 전달
  9. Customize



### 활용

- 하둡 클러스터 내 노드들의 로그 수집
- 웹서버, 메일 서버 같은 기존 시스템들의 로그 수집
- 광고 네트워크 애플리케이션의 노출 정보 수집
- 시스템들의 성능 정보 수집
- 기본적인 온라인 스트리밍 분석 