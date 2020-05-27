# Day30 

## 학습목표

1. 데이터를 저장하는 테이블을 생성할 수 있다
2. 제약규칙과 데이터 무결성에 대하여 구분하고 숙지할 수 있다
3. 생성된 테이블을 이용해서 속성과 테이블의 관계를 알 수 있다
4. user_oo테이블의 내용을 확인하여 생성된 테이블의 정보를 확인 할 수 있다





## 테이블

```mariadb
SQL > desc user_tables; -- 현재 계정으로 접속하여 생성된 테이블의 정보를 취득 
SQL > select table_name, status, logging, num_rows from user_tables;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf6p6f7dn7j30rm0tqthp.jpg" alt="image-20200527092204956" style="zoom:50%;" /> 



```mariadb
desc user_constraints; -- 테이블에 대한 제약 정보를 취득
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf6p8bhxfdj30x60sids2.jpg" alt="image-20200527092357756" style="zoom:50%;" /> 

```mariadb
select constraint_name, constraint_type, table_name, search_condition, r_owner, r_constraint_name
from user_constraints;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf6pb53yv1j30yu03sq4s.jpg" alt="image-20200527092640150" style="zoom:50%;" /> 



### 테이블 생성 

```mariadb
CREATE TABLE table_name

```

#### Naming Rule

- 테이블 및 컬럼 이름
  - 문자로 시작, 30자 이하 
  - 영문 대/소문자, 숫자, 특수문자(_, $, #), 한글만 포함 가능 
- 중복되는 이름은 사용할 수 없음 
- 예약 키워드(CREATE, TABLE, COLUMN 등)는 사용할 수 없음



#### 실습

```mariadb
create table orders
(orderno char(4),
 custno char(4),
 orderdate date default sysdate,
 shipdate date,
 shipaddress varchar2(40),
 quantity number
);
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf6puz5yljj30zg0bmgpd.jpg" alt="image-20200527094544103" style="zoom:50%;" /> 



```mariadb
select constraint_name, constraint_type, table_name
from user_constraints
where table_name = 'orders';
```



```mariadb
insert into orders(orderno, custno, orderdate) values ('1','1',sysdate);
insert into orders(orderno, custno) values ('1','1');
insert into orders(orderno, custno) values (2,2);
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf6q3dndvvj313y06mwgp.jpg" alt="image-20200527095348631" style="zoom:50%;" />  

```mariadb
insert into orders(orderno, custno, orderdate) values ('3','3','20-05-27');
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf6q4n31kqj313y08e0vf.jpg" alt="image-20200527095501467" style="zoom:50%;" /> 

```mariadb
insert into orders(orderno, custno, orderdate) values ('4','4', to_date('20-05-27','yyyy-MM-DD'));
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf6q7lbjhnj313y09gtbs.jpg" alt="image-20200527095751358" style="zoom:50%;" /> 

```mariadb
insert into orders(orderno, custno, orderdate) values ('5','5', to_date('2020-05-27','yyyy-MM-DD'));
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf6q8h6fnqj313y0autc6.jpg" alt="image-20200527095842308" style="zoom:50%;" /> 

```mariadb
insert into orders(orderno, custno, orderdate) values ('6','6', to_date('2020/05/27','yyyy-MM-DD'));
```



## 제약조건

### 데이터 무결성 

- 데이터베이스에 저장되어 있는 데이터가 손사오디거나 원래의 의미를 잃지 않고 유지하느 ㄴ상태 
- 원래의 데이터를 살리자 

- 데이터 무결성의 제약조건 
  - 데이터 무결성을 보장하기 위해 오라클에서 지원하는 방법
  - 예) 유효하지 않은 데이터 입력방지, 유효한 범위에서만 데이터 변경/삭제 작업 허용

### 제약 조건 

- not null 
  - 해당 컬럼에 null을 포함되지 않도록 함 
  - 설정레벨: 컬럼 
- unique
  - 해당 컬럼 또는 컬럼 조합 값이 유일하도록 함(null값 포함O) 
  - 설정레벨: 컬럼, 테이블 
- primary key
  - 각 행을 유일하게 식별할 수 있도록 함(null값 포함X)
  - 설정레벨: 컬럼, 테이블
- references table(column_name)
  - 해당 컬럼이 참조하고 있는 테이블(부모테이블)의 특정 컬럼 값들과 일치하거나 또는 null이 되도록 보장함 
  - 설정레벨: 컬럼, 테이블
