# Day31

## 학습목표 

1. 서브 쿼리를 이용하여 테이블을 생성할 수 있다.
2. 테이블 생성 후 제약조건을 추가하고 삭제할 수 있다.
3. 생성된 테이블의 컬럼을 추가하고 수정 및 삭제를 할 수 있다.
4. 데이터 무결성에 대한 구문을 명확하게 설계할 수 있다.



## 서브 쿼리를 이용하여 테이블을 생성

> 2. 사원의 이름, 부서번호, 부서명, 부서위치를 출력 

```mariadb
select ename, deptno, dname, loc
from emp join dept using(deptno);
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf7uum20i6j30pc0oythn.jpg" alt="image-20200528092356510" style="zoom:50%;" /> 



> 3. Q2를 이용해서 TEST라는 테이블을 생성하자. 컬럼명을 동일하게 구현하자 

```mariadb
create table TEST 
as select ename, deptno, dname, loc
from emp join dept using(deptno); 
```



> 4. Q2를 이용해서 TEST02라는 테이블을 생성하자. 컬럼명을 이름, 부서번호, 부서명, 위치로 동일하게 구현하자 

```mariadb
create table TEST02
as select ename 이름, deptno 부서번호, dname 부서명, loc 위치
from emp join dept using(deptno); 
```



> 5. 'JAMES'의 월급보다 많이 받는 사원의 정보 중 이름, 월급만 추출해서 TEST03 테이블 생성 

```mariadb
create table test03
as 
select ename, sal from emp where sal > (select sal from emp where ename ='JAMES');
```



> 6. 30번 부서의 직원의 평균 월급보다 더 많이 받는 사원의 이름, 월급, 부서번호를 추출해서 TEST04 테이블을 생성 

```mariadb
create table test04
as 
select ename, sal, deptno 
from emp
where sal > (select avg(sal) from emp where deptno = 30 );
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf7v9h9phpj30ok0oyagb.jpg" alt="image-20200528093813811" style="zoom:50%;" /> 



### 구문

- AS SUBQUERY 옵션을 사용하면 테이블 생성과 행 삽입을 동시에 할 수 있음

```mariadb
create table 테이블명
AS 
SUBQUERY
```

- 특징 
  - 테이블을 생성하고 서브쿼리 실행결과가 자동으로 입력됨 
- 컬럼 정의 
  - 데이터 타입의 정의 불가 : 컬럼  이름 및 Default 값만 정의 가능
  - 컬럼 이름 생략 가능 : 서브쿼리에서 사용한 컬럼 이름이 적용
  - 제약조건: 서브쿼리에서 사용한 대상 컬럼들의 not null 조건은 자동 반영됨 
  - 생성시점에 컬럼 레벨에서 제약조건 생성 가능 -> references 제약 조건불가

### 데이터 딕셔너리 

- 사용자 테이블 
  - 일반 사용자가 생성하고 유지/관리하는 테이블
  - 사용자 데이터를 포함
- 데이터 딕셔너리 
  - 오라클 DBMS가 내부적으로 생성하고 유지/관리하는 테이블
  - 데이터베이스 정보(사용자 이름, 사용자 권한, 객체 정보, 제약 조건 등)를  포함
  - USER_XXXXX 형식의 데이터 딕셔너리에 접근 가능 

#### 테이블 이름 조회

```mariadb
-- 테이블 정보 관리 
select table_name from user_tables;

-- 테이블, 뷰, 시퀀스 정보 관리
select table_name from user_catalog;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf7vsb0h7fj30880fsq51.jpg" alt="image-20200528095619333" style="zoom:50%;" /> 

```mariadb
-- 테이블, 뷰, 시퀀스, 인덱스 등 사용자 객체의 모든 정보 관리
select object_name
from user_objects
where object_type = 'TABLE';
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf7vsl8q12j30880fs76h.jpg" alt="image-20200528095636204" style="zoom:50%;" /> 



#### 테이블 및 객체 정보 조회 

> 사용자 객체 별 개수 현황 확인

```mariadb
select object_type as 유형, count(*) as 개수 
from user_objects 
group by object_type;
```



> 테이블 별 생성일, 수정일 현황 확인

```mariadb
select object_name as 이름, object_type as 유형, created as 생성일, last_ddl_time as 최종수정일
from user_objects
where object_type = 'TABLE';
```



#### 제약조건 

