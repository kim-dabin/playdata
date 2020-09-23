# Hive#1



```cmd
sqoop import --options-file books_import.sh --target-dir /user/hive
```





## hive query exam

```cmd
hive> create table books(isnb int, title String);
hive> DESCRIBE books;
hive> create table users(id int, name string) partitioned by (vcol string);
hive> desc users;
hive> show tables;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gj0lbbl2hpj30s80fe0x3.jpg" alt="image-20200923155610886" style="zoom:33%;" /> 

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gj0lbi1bppj30ii06idgy.jpg" alt="image-20200923155634731" style="zoom:33%;" /> 







### 수정

```cmd
hive> ALTER TABLE books add columns (author string, category string);
hive> desc books;
hive> alter table books rename to mybooks;
hive> drop table users;
```

<img src="../../../Library/Application Support/typora-user-images/image-20200923155702139.png" alt="image-20200923155702139" style="zoom:33%;" /> 

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gj0ldg5ddzj30m207mtao.jpg" alt="image-20200923155818623" style="zoom:33%;" /> 

![image-20200923155856831](../../../Library/Application Support/typora-user-images/image-20200923155856831.png)



```cmd
sqoop import --connect jdbc:oracle:thin:@192.168.0.219:1521/XE --username big5 --password admin1234 -P --table EMP  --colns "ENAME, EMPNO,SAL" --target-dir myemp  -m 1
```



```shell
--username
test
-P
--connect
jdbc:oracle:thin:@192.168.0.103:1521:xe
--table
"BOOKS"
--export-dir
books
-m
1
--direct
```



```cmd
create table my_books(no int, title string, author string, publish_date date)
```



```shell
--username
big5
-P
--connect
jdbc:oracle:thin:@192.168.0.15:1521:xe
--query
"SELECT no, title, author, publish_date FROM books WHERE $CONDITIONS"
--hive-import 
--create-hive-table
--hive-table
yourbooks
--split-by
NO
--m
1
```



```shell
--username
big5
-P
--connect
jdbc:oracle:thin:@192.168.0.15:1521:xe
--query
"SELECT no, title FROM books WHERE $CONDITIONS"
--hive-import 
--hive-table
books
--hive-home
/user/hive/warehouse
--target-dir
/user/hive/warehouse/yourbooks02
--hive-overwrite
--m
1
```