- check 
  - 해당 컬럼에 특정 조건을 항상 만족시키도록 함 
  - 설정레벨: 컬럼 테이블 
- 이름으로 관리 
  - 문자로 시작, 길이는 30자까지 가능
  - 이름을 따로 지정하지 않으면 자동 생성(SYS_Cxxxxx 형식)
- 생성 시기
  - 테이블 생성과 동시 
  - 테이블을 생성한 후 
- 컬럼 레벨 또는 테이블 레벨에서 정의 할 수 있음
  - not null은 '컬럼 레벨'에서만 가능
  - 컬럼 여러개를 조합하는 경우에는 '테이블 레벨'에서만 가능

#### not null

```mariadb
create table not_null(
	id varchar2(10) not null
);

select constraint_name, constraint_type, table_name, search_condition, r_owner, r_constraint_name
from user_constraints
where table_name = 'not_null';

SELECT CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME, SEARCH_CONDITION, R_OWNER, R_CONSTRAINT_NAME
FROM USER_CONSTRAINTS
WHERE TABLE_NAME ='NOT_NULL';

insert into not_null values(null); -- ORA-01400: cannot insert NULL into ("BIG5"."NOT_NULL"."ID")
```



#### unique

```mariadb
create table table_unique(
	id varchar2(10) unique
);

SELECT CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME, SEARCH_CONDITION, R_OWNER, R_CONSTRAINT_NAME
FROM USER_CONSTRAINTS
WHERE TABLE_NAME ='TABLE_UNIQUE';
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf6s90gu73j313y0383zz.jpg" alt="image-20200527110825009" style="zoom:50%;" /> 



```mariadb
insert into table_unique values(null);
insert into table_unique values(null);
insert into table_unique values(null);
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf6sbccfcmj30l809ejt2.jpg" alt="image-20200527111039491" style="zoom:50%;" /> 



> 중복값 허용하지 않음 

````mariadb
insert into table_unique values(1);
insert into table_unique values(1); -- ERROR) ORA-00001: unique constraint (BIG5.SYS_C007036) violated
````

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf6scq4254j30te0ckdkc.jpg" alt="image-20200527111159233" style="zoom:50%;" /> 



```mariadb
create table table_unique2
(id char(3),
 sname varchar2(20),
 scode char(2),
 constraint tn2_id_un unique (id,sname)
);

SELECT CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME, SEARCH_CONDITION, R_OWNER, R_CONSTRAINT_NAME
FROM USER_CONSTRAINTS
WHERE TABLE_NAME ='TABLE_UNIQUE2';
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf6sj8gqnzj31j8022jso.jpg" alt="image-20200527111814393" style="zoom:50%;" /> 



```mariadb
insert into table_unique2 values(1,1,1);
insert into table_unique2 values(1,1,1); --  ORA-00001: unique constraint (BIG5.TN2_ID_UN) violated
insert into table_unique2 values(1,2,1);
insert into table_unique2 values(2,2,1);

-- null 허용 
insert into table_unique2 values(null,null,1);
insert into table_unique2 values(null,null,1);
insert into table_unique2 values(null,null,1);
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf6sl88f3jj30q80es0x0.jpg" alt="image-20200527112009272" style="zoom:50%;" /> 



> EMP의 제약 조건을 확인한다

```mariadb
SELECT CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME, SEARCH_CONDITION, R_OWNER, R_CONSTRAINT_NAME
FROM USER_CONSTRAINTS
WHERE TABLE_NAME ='EMP';
```