- USER_CONSTRAINTS, USER_CONS_COLUMNS를 이용

  - | 항목              | 설명                                                         |
    | ----------------- | ------------------------------------------------------------ |
    | CONSTRAINT_TYPE   | - P : PRIMARY KEY<br />- U : UNIQUE<br />- R : REFERENCES<br />- C : CHECK, NOT NULL |
    | SEARCH_CONDITION  |                                                              |
    | R_CONSTRAINT_NAME |                                                              |
    | DELETE_RULE       |                                                              |

  ```mariadb
  SELECT CONSTRAINT_TYPE, SEARCH_CONDITION, R_CONSTRAINT_NAME, DELETE_RULE
  FROM USER_CONSTRAINTS;
  
  
  SELECT CONSTRAINT_NAME AS 이름,CONSTRAINT_TYPE AS 유형,COLUMN_NAME AS 컬럼,SEARCH_CONDITION AS 내용, R_CONSTRAINT_NAME AS 참조, DELETE_RULE  AS 삭제규칙 
  FROM USER_CONSTRAINTS
  JOIN USER_CONS_COLUMNS 
  USING(CONSTRAINT_NAME, TABLE_NAME)
  WHERE TABLE_NAME='EMP';
  ```



## 테이블 수정 

### 범위

- 컬럼 관련
  - 컬럼 추가/삭제
  - 데이터 타입 변경, Default 변경
  - 컬럼 이름 변경
- 제약 조건 관련
  - 제약조건 추가/삭제 
  - 제약조건 이름 변경
- 테이블 관련 
  - 테이블 이름 변경



### 구문

```mariadb
ALTER TABLE [ schema. ] table
  [ alter_table_properties
  | column_clauses
  | constraint_clauses
  | alter_table_partitioning
  | alter_external_table
  | move_table_clause
  ]
  [ enable_disable_clause
  | { ENABLE | DISABLE } { TABLE LOCK | ALL TRIGGERS }
  ] ...
  ;
```



> 7. TEST 테이블을 MYTEST로 변경

```mariadb
RENAME TEST TO MYTEST;
SELECT * FROM TAB;
```



> 8. MYTEST 테이블의 ENAME 컬럼을 EMP_NAME으로 변경해보자 

```mariadb
alter table mytest
rename column ename to emp_name;
```



> 9. MYTEST 테이블의 LOC 컬럼을 EMP_LOC로 변경해보자 

```mariadb
ALTER TABLE MYTEST
RENAME COLUMN LOC TO EMP_LOC;
```



> 10. MYTEST 테이블에 EMPNO NUMBER로 추가

```mariadb
ALTER TABLE MYTEST
ADD EMPNO NUMBER;
```



> 11. MYTEST 테이블에 NO NUMBER로 추가하고 기본값을 10으로 

```mariadb
ALTER TABLE MYTEST
ADD no number default 10;

insert into mytest(empno) values(1111);
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf7xsw7rcpj30he0s8tcx.jpg" alt="image-20200528110604577" style="zoom:50%;" /> 



> 12. test06 테이블 생성 후 ADD라는 키워드로 컬럼을 여러개 추가 

```mariadb
create table test06 as select empno, ename, deptno from emp where deptno=30;

alter table test06 
add A number
add B number 
add C number;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf7xxdaxgij30wo0bugp2.jpg" alt="image-20200528111023447" style="zoom:50%;" /> 



> 13. TEST06에 EMPNO에 PK 추가 

```mariadb
alter table test06 
add constraint PK_TEST06 primary key(empno);
```



> 14. TEST07 테이블을 생성한 후 NO와 NAME을 추가 

```mariadb
create table TEST07(
tel varchar2(40)
);



alter table test07
add no number
add name varchar2(20);

alter table test07 
add primary key(no)
modify name default 'ABC';

-- 

create table TEST07 as select ename from emp;

alter table test07
add primary key(ename)
add name varchar2(20) default 'ABC';
```



> 15. TEST04의 ENAME 컬럼에 not null 제약조건을 추가(MODIFY)

```mariadb
alter table test04
MODIFY ename not null;
```

- MODIFY에서 제약 조건 X 
  - 제약 조건은 이름이 올라가는 것이라서, add || drop만 가능 

> 16. TEST06 A,B,C 컬럼 중 A[100], B[200]만 기본값 추가 

