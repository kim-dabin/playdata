# DAY86



## Hadoop 운영

1. hadoop01: 주키퍼 서버, 액티브 네임노드, 저널노드, 리소스 매니저
2. hadoop02: 주키퍼 서버, 스탠바이 네임노드, 저널노드, 데이터노드, 노드 매니저
3. hadoop03: 주키퍼 서버, 데이터노드, 노드 매니저
4. hadoop04: 데이터노드, 노드 매니저



```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
   <property>
     <name>hive.metastore.warehouse.dir</name>
     <value>/user/hive/warehouse</value>
   </property>
   <property>
     <name>hive.cli.print.header</name>
     <value>true</value>
   </property>
   <property>
     <name>javax.jdo.option.ConnectionURL</name>
     <value>jdbc:oracle:thin:@192.168.0.15:1521:xe</value>
   </property>
   <property>
     <name>javax.jdo.option.ConnectionDriverName</name>
     <value>oracle.jdbc.OracleDriver</value>
   </property>
   <property>
     <name>hive.metastore.local</name>
     <value>false</value>
   </property>
   <property>
     <name>javax.jdo.option.ConnectionUserName</name>
     <value>hive</value>
   </property>
   <property>
     <name>javax.jdo.option.ConnectionPassword</name>
     <value>hive</value>
   </property>
</configuration>
```