![image-20200527112349421](https://tva1.sinaimg.cn/large/007S8ZIlgy1gf6sp1bupwj31l002uq4m.jpg)



> MYTEST라는 테이블을 EMP랑 동일하게 생성 후 제약 조건이 추가되었는지 확인한다.

```mariadb
create table mytest
as select * from emp;

-- 제약조건 안옴(no rows selected) --구조만 옴 
SELECT CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME, SEARCH_CONDITION, R_OWNER, R_CONSTRAINT_NAME
FROM USER_CONSTRAINTS
WHERE TABLE_NAME ='MYTEST';
```



> Q. TABLE_UNIQUE3

```mariadb
create table table_unique3
(id char(3) unique, 
 sname varchar2(20) unique, 
 scode char(2)
);

SELECT CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME, SEARCH_CONDITION, R_OWNER, R_CONSTRAINT_NAME
FROM USER_CONSTRAINTS
WHERE TABLE_NAME ='TABLE_UNIQUE3';
```



> 'ID'컬럼과 'SNAME'컬럼에 각각 설정되어서, 중복된 'SNAME'컬럼 값이 입력될 수 없음 
>
> -> 두 컬럼의 조합 결과를 유일하게 하려면 '테이블 레벨'에서 생성 

```mariadb
insert into table_unique3 values('100', 'ORACLE','01');
INSERT INTO TABLE_UNIQUE3 VALUES('200', 'ORACLE','01'); -- ORA-00001: unique constraint (BIG5.SYS_C007039) violated
```



> 제약 조건 일므을 동일하게 해서 'ID'컬럼과 'SNAME' 컬럼 조합 결과를 유일하게 하려고 했음 
>
> -> 두 컬럼의 조합 결과를 유일하게 하려면 '테이블 레벨'에서 생성

```mariadb
CREATE TABLE TABLE_UNIQUE4
(ID CHAR(3) CONSTRAINT TN4_ID_UN UNIQUE,
 SNAME VARCHAR2(20) CONSTRAINT TN4_ID_UN UNIQUE, -- ORA-02264: name already used by an existing constraint
 SCODE CHAR(2)); 
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf6t37bno8j30s20aqgqx.jpg" alt="image-20200527113726146" style="zoom:50%;" /> 



### PRIMARY KEY

- unique+not null 의미
- 테이블 당 1개만 생성 가능 

```mariadb
create table table_pk
(id char(3) constraint tp_id primary key,
sname varchar2(20));

insert into table_pk values('100','ORACLE');
insert into table_pk values('100','IBM'); -- ORA-00001: unique constraint (BIG5.SYS_C007041) violated
insert into table_pk values(null,'SUN'); -- ORA-01400: cannot insert NULL into ("BIG5"."TABLE_PK"."ID")
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf6t89q60xj30em072mye.jpg" alt="image-20200527114218821" style="zoom:50%;" /> 



> 제약 조건 이름은 중복되면 안된다

```mariadb
create table table_pk02
(id char(3) constraint tp_id primary key, -- ORA-02264: name already used by an existing constraint
sname varchar2(20));
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf6tax9snpj30mk072gnd.jpg" alt="image-20200527114451441" style="zoom:50%;" /> 



> primary key는 테이블에 하나만 있어야 함 

```mariadb
create table table_pk02
(id char(3) primary key,
 pw char(3) primary key, -- ORA-02260: table can have only one primary key
sname varchar2(20));
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf6tdc8ydrj30no0b4tco.jpg" alt="image-20200527114709886" style="zoom:50%;" /> 



#### 조합 컬럼 생성

```mariadb
create table table_pk02
( id char(3),
 sname varchar2(20),
 scode char(2),
 constraint tp2_pk primary key(id,sname)
);
insert into table_pk02 values('100', 'ORACLE','01');
insert into table_pk02 values('100', 'ORACLE','02'); -- ORA-00001: unique constraint (BIG5.TP2_PK) violated
insert into table_pk02 values(null, 'ORACLE','01'); -- ORA-01400: cannot insert NULL into ("BIG5"."TABLE_PK02"."ID")
```

```mariadb
SELECT CONSTRAINT_NAME, CONSTRAINT_TYPE, TABLE_NAME, SEARCH_CONDITION, R_OWNER, R_CONSTRAINT_NAME
FROM USER_CONSTRAINTS
WHERE TABLE_NAME ='TABLE_PK02';
```





### FOREIGN KEY

- 참조 테이블의 컬럼값과 일치하거나 null 상태를 유지하도록 함
- 참조되는 대상의 테이블은 반드시 PK만 참조됨 
- PK는 반드시 하나만 존재하기 때문에 테이블명만 명시하고 참조해도 자동으로 PK컬럼을 참조하게 됨 
- 만일 참조되는 대상 테이블의 컬럼을 명시하게 되면 PK, U가 지정되어 있어야 함 
  - ex) create table table_fk02(lid number references dept(id)); 
  - dept 테이블의 id는 PRIMARY KEY 이거나 UNIQUE KEY로 지정되어 있음 
- 반드시 참조되는 테이블에는 데이터가 존재해야함 
  - 없으면 ORA-02291 에러 발생 

