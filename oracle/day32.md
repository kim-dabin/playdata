# Day32

## 학습목표

1. 계층적 질의를 살펴 보고 사용할 수 잇다. 
2. PL/SQL을 사용할 수 있다. 
3. PL/SQL의 구문을 이용해서 기본 문법과 SQL 구문을 접목할 수 있다. 
4. 함수와 프로시저를 사용할 수 있다. 



## 계층적 질의

- 정의 : 테이블 행 사이의 계층적 관련성을 바탕으로 데이터를 검색하는 질의 

- 형식 

  ```mariadb
  SELECT [LEVEL], COLUMN1, COLUMN2,,,
  FROM TABLE -- 조인 사용 불가 
  WHERE conditions
  [START WITH condition] -- LEVEL의 의사열을 가진다. ROOT의 계층 시작점을 지정(서브쿼리 사용)
  [CONNECT BY PRIOR condition] -- 계층의 상 하위에 대한 LEVEL을 관리한다. PRIOR 바로 다음에 오는 행부터 검색되며 서브쿼리를 사용할 수 없다. 
  ```

- 방법 

  1. START WITH 절을 이용한 시작점 지정

     ex) START WITH COLUMN1=VALUE : 시작 행의 위치를 지정할 수 있다. 

     만일 명시하지 않으면 테이블의 모든 ROOT가 NODE1(LEVEL=1)이 된다. 

  2. CONNECT BY 절을 이용한 트리 진행 방향 설정

  3. LEVEL 의사열의 활용

  4. PRIOR 키워드의 활용

  5. WHERE 절 조건을 활용한 데이터 제거 방법

  6. CONNECT BY 절 조건을 이용한 데이터 제거

- 계층적 질의는 START WITH와 CONNECT BY의 존재 여부에 따라서 식별됨 

- 사용 시점 
  - 관계형 데이터베이스는 레코드를 계층적인 방법으로 저장하지 않음
  - but, 계층적 관련성이 단일 테이블의 행사이에 존재하며 계층적 질의는 하나의 테이블에 잇는 행들 사이에 어떤 관련성이 존재할 때 가능함 
    - ex) 매니저 직책에 있는 사원이 킹에게 보고할 때 사용
  - ANSI-SQL에서는 계층관계를 표현하는 Hierarchical Query를 사용하는 것은 불가능함
  - 확장된 형태의 recursive PL/SQL이나 CONNECT BY 를 이용하여 Hierarchical Query(계층쿼리) 표현이 가능함
  - 오라클에서는 CONNECT BY라는 확장된 구문을 통해 계층쿼리를 지원함 
  - 조직구조,(답글게시판),(디렉토리구조)등에 주로 이용됨
    - ex) CONNECT BY PRIOR 탑키 = 하위키 : TOP DOWN 방향 
    - ex) CONNECT BY PRIOR 하위키 = 탑키 : BOTTOM UP 방향 
    - 상위를 부모, 하위를 자식으로 연결 시켜서 열 위치에 의해 의사 결정을 사용함 
    - 항상 현재 부모 행에 관련된 CONNECT BY 조건에 의해 자식을 선택함 



> 1. 계층적 쿼리를 사용하여 위에서 아래로 사원의 이름과 관리자 이름을 조회하자 

```mariadb
select ename || ' 의 상사'|| prior ename "walk"
from emp 
start with ename = 'KING'
connect by prior empno=mgr;
```



> 2. 'SMITH' 부터 시작하여 아래서 위로 해당 사원의 매니저가 정보가 나오게 출력 