```mariadb
alter table test06
modify a default 100
modify b default 200;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf7ypzx1d6j30x80m0dme.jpg" alt="image-20200528113754174" style="zoom:50%;" /> 



### MODIFY

```mariadb
ALTER TABLE TABLE_NAME
MODIFY (column-name datatype[default])
```

- 조건 
  1. 컬럼 데이터 타입 확인 
     - 대상컬럼의 데이터가 비어있는 경우만 해당(NULL 포함)
     - 단, CHAR <-> VARCHAR2 크기를 변경하지 않을 경우만 변환 가능 
  2. 컬럼 크기 확인 
     - NUMBER(10,2); NUMBER(8) 등으로 숫자를 구현할 때 타입에 따라 크기가 조절되어야 함 (실수냐? 정수냐?)
  3. DEFAULT 관련 확인
     - 변경 이후 부터 적용됨
     - insert가 되지 않은 데이터일 경우 기본값으로 추가

> 16. TEST06 A,B에는 100, 200 추가 된 상태

```mariadb
alter table test06
modify a default 300;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf7z22goi6j30x80i479m.jpg" alt="image-20200528114930058" style="zoom:50%;" /> 

```mariadb
alter table test06
add d number default 700; -- insert가 되지 않은 데이터일 경우 기본값으로 추가
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf7z5n92jfj312u0f443w.jpg" alt="image-20200528115256269" style="zoom:50%;" /> 



````mariadb
alter table test06
modify empno varchar2(4);
````

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf7zbpr0otj30x005aq53.jpg" alt="image-20200528115846640" style="zoom:50%;" /> 



````mariadb
SQL> alter table 
  2  test06
  3  modify ename varchar2(20);
````

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf7zh85796j30x007e0v4.jpg" alt="image-20200528120404507" /> 

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf7zh85796j30x007e0v4.jpg" alt="image-20200528120404507" style="zoom:50%;" />

- 데이터 무결성, 원래 데이터를 지키려고 함 



- null 문제

```mariadb
SQL> create table mtest(no number);
SQL> alter table mtest modify no varchar2(20);
SQL> insert into mtest values(null);
SQL> select count(*) from mtest; 
  COUNT(*)
----------
	 1
	 
SQL> select count(no) from mtest;

 COUNT(NO)
----------
	 0
	 
SQL> alter table mtest modify no number;

Table altered.

SQL> select * from mtest;

	NO
----------


```



### DROP

```mariadb
ALTER TABLE TABLE_NAME
DROP 
```

- 규칙 
  - 컬럼 하나씩 삭제 
  - 컬럼 여러개를 한번에 삭제가 가능 
  - 참조되는 부모테이블의 제약 조건을 확인 후 삭제 
- 기존에 데이터가 있을 경우, 모든 데이터를 포함해서 삭제됨 
- 기존에 작업 후에는 테이블에 반드시 컬럼이 하나 이상 존재해야함 
- 삭제된 컬럼은 복구가 불가함 
- 예시

````mariadb
ALTER TABLE ABC  
DROP COLUMN ENAME;
ALTER TABLE ABC DROP COLUMN ENAME
ALTER TABLE ABC DROP(ENAME);
ALTER TABLE ABC DROP(ENAME, ENAME, DEPNO);
````



> 18. TEST06의 B,C,D 컬럼을 동시에 삭제 

```mariadb
ALTER TABLE TEST06
DROP (B,C,D);
```



> 19. TEST06의 A컬럼을 삭제

```mariadb
ALTER TABLE TEST06
DROP COLUMN A;
```



> 20. 테이블을 생성한 후 컬럼을 삭제 

```mariadb
create table test08(
  pk number primary key, 
  fk number references dept,
  col number,
  check(pk>0 and col > 0)
);

alter table test08 
drop (pk); -- ORA-12991: column is referenced in a multi-column constraint

create table test09(
  pk number primary key, 
  fk number references test09,
  col number,
  check(pk>0 and col > 0)
);
alter table test09 
drop (pk); -- ORA-12992: cannot drop parent key column 부모 키 열을 삭제할 수 없다 

alter table test09 
drop (col); -- ORA-12991: column is referenced in a multi-column constraint 열이 다중-열 제약 조건에 참조되어 삭제할 수 없다 
```



> 21. 제약 조건이 있는 컬럼을 삭제 

```mariadb
-- 삭제 되는 컬럼이 참조하고 있는 다른 컬럼에 설정된 제약 조건까지 삭제 
alter table test09 drop (pk) cascade constraints; 
alter table test09 drop (col) cascade constraints;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf80pqk1phj30s209i0vp.jpg" alt="image-20200528124650891" style="zoom:50%;" /> 

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf80r3tn8zj313k0ek79p.jpg" alt="image-20200528124807214" style="zoom:50%;" />



> 22. TEST08이 DEPT 테이블 FK가 참조하고 있기 때문에 부모테이블인 DEPT의 DEPTNO를 삭제하게 되면 오류발생 