```mariadb
SELECT CONSTRAINT_NAME, CONSTRAINT_TYPE
FROM USER_CONSTRAINTS
WHERE TABLE_NAME ='DEPT';

select * from dept;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf6toargh6j30ic08o76k.jpg" alt="image-20200527115742739" style="zoom:50%;" /> 

```mariadb
SQL> insert into emp(empno, deptno) values(1,10);

1 row created.

SQL> insert into emp(empno, deptno) values(2,10);        

1 row created.

SQL> insert into emp(empno, deptno) values(3,50);
insert into emp(empno, deptno) values(3,50)
*
ERROR at line 1:
ORA-02291: integrity constraint (BIG5.FK_DEPTNO) violated - parent key not
found


SQL> insert into emp(empno, deptno) values(3,null);

1 row created.
```



#### 컬럼 레벨에서 생성

```mariadb
create table table_fk
(id char(3),
 sname varchar2(20),
 lid NUMBER(2) references dept(DEPTNO) -- dept : 참조 테이블/ DEPTNO: 참조 컬럼 
);

SELECT CONSTRAINT_NAME, CONSTRAINT_TYPE
FROM USER_CONSTRAINTS
WHERE TABLE_NAME ='TABLE_FK';
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf6ttsuma3j30hi054gmi.jpg" alt="image-20200527120259753" style="zoom:50%;" /> 



> 참조되는 대상의 테이블은 반드시 PK만 참조됨 

```mariadb
create table table_fk02(
lid number references dept);
```



```mariadb
create table mydept(
id number primary key,
no number unique
);

SELECT CONSTRAINT_NAME, CONSTRAINT_TYPE
FROM USER_CONSTRAINTS
WHERE TABLE_NAME ='MYDEPT';

insert into mydept values(1,1);
insert into mydept values(2,2);
insert into mydept values(3,3);
insert into mydept values(4,4);
insert into mydept values(5,0);


create table my(lid number references mydept);
create table my02(lid number references mydept(no));

SELECT CONSTRAINT_NAME, CONSTRAINT_TYPE
FROM USER_CONSTRAINTS
WHERE TABLE_NAME ='MY';

SELECT CONSTRAINT_NAME, CONSTRAINT_TYPE
FROM USER_CONSTRAINTS
WHERE TABLE_NAME ='MY02';

insert into my values(1);
insert into my02 values(1);

insert into mydept values(6,null);
insert into mydept values(7,7);
```



````mariadb
create table mydept03(
id number primary key,
no number);

create table my04(lid number references mydept02(no));
````



#### 테이블 레벨에서 생성

```mariadb
create table my05
(id char(3),
 sname varchar2(20),
 lid char(2),
 [constraint fk1] foreign key (lid) references mydept02(no));
 
create table my05
(sname varchar2(20),
lid NUMBER,
foreign key (lid) references mydept02(no));
```



#### deletion option

- foreign key 제약 조건을 생성할 때, 참조 컬럼 값이 삭제되는 경우 foreign key 컬럼값을 어떻게 처리할 지 지정 가능 

```mariadb
[constraint constraint_name] constraint_type on delete set null; 
-- 또는
[constraint constraint_name] constraint_type on delete cascade;
```

- on delete set null : 참조 컬럼 값이 삭제 될때, foreign key 컬럼 값을 null로 변경하는 옵션

- on delete cascade; 참조 컬럼 값이 삭제될 때, foreign key 컬럼 값도 함께 삭제(행 삭제 의미)하는 옵션 



### CHECK

- 각 컬럼값이 만족해야 하는 조건을 지정 

  ```mariadb
  create table table_check 
  (emp_id char(3) primary key,
   salary number check(salary>0),
   marriage char(1),
   constraint chk_mrg check(marriage in('Y','N'))
  );
  
  SELECT CONSTRAINT_NAME, CONSTRAINT_TYPE, SEARCH_CONDITION
  FROM USER_CONSTRAINTS
  WHERE TABLE_NAME ='TABLE_CHECK';
  ```

  ```mariadb
  insert into table_check
  values('100',-100,'Y'); -- salary =-100 이므로 check 제약 조건에 위배 
  
  insert into table_check
  values('100',500,'?'); -- marriage = '?' 이므로 check 제약조건에 위배 
  
  insert into table_check values('1',900,'Y'); 
  insert into table_check values('2',900,'y'); -- ORA-02290: check constraint (BIG5.CHK_MRG) violated
  ```