```mariadb
select ename, job, mgr
from emp 
start with ename = 'SMITH'
connect by prior mgr = empno;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf92ipga5dj30g40860uq.jpg" alt="image-20200529103451859" style="zoom:50%;" /> 

> 3. 가장 높은 레벨을 시작으로 가장 낮은 레벨의 각각의 다음 레벨을 들여 쓰기로 출력하자 
>
>    ex) KING 
>
>    ​		CLARK
>
>    ​			
>
>    ​			MILLER

```mariadb
SELECT LPAD(' ', 4*LEVEL-4) || ENAME RES,
LEVEL , EMPNO, MGR, DEPTNO
FROM EMP 
START WITH MGR IS NULL
CONNECT BY PRIOR EMPNO = MGR;
```



> 4. 전체 노드 중에서 'CLARK'의 노드를 제거한 후 출력 

```mariadb
select ename || ' 의 상사'|| prior ename "walk"
from emp 
where ename != 'CLARK'
start with ename = 'KING'
connect by prior empno=mgr;

select deptno, empno, ename, job, sal 
from emp 
where ename != 'CLARK'
start with mgr is null
connect by prior empno=mgr;
```



> 5. BLAKE와 부하 직원들을 모두 제거 
>
>    GIT의 브런치 삭제와 같은 원리 

````mariadb
select deptno, empno, ename, job, sal
from emp 
start with mgr is null
connect by prior empno = mgr and ename != 'BLAKE';

SELECT LPAD(' ', 4*LEVEL-4) || ENAME RES,
LEVEL , EMPNO, MGR, DEPTNO
FROM EMP 
START WITH MGR IS NULL
CONNECT BY prior empno = mgr and ename != 'BLAKE';
````

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf91r8mjy2j30rm0jy7ar.jpg" alt="image-20200529100827787" style="zoom:50%;" /> 

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf91r8mjy2j30rm0jy7ar.jpg" alt="image-20200529100827787" style="zoom:50%;" /> 



> 6. LEVEL을 정렬해보자 

```mariadb
SELECT LPAD(' ', 4*LEVEL-4) || ENAME RES,
LEVEL , EMPNO, MGR, DEPTNO
FROM EMP 
START WITH MGR IS NULL
CONNECT BY prior empno = mgr
order by ename;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf91tswbusj30vg0u0dpq.jpg" alt="image-20200529101054774" style="zoom:50%;" />  



> 7. Q6의 쿼리를 실행하게 되면 TREE구조로 만들어 정렬하기 때문에 계층이 틀어진다고 봄
>
>    TREE구조를 만들어서 계층을 두고 대상을 정렬하려면 ORDER SIBILINGS BY 키워드를 사용함
>
>    -> ROOT외의 노드들을 단계별로 정렬 

```mariadb
SELECT LPAD(' ', 4*LEVEL-4) || ENAME RES,
LEVEL , EMPNO, MGR, DEPTNO
FROM EMP 
START WITH MGR IS NULL -- KING
CONNECT BY prior empno = mgr
order SIBLINGS by ename;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf91wn2hcnj30vg0u0dpq.jpg" alt="image-20200529101338755" style="zoom:50%;" /> 



> 8. SYS_CONNECT_BY_PATH(대상, '구분자')를 이용해서 EMP의 TREE를 출력해보자 

```mariadb
SELECT LPAD(' ', 4*LEVEL-4) || ENAME RES, JOB,
	SYS_CONNECT_BY_PATH(ENAME,'/') RES02
FROM EMP 
START WITH MGR IS NULL
CONNECT BY prior empno = mgr;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf923tm2gsj30ry0vaalg.jpg" alt="image-20200529102032725" style="zoom:50%;" /> 



> 9. CONNECT_BY_ISLEAF: 마지막 노드의 유무를 찾을 수잇다
>
>    CONNECT_BY_ISLEAF = 0 : 현재 ROW가 자식노드를 가지고 있지 않을 때 LEAF NODE가 아니다
>
>    CONNECT_BY_ISLEAF = 1: 현재 ROW가 자식노드를 가지고 있지 않을 때 LEAF NODE 이다. 