```mariadb
-- 참조하고 제약 조건까지 삭제 
ALTER TABLE DEPT 
DROP (DEPTNO) CASCADE CONSTRAINTS; 

-- 제약 조건 확인
SQL> select constraint_name, constraint_type from user_constraints where table_name='DEPT';
-- 결과: DEPT 테이블의 제약 조건이 없어짐 
no rows selected

ALTER TABLE TEST08 DROP(FK);
```



> 23. BIG5의 계정으로 접속한 후 테이블의 목록을 확인한다.

```mariadb
conn big5/admin1234
show user

select constraint_name, constraint_type
from user_constraints 
where table_name = 'MY';
-- 
select constraint_name, constraint_type
from user_constraints 
where table_name = 'MY02';
-- 
select constraint_name, constraint_type
from user_constraints 
where table_name = 'MYDEPT';
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf8322asbrj30gy04ajs7.jpg" alt="image-20200528140751564" style="zoom:50%;" /> 

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf838yx394j30gy04awfa.jpg" alt="image-20200528141431768" style="zoom:50%;" /> 

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf839bfoa8j30gy05aq42.jpg" alt="image-20200528141452431" style="zoom:50%;" /> 



> 24. MY가 가진 제약 조건을 삭제

```mariadb
alter table my 
drop constraint SYS_C007048;
```



> 25. MY02가 가진 제약 조건을 삭제

```mariadb
alter table my02
drop constraint SYS_C007049;
```



> 26. MYDEPT 테이블의 PK를 삭제 

```mariadb
alter table mydept 
drop constraint SYS_C007046;
```



> 27. MYDEPT 테이블의 U를 삭제함 

```mariadb
alter table mydept
drop constraint SYS_C007047;
```



> 28. MY 테이블에 MY_PK로 추가

```mariadb
delete from my where LID is null;
alter table my
add constraint MY_PK primary key(LID);
```



> 29. MY02 테이블에 PK를 추가 

```mariadb
alter table my02
add constraint MY02_PK primary key(LID);
```





> 30. MY02에 NO컬럼을 추가하고 DEPT 테이블로 참조 
>
>     MY02_REF라고 이름을 부여/ MYDEPT_PK라는 제약도 있어야함 

```mariadb
alter table my02
add constraint MY02_REF no number references mydept;

alter table mydept
add constraint MYDEPT_PK primary key(id);
```





 ```mariadb
create table MY_EXAM(
  col1 number primary key,
  ename varchar2(10),
  foreign key(col1) references emp);


SELECT CONSTRAINT_NAME AS 이름,CONSTRAINT_TYPE AS 유형,COLUMN_NAME AS 컬럼,SEARCH_CONDITION AS 내용, R_CONSTRAINT_NAME AS 참조, DELETE_RULE  AS 삭제규칙 
FROM USER_CONSTRAINTS
JOIN USER_CONS_COLUMNS 
USING(CONSTRAINT_NAME, TABLE_NAME)
WHERE TABLE_NAME='MY_EXAM';
 ```



> 31. 컬럼 이름을 변경해보자   
>     my_exam 테이블의 col1을 empno로 컬럼명 변경해보자 

```mariadb
alter table my_exam
rename column col1 to empno;
```



> 32. 제약 조건의 이름을 변경해보자 
>
>     my_exam의 제약 조건을 확인하고 제약 조건 이름을 PK_EXAM이라고 변경해보자(PK)

```mariadb
alter table my_exam
rename constraints SYS_C007108 to PK_EXAM;

alter table my_exam
rename constraints SYS_C007109 to FO_EXAM;
```



> 33. 테이블 삭제 DROP : 테이블에 포함 데이터 및 모든 정보가 삭제됨

```mariadb
drop table my;
```



> 34. 삭제 대상 테이블의 PK,UK 의 제약조건을 참조하는 테이블의 정보도 삭제해야함 

```mariadb
drop table mydept cascade constraints;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf85qbw42pj30y4070dig.jpg" alt="image-20200528154024639" style="zoom:50%;" /> 



## VIEW

- 단일 쿼리 및 서브 쿼리를 원본 테이블에 있는 데이터를 호출해서 하나의 가상 테이블로 만드는 것을 말함 [선택적인 정보만 제공함] 
- 이름은 있지만(주소가 생성됨) 크기는 없음 
- 특징 
  - 데이터 접근을 제한함 
  - 자주 사용하는 SQL문을 효율적으로 사용하기 위함
  - 복잡한 쿼리문으로 view의 객체를 생성해 놓으면, 사용자는 view에 접근했을 때, 단순 SELECT만 요청하면 됨 
  - 실제 table명과 view이 명은 달라도 됨 