- 변하는 값은 조건으로 사용 할 수 없음

  ```mariadb
  create table table_check2
  (id char(3) primary key,
   hiredate date check(hiredate < sysdate)); -- ORA-02436: date or system variable wrongly specified in CHECK constraint
  ```

  

- check 조건을 여러개 사용할 수 있음

  ```mariadb
  create table table_check3
  (eid char(3) primary key,
   ename varchar2(10) not null,
   salary number,
   marriage char(1),
   check(salary > 0 and salary < 1000000));
  ```

  

![image-20200527144305382](https://tva1.sinaimg.cn/large/007S8ZIlgy1gf6ygg9xnjj31as0qinpe.jpg)



### 서브쿼리를 활용한 테이블 생성 구문

"AS SUBQUERY"옵션을 사용하면 테이블 생성과 행 삽입을동시에 할 수 있음



````mariadb
CREATE TABLE TB_DEPARTMENT(
 DEPARTMENT_NO VARCHAR2(10) PRIMARY KEY,
  DEPARTMENT_NAME VARCHAR2(20) NOT NULL,
  CATEGORY VARCHAR2(20),
  OPEN_YN CHAR(1),
  CAPACITY NUMBER
);

CREATE TABLE TB_PROFESSOR(
  PROFESSOR_NO VARCHAR2(10) PRIMARY KEY,
  PROFESSOR_NAME VARCHAR2(30) NOT NULL,
  PROFESSOR_SSN VARCHAR2(14),
  PROFESSOR_ADDRESS VARCHAR2(100),
  DEPARTMENT_NO VARCHAR2(10) REFERENCES TB_DEPARTMENT
);

CREATE TABLE TB_STUDENT(
 STUDENT_NO VARCHAR2(10) PRIMARY KEY,
  DEPARTMENT_NO VARCHAR2(10) NOT NULL references TB_DEPARTMENT,
  STUDENT_NAME VARCHAR2(30) NOT NULL,
  STUDENT_SSN VARCHAR2(14),
  STUDENT_ADDRESS VARCHAR2(100),
  ENTRANCE_DATE DATE,
  ABSENCE_YN CHAR(1) check(ABSENCE_YN in('Y','N')),
  COACH_PROFESSOR_NO VARCHAR2(10) REFERENCES TB_PROFESSOR
);

CREATE TABLE TB_CLASS(
	CLASS_NO VARCHAR2(10) PRIMARY KEY,
  DEPARTMENT_NO VARCHAR2(10) NOT NULL REFERENCES TB_DEPARTMENT,
  PREATTENDING_CLASS_NO VARCHAR2(10) REFERENCES TB_CLASS,
  CLASS_NAME VARCHAR2(30) NOT NULL,
  CLASS_TYPE VARCHAR2(10)
);

CREATE TABLE TB_CLASS_PROFESSOR(
 CLASS_NO VARCHAR2(10) REFERENCES TB_CLASS,
 PROFESSOR_NO VARCHAR2(10) REFERENCES TB_PROFESSOR, 
 constraint TB_CLASS_PROFESSOR_PK primary key(CLASS_NO, PROFESSOR_NO)
);

CREATE TABLE TB_GRADE(
  TERM_NO VARCHAR2(10),
  CLASS_NO VARCHAR2(10) REFERENCES TB_CLASS,
  STUDENT_NO VARCHAR2(10) REFERENCES TB_STUDENT,
  POINT NUMBER(3,2),
  CONSTRAINT TB_GRADE_PK PRIMARY KEY(TERM_NO, CLASS_NO, STUDENT_NO)
);
````



```mariadb
CREATE TABLE TB_CLASS(
	CLASS_NO VARCHAR2(10) NOT NULL,
  DEPARTMENT_NO VARCHAR2(10) NOT NULL,
  PREATTENDING_CLASS_NO VARCHAR2(10),
  CLASS_NAME VARCHAR2(30) NOT NULL,
  CLASS_TYPE VARCHAR2(10),
  CONSTRAINT PK_CLASS PRIMARY KEY(CLASS_NO),
  CONSTRAINT FK_CLASS_01 FOREIGN KEY (PREATTENDING_CLASS_NO),
  CONSTRAINT FK_CLASS_02 FOREIGN KEY (DEPARTMENT_NO) 
  REFERENCES TB_DEPARTMENT (DEPARTMENT_NO));
```

- 부모 테이블의 primary key가 컬럼 두개로 묶여 있으면, 자식 테이블도 두 개의 컬럼을 참조함 