```mariadb
SELECT LPAD(' ', 4*LEVEL-4) || ENAME RES, JOB,
	SYS_CONNECT_BY_PATH(ENAME,'/') RES02, CONNECT_BY_ISLEAF
FROM EMP 
START WITH MGR IS NULL
CONNECT BY prior empno = mgr;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf927q6flhj312w0u0163.jpg" alt="image-20200529102416566" style="zoom:50%;" /> 



> 10. 사원 테이블의 계층 구조에서 LEAF NODE만 보자 

````mariadb
SELECT LPAD(' ', 4*LEVEL-4) || ENAME RES, JOB
FROM EMP 
where CONNECT_BY_ISLEAF = 1
START WITH MGR IS NULL
CONNECT BY prior empno = mgr;
````

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf92arcd74j30fo0k0gpg.jpg" alt="image-20200529102713171" style="zoom:50%;" /> 



> 11. 사원 테이블의 계층 구조에서 LEAF NODE가 아닌 것들 

```mariadb
SELECT LPAD(' ', 4*LEVEL-4) || ENAME RES, JOB
FROM EMP 
where CONNECT_BY_ISLEAF = 0
START WITH MGR IS NULL
CONNECT BY prior empno = mgr;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf92bf92aoj30fo0demzz.jpg" alt="image-20200529102751260" style="zoom:50%;" /> 



## MERGE 구문

- 형식

  ````mariadb
  MERGE INTO TABLE_NAME -- 변경 또는 추가할 대상 테이블
  USING(쿼리) -- 변경 추가할 데이터의 원본을 지정, 테이블, 뷰, 서브쿼리를 사용
  ON(조인조건) -- MERGE 연산이 변경, 추가 되는 조건
  WHEN MATCHED THEN -- 해당 행이 존재하면 UPDATE
  UPDATE SET
  WHEN NOT MATCHED THEN -- 존재하지 않으면 INSERT
  INSERT ... VALUES...
  ````

  ```mariadb
  CREATE TABLE EMP_MAIN
  AS
  SELECT * FROM EMP;
  
  CREATE TABLE EMP_MASTER 
  AS 
  SELECT * FROM EMP WHERE DEPTNO = 30;
  
  INSERT INTO EMP_MASTER(EMPNO) VALUES(111);
  INSERT INTO EMP_MASTER(EMPNO) VALUES(222);
  INSERT INTO EMP_MASTER(EMPNO) VALUES(333);
  ```

  

> 12. 사원 EMP_MASTER 테이블을 EMP_MASTER 테이블과 비교해서 동일한 사원번호의 데이터가 있으면 EMP_MASTER 테이블의 급여, COMM를 EMP테이블의 값으로 수정하고 해당 사원번호를 가진 사원이 없으면 EMP_MASTER에 EMP테이블의 데이터를 이용해서 입력하는 작업을 수행하자 

```mariadb
MERGE INTO EMP_MASTER T
USING EMP_MAIN E
ON (T.EMPNO = E.EMPNO)
WHEN MATCHED THEN 
UPDATE SET T.SAL = E.SAL, T.COMM = E.COMM 
WHEN NOT MATCHED THEN 
INSERT(EMPNO, ENAME, SAL, COMM) VALUES(E.EMPNO, E.ENAME, E.SAL, E.COMM);
```



> 13. EMP_MASTER 테이블의 봉급과 커미션을 0으로 수정 후 실행