### 형식

````mariadb
CREATE [OR REPLACE] [FORCE | NOFORCE] VIEW view_name [(alias[, alias ...])] 
AS SUBQUERY
[WITH CHECK OPTION [CONSTRAINT constraint_name]]
[WITH READ ONLY [CONSTRAINT constraint_name]];
````

- CREATE OR REPLACE
  - 지정한 이름의 뷰가 없으면 새로 생성, 동일 이름이 존재하면 수정(overwrite)
- FORCE | NOFORCE
  - FORCE : 베이스 테이블이 존재하지 않아도 뷰 생성 가능
  - NOFORCE : 베이스 테이블이 존재하는 경우에만 뷰 생성 가능
- ALIAS
  - 뷰에서 사용할 표현식 이름(테이블 컬럼 이름 의미)
  - 생략 : SUBQUERY에서 사용한 이름 적용
  - ALIAS 개수 : SUBQUERY에서 사용한 SELECT LIST 개수와 일칠 
- SUBQUERY
  - 뷰에서 표현하는 데이터를 생성하는 SELECT 구문
- 제약조건 
  - WITH CHECK OPTION : 뷰를 통해 접근 가능한 데이터에 대해서 DML 작업허용
  - WITH READ ONLY: 뷰를 통해 DML 작업 허용 안함
  - 제약조건으로 간주되므로 별도 이름 지정 가능 



### 예제

```mariadb
create VIEW TEST_VIEW 
as select ename, deptno, dname, loc
from emp join dept using(deptno); 
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf86pevesbj30p808ewgj.jpg" alt="image-20200528161408216" style="zoom:50%;" /> 



```mariadb
SELECT COLUMN_NAME, DATA_TYPE, NULLABLE
FROM USER_TAB_COLS
WHERE TABLE_NAME='TEST_VIEW';

-- VIEW DROP 
SQL> DROP VIEW TEST_VIEW;

View dropped.

CREATE OR REPLACE VIEW TEST_VIEW 
as select ename, deptno, dname, loc
from emp join dept using(deptno); 

-- VIEW는 덮어쓰기 가능 
CREATE OR REPLACE VIEW TEST_VIEW 
as select ename, deptno, dname, loc
from emp join dept using(deptno);
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf86shy88kj30lu0f2wjh.jpg" alt="image-20200528161705843" style="zoom:50%;" /> 



````mariadb
CREATE OR REPLACE VIEW TEST02_VIEW(이름, 부서번호, 부서명, 위치)
as select ename, deptno, dname, loc
from emp join dept using(deptno); 
````

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf86wo9wxaj30pq0r4qcm.jpg" alt="image-20200528162106577" style="zoom:50%;" /> 



- view를 생성하고 select문과 desc구문으로 확인

- 특징 
  - view를 통해서 DML작업이 가능함(단, 조건에 맞는 컬럼을 추출했을 때만)
  - commit|rollback 작업이 필요함 
    - with read only : dml 불가 
    - with check option : 뷰를 통해 dml 사용 가능, 단 INSERT/UPDATE 작업제한(DELETE 제한이 없다)

```mariadb
CREATE OR REPLACE VIEW test03_view
as 
select ename, sal from emp where sal > (select sal from emp where ename ='JAMES');

insert into test03_view(EMPNO) values('1111',100); -- ORA-01400: cannot insert NULL into ("BIG6"."EMP"."EMPNO") -- insert X
```



```mariadb
create OR REPLACE view test04_view
as select * from emp

insert into test04_view(empno) values(1111);


-- read only
create OR REPLACE view test04_view
as select * from emp
with read only;

insert into test04_view(empno) values(2222); -- ORA-42399: cannot perform a DML operation on a read-only view
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf876h6j02j30na076dh9.jpg" alt="image-20200528163031990" style="zoom:50%;" /> 



> 36. VIEW 생성시 WITH CHECK OPTION을 써보자 

```mariadb
CREATE OR REPLACE VIEW TEST05_VIEW
AS 
SELECT EMPNO, ENAME, SAL FROM EMP WHERE SAL > 2000
WITH CHECK OPTION;

INSERT INTO TEST05_VIEW VALUES(1123, 'BIBI', 1200); -- ORA-01402: view WITH CHECK OPTION where-clause violation

INSERT INTO TEST05_VIEW VALUES(1123, 'BIBI', 2700);
UPDATE TEST05_VIEW SET SAL = 3000 WHERE EMPNO = 1123;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf87igvxfhj30ss07676h.jpg" alt="image-20200528164203758" style="zoom:50%;" /> 

