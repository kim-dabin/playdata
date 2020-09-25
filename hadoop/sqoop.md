# Sqoop

## 개요

- sql to hadoop
- RDBMS로 부터 수집/저장
  - 별도의 로그 수집 시스템 및 데이터 저장소가 마련되지 않아 Oracle, MySQL등의 RDBMS에 로그를 저장하는 경우 
  - 로그 뿐 아니라, 메타성 데이터는 대부분 RDBMS에 저장되어 있는데, 이 **RDBMS의 메타데이터를 Hadoop, Hive 등으로 옮겨야 하는 경우**
    - 메타데이터의 유실을 방지 
  - 분산 환경의 Hadoop, Hive 등에서 분석된 결과를 api형태가 아닌 원격의 RDBMS로 전송할 경우 