```mariadb
update EMP_MASTER
set sal = 0, comm = 0;

-- 
MERGE INTO EMP_MASTER T
USING EMP_MAIN E
ON (T.EMPNO = E.EMPNO)
WHEN MATCHED THEN 
UPDATE SET T.SAL = E.SAL, T.COMM = E.COMM 
WHEN NOT MATCHED THEN 
INSERT(EMPNO, ENAME, SAL, COMM) VALUES(E.EMPNO, E.ENAME, E.SAL, E.COMM);
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf93mokn8qj30n411kdnx.jpg" alt="image-20200529111316344" style="zoom: 33%;" /> - > <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf93n6qiw6j30ms116114.jpg" alt="image-20200529111345972" style="zoom: 33%;" />

```mariadb
update EMP_MASTER
set sal = 0, comm = 0;
-- 
MERGE INTO EMP_MASTER T
USING EMP_MAIN E
ON (T.EMPNO = E.EMPNO)
WHEN MATCHED THEN 
UPDATE SET T.SAL = E.SAL, T.COMM = E.COMM 
WHEN NOT MATCHED THEN 
INSERT(T.EMPNO, T.ENAME, T.SAL, T.COMM) VALUES(E.EMPNO, E.ENAME, E.SAL, E.COMM);
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf93qlcet7j30ms1167cp.jpg" alt="image-20200529111702413" style="zoom:33%;" /> 





## 트랜잭션(TRANSACTION)

### 정의 

- 논리단위를 형성하는 DML문의 모음을 말함 
- 하나 또는 두개 이상의 SQL문으로 이뤄진 단위를 말함 
- 하나의 트랜잭션안에 모든 SQL은 동일한 효과를 가짐
- COMMIT(저장), ROLLBACK(취소)으로 모듈단위를 실행함 



### 트랜잭션 이벤트 발생 원리

- 실행 가능한 첫번째 SQL문이 실행될 때 시작되어 다음 이벤트가 발생하면 종료됨
- 트랜잭션 이벤트
  - COMMIT
  - ROLLBACK
  - SQL 실행종료
  - 시스템 장애 고장(전기 나가는 거)
  - DDL 발생(CREATE)
- 트랜잭션이 종료가 되면 실행가능한 SQL구문이 다음 트랜잭션 실행을 자동으로 시작 
  1. 데이터 추가 (INSERT) -> 데이터 삭제(DELETE) -> COMMIT; 트랜잭션 종료
  2. 트랜잭션 시작 -> 데이터 추가 (INSERT) -> 데이터 삭제(DELETE) -> ROLLBACK; 트랜잭션 종료
  3. 트랜잭션 시작 -> 데이터 추가 (INSERT) -> 데이터 삭제(DELETE) -> CREATE; 트랜잭션 종료

- 트랜잭션 명령어 : [COMMIT], [ROLLBACK], [SAVEPOINT 이름] 
- 암시적 트랜잭션 
  - 자동 COMMIT이 발생할 경우 
    - DDL, DCL이 실행되는 경우
    - COMMIT, ROLLBACK 명시하지 않고 정상적으로 SQLPLUS가 종료될 경우 
  - 자동 ROLLBACK
    - 비정상적으로 SQLPLUS가 종료될 경우(예를 들어, exit안하고 강제종료)
    - 시스템에 장애가 있을 때...
- 명시적 트랜잭션 제어 
  - COMMIT : 보류중인 모든 데이터의 변경 내용을 영구적으로 저장하고 현재의 트랜잭션을 종료
  - SAVEPOINT 이름 : 현재의 트랜잭션내에 저장점을 표시함 
  - ROLLBACK : 보류중인 모든 데이터의 변경 내용을 버리고 현재의 트랜잭션을 종료함 
  - ROLLBACK TO SAVEPOINT 이름 : 트랜잭션 저장점으로 ROLLBACK 하여, 저장점 이후에 생성된 SQL 문장이 있는 내용 및 저장점을 버림 
- 실습

```mariadb
create table emp_res as select empno from emp;
insert into emp_res values(111);  
SAVEPOINT A; -- INSER문의 실행까지 표시 
update emp_res set empno = 3333 where empno = 111;
ROLLBACK TO A; -- UPDATE 문만 취소가 되고 INSERT 트랜잭션은 유효함
DELETE FROM EMP_RES;
COMMIT; -- SAVEPOINT A 이후에 실행된 INSERT DELETE 결과가 테이블에 영구적으로 저장됨 
```



## 시퀀스(SEQUENCE)

- 시퀀스는 static -> 모든 테이블이 공유 

```mariadb
CREATE SEQUENCE MYSC
 START WITH     1
 INCREMENT BY   5
 MAXVALUE 20
 MINVALUE 1
 CYCLE
 NOCACHE;
 
 CREATE SEQUENCE MYSC
 INCREMENT BY   5
 MAXVALUE 20
 MINVALUE 1
 CYCLE
 NOCACHE;
 
 MYSC.CURRVAL -- 현재 시퀀스 값 확인
 MYSC.NEXTVAL -- 실행 
 
 select mysc.nextval from dual;
 select mysc.CURRVAL from dual;
```



## FLOAT DATA TYPE

[Storage of Scale and Precision](https://docs.oracle.com/cd/E11882_01/server.112/e41084/sql_elements001.htm#g196646)

```mariadb
CREATE TABLE test (col1 NUMBER(5,2), col2 FLOAT(5));

INSERT INTO test VALUES (1.23, 1.23);
INSERT INTO test VALUES (7.89, 7.89);
INSERT INTO test VALUES (12.79, 12.79);
INSERT INTO test VALUES (123.45, 123.45);
INSERT INTO test VALUES (125.42, 125.42);

CREATE TABLE test_float (col1 NUMBER(3,2), col2 FLOAT(3));

INSERT INTO test_float VALUES (1.23, 1.23);
INSERT INTO test_float VALUES (7.89, 7.89);
INSERT INTO test_float VALUES (12.79, 12.79);
INSERT INTO test_float VALUES (123.45, 123.45);
INSERT INTO test_float VALUES (125.42, 125.42);


```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf95zew7thj30cm0aktae.jpg" alt="image-20200529123442639" style="zoom:50%;" />  



## REF

[]()

```mariadb
INSERT INTO address_table VALUES
  ('1 First', 'G45 EU8', 'Paris', 'CA', 'US');

INSERT INTO customer_addresses
  SELECT 999, REF(a) FROM address_table a;

SELECT address
  FROM customer_addresses
  ORDER BY address;

ADDRESS
--------------------------------------------------------------------------------
000022020876B2245DBE325C5FE03400400B40DCB176B2245DBE305C5FE03400400B40DCB1

SELECT DEREF(address)
  FROM customer_addresses;

DEREF(ADDRESS)(STREET_ADDRESS, POSTAL_CODE, CITY, STATE_PROVINCE, COUNTRY_ID)
--------------------------------------------------------------------------------
CUST_ADDRESS_TYP_NEW('1 First', 'G45 EU8', 'Paris', 'CA', 'US')
```



## tablespace

1. /system : id 생성 및 권한 설정
2. 테이블 영역을 저장할 수 있는 .dbf 생성
3. 선언된 id에게 .dbf 지정
4. 기존 서비스(.dbf) 중단하고 새롭게 매핑된 id로 접속하는 서비스를 실행
5. 생성된 id로 접속



```mariadb
-- 2번 
conn as sysdba
sys
oracle

-- 3번 
create tablespace ts_test
logging
datafile '/tmp/ts_test.dbf'
size 32m
autoextend on
next 32m maxsize 2048m
extent management local;

-- 4번 testman user 만들기
create user testman
identified by admin1234
default tablespace ts_test
temporary tablespace temp;

-- 5번 권한 부여
grant connect, resource to testman;
```

```mariadb
create user big5 identified by admin1234;
grant CREATE SESSION, ALTER SESSION, CREATE DATABASE LINK, -
  CREATE MATERIALIZED VIEW, CREATE PROCEDURE, CREATE PUBLIC SYNONYM, -
  CREATE ROLE, CREATE SEQUENCE, CREATE SYNONYM, CREATE TABLE, - 
  CREATE TRIGGER, CREATE TYPE, CREATE VIEW, UNLIMITED TABLESPACE -
  to big5;

```



- 테이블 영역생성을 잘못했을 경우

```mariadb
-- 1번 
conn as sysdba
sys
oracle

-- 2번 실행하는 .dbf 서비스를 중단 시킨다
shutdown immediate;

-- 3번 수작업으로 /test02/ts_test.dbf를 이동한다

-- 4번 경로 수정한다
startup mount;
alter database rename file '/tmp/ts_test.dbf' to '/test02/ts_test.dbf';
-- 5번 재시작후 접속한다
alter database open;

-- 
 conn testman/admin1234
```



```mariadb
CREATE TABLESPACE tbs_01 
   DATAFILE 'tbs_f2.dbf' SIZE 40M 
   ONLINE; 
   -- /u01/app/oracle/dbs여기에 생성됨ㄴ
```



````mariadb
-- 
CREATE TABLESPACE auto_seg_ts DATAFILE 'file_2.dbf' SIZE 1M
   EXTENT MANAGEMENT LOCAL
   SEGMENT SPACE MANAGEMENT AUTO;
````



- 권한에 대한 5개의 대표적인 룰

  - connect : 접속 권한
  - resource: 객체 생성, 변경, 삭제 등 시스템에 대한 기본 권한
  - dba : 데이터베이스 관리에 대한 권한
  - sysdba : 데이터베이스 시작과 종료 및 관리 권한
  - sysoper: sysdba+데이터베이스 생성에 관한 권한

  ```mariadb
  select * from dba_sys_privs; -- sys 권한
  select * from role_sys_privs; -- role 확인 
  ```

  



## PL/SQL

- 형식

```mariadb
<< label >> (optional)
DECLARE    -- Declarative part (optional)
  -- Declarations of local types, variables, & subprograms

BEGIN      -- Executable part (required)
  -- Statements (which can use items declared in declarative part)

[EXCEPTION -- Exception-handling part (optional)
  -- Exception handlers for exceptions (errors) raised in executable part]
END;
```



- PL/SQL Engine

<img  src="https://docs.oracle.com/cd/E11882_01/appdev.112/e25519/img/lnpls004.gif"/> 



> 1. 기본 문장을 출력하자 

```mariadb
SET SERVEROUTPUT ON;
BEGIN
DBMS_OUTPUT.ENABLE;
DBMS_OUTPUT.PUT_LINE('1.HELLO ORACLE');
DBMS_OUTPUT.DISABLE;
DBMS_OUTPUT.PUT_LINE('2. HELLO JAVA');
END;
/
```



> 2. i라는 변수를 선언하고 20을 대입 후. 출력해보자 

```mariadb
SET SERVEROUTPUT ON;
DECLARE 
	i integer := 20;
BEGIN 
	DBMS_OUTPUT.PUT_LINE('i의 값은 '|| i);
END;
/
```



> 3. 4칙 연산을 출력해보자 

````mariadb
SET SERVEROUTPUT ON;
DECLARE 
	i int := 100;
	j int := 200;
	hap int := 0;
	sub int :=0;
	mul int :=0;
	div int :=0;
BEGIN 
	hap := i+j;
	sub := i-j;
	mul := i*j;
	div := i/j;
	DBMS_OUTPUT.PUT_LINE(i||'+'||j||'='|| hap);
	DBMS_OUTPUT.PUT_LINE(i||'-' ||j||'='|| sub);
	DBMS_OUTPUT.PUT_LINE(i||'*' ||j||'='|| mul);
	DBMS_OUTPUT.PUT_LINE(i||'/' ||j||'='|| div);
END;
/
````



```mariadb
-- SET SERVEROUTPUT ON; 이거 안했는데 왜 출력되는걸까?
DECLARE
  first_name  CHAR(10 CHAR);
  last_name   VARCHAR2(10 CHAR);
BEGIN
  first_name := 'John ';
  last_name  := 'Chen ';
 
  DBMS_OUTPUT.PUT_LINE('*' || first_name || '*');
  DBMS_OUTPUT.PUT_LINE('*' || last_name || '*');
END;
/
```



IF THEN

IF THEN ELSE

IF THEN ELSIF



```mariadb
DECLARE
  grade CHAR(1);
BEGIN
  grade := 'B';
  
  IF grade = 'A' THEN
    DBMS_OUTPUT.PUT_LINE('Excellent');
  ELSIF grade = 'B' THEN
    DBMS_OUTPUT.PUT_LINE('Very Good');
  ELSIF grade = 'C' THEN
    DBMS_OUTPUT.PUT_LINE('Good');
  ELSIF grade = 'D' THEN
    DBMS_OUTPUT. PUT_LINE('Fair');
  ELSIF grade = 'F' THEN
    DBMS_OUTPUT.PUT_LINE('Poor');
  ELSE
    DBMS_OUTPUT.PUT_LINE('No such grade');
  END IF;
END;
/
```



### FOR LOOP

```mariadb
BEGIN
  DBMS_OUTPUT.PUT_LINE ('lower_bound < upper_bound');
 
  FOR i IN 1..3 LOOP
    DBMS_OUTPUT.PUT_LINE (i);
  END LOOP;
 
  DBMS_OUTPUT.PUT_LINE ('lower_bound = upper_bound');
 
  FOR i IN 2..2 LOOP
    DBMS_OUTPUT.PUT_LINE (i);
  END LOOP;
 
  DBMS_OUTPUT.PUT_LINE ('lower_bound > upper_bound');
 
  FOR i IN 3..1 LOOP
    DBMS_OUTPUT.PUT_LINE (i);
  END LOOP;
END;
/
```

```mariadb
BEGIN
  DBMS_OUTPUT.PUT_LINE ('upper_bound > lower_bound');
 
  FOR i IN REVERSE 1..3 LOOP
    DBMS_OUTPUT.PUT_LINE (i);
  END LOOP;
 
  DBMS_OUTPUT.PUT_LINE ('upper_bound = lower_bound');
 
  FOR i IN REVERSE 2..2 LOOP
    DBMS_OUTPUT.PUT_LINE (i);
  END LOOP;
 
  DBMS_OUTPUT.PUT_LINE ('upper_bound < lower_bound');
 
  FOR i IN REVERSE 3..1 LOOP
    DBMS_OUTPUT.PUT_LINE (i);
  END LOOP;
END;
/
```



```mariadb
BEGIN
  FOR i IN 1..3 LOOP
 DBMS_OUTPUT.PUT_LINE ('cnt: '||TO_CHAR(i));
    IF i < 3 THEN
      DBMS_OUTPUT.PUT_LINE (TO_CHAR(i));
    ELSE
       DBMS_OUTPUT.PUT_LINE ('else i: '||TO_CHAR(i));
    END IF;
  END LOOP;
END;
/
 
```



> 5. 테이블을 생성하자 

````mariadb
drop table test01;
create table test01(
	no number(3),
  irum varchar2(20));
  
begin 
	for i in 1..10 loop
		insert into test01 values(i,'홍길동'||i);
	end loop;
end;
/
````

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf9cikj1qdj30de0kwgpo.jpg" alt="image-20200529162040552" style="zoom:50%;" /> 



> 6. 사원 테이블의 10번 부서의 평균 월급을 구하는 구문을 작성하자 

```mariadb
declare -- 익명
avg01 number(7,2) :=0;
deptno01 number(7) :=10;

begin 
select avg(sal) INTO avg01 
from emp 
where deptno=deptno01
group by deptno;
dbms_output.put_line(deptno01||'번 부서의 평균은 ['||avg01||']');
end;
/
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf9cr6tj5zj30kg04gta1.jpg" alt="image-20200529162859815" style="zoom:50%;" /> 