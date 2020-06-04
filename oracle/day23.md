# Day 23

[TOC]



## 학습 목표

1. db server를 설치하고 클라이언트로 통신을 할 수 있다. 
2. 임의의 계정과 권한을 설정한 다음 jdbc를 설치 할 수 있다. 
3. XE(SID)설정 파일을 숙지 할 수 있다. 
   1. 11_ver = XE /OracleServiceXE /OracleServiceOcl
   2. /u01/app/oracle/product/11.2.0/xe/network/admin
      1. listener.ora 
      2. tnsnames.ora
4. DB의 개념을 살펴보고 SELECT 구문을 활용할 수 있다. 





## 데이터베이스 & DBMS



### DBMS 사용 이점 

1. 데이터 독립화 
   1. 데이터와 응용 프로글매을 분리시킴으로써 상호 영향 정도를 줄일 수 있음
2. 데이터 중복 최소화/데이터 무결성 보장 
   1. 중복되는 데이터를 최소화 시키면 데이터 무결성이 손상될 가능성이 줄어듦
   2. 중복되는 데이터를 최소화 시키면 필요한 저장 공간의 낭비를 줄일 수 있음 
3. 데이터 보안 향상 
   1. 응용 프로그램은 DBMS를 통해 DBMS가 허용하는 데이터에만 접근할 수 있음
   2. 권한에 맞게 데이터 접근을 제한하거나 데이터를 암호화 시켜 저장 가능
4. 관리 편의성 향상 
   1. 다양한 방법으로 데이터를 배업할 수 있음
   2. 장애 발생 시 데이터를 복구 가능



## RDBMS

### 주요용어 

#### null

#### 기본키 

- 각 행을 식별할 수 있는 컬럼 또는 컬럼 조합 
- 컬럼 라벨, 테이블 라벨 

#### 외부키

- 다른 테이블의 기본키를 참조하는 컬럼(또는 컬럼 조합)
- 테이블의 연관 관계를 표현 



### SQL

- 관계형 데이터베이스에서 데이터를 조회하거나 조작하기 위해 사용하는 표준 검색 언어 
- 원하는 데이터를 찾는 방법(절차)이 아닌 조건을 기술하는 방식 -> 어떤 방법으로 데이터를 찾을 것인지는 DBMS가 결정 

#### DML (Data Manipulation Language) - 데이터 조작어

- 정의된 데이터베이스에 입력된 레코드를 **조회하거나 수정하거나 삭제**하는 등의 역할을 하는 언어
- 데이터베이스 사용자가 응용 프로그램이나 질의어를 통하여 저장된 데이터를 실질적으로 처리하는데 사용하는 언어
- 데이터베이스 사용자와 데이터베이스 관리 시스템 간의 인터페이스를 제공
- 종류
  1. SELECT : 데이터를 조회하는 역할
  2. INSERT : 데이터를 삽입하는 역할
  3. UPDATE : 데이터를 수정하는 역할
  4. DELETE : 데이터를 삭제하는 역할

##### DQL(Data Query Language) - SELECT 

- 데이터 검색

- 일부에서는 DML에서 SELECT 만을 따로 분리해서 DQL (Data Query Language) 나 

  간단히 QUERY 로 표현



#### DDL (Data Definition Language) - 데이터 정의어  

- 데이터베이스를 정의하는 언어 
- 데이터리를 **생성, 수정, 삭제**하는 등의 데이터의 전체의 골격을 결정하는 역할을 하는 언어
- SCHEMA, DOMAIN, TABLE, VIEW, INDEX를 정의하거나 변경 또는 삭제할 때 사용하는 언어
- 데이터 베이스 관리자나 데이터베이스 설계자가 사용
- 종류
  1. CREATE : 데이터베이스, 테이블등을 생성하는 역할
  2. ALTER : 테이블을 수정하는 역할
  3. DROP : 데이터베이스, 테이블을 삭제하는 역할
  4. TRUNCATE : 테이블을 초기화 시키는 역할

#### DCL(Data Control Language) - 데이터 제어어 

- 데이터베이스에 접근하거나 객체에 권한을 주는등의 역할을 하는 언어
- 데이터를 제어하는 언어
- 데이터의 보안, 무결성, 회복, 병행 수행제어 등을 정의하는데 사용
- 종류 
  1. GRANT: 특정 데이터베이스 사용자에게 특정 작업에 대한 수행권한 부여

  2. REVOKE: 특정 데이터베이스 사용자에게 특정 작업에 대한 수행 권한을 박탈, 회수

  3. COMMIT: 트랜잭션의 작업을 결과를 반영하는 역할

  4. ROLLBACK: 트랜잭션의 작업을 취소 및 원래대로 복구하는 역할                                                                                                                                       

##### TCL(Transaction Control Language) - 트랜잭션 제어어

- 트랜잭션 제어 
- 일부에서는 DCL 에서 트랜잭션을 제어하는 명령인 COMMIT 과 ROLLBACK 만을 따로 분리해서 TCL 라고 표현



### 주요 데이터 타입



## SELECT

### 규칙

```mariadb
SELECT 컬럼리트스[*] (as) 별칭 ,,,, <<--- 2
FROM 테이블명 별칭 ,,,,; <<--- 1 
```



### 예제

1. Emp(사원테이블)의 모든 컬럼을 출력하자 

   ```mariadb
   select *
   from emp;
   ```

2. ```
   select ename, sal
   from emp;
   ```

3. ```
   select ename, sal, mgr
   from emp;
   ```

   

4. ```
   select EMPNO, HIREDATE, DEPTNO
   from emp;
   ```

5. ```mariadb
   select ename, empno, sal, comm 
   from emp;
   ```

6. ```mariadb
   select * from dept;
   ```

7. ```mariadb
   DESC dept;
   ```

8. ```mariadb
   select * from tab;
   ```

9. ```mariadb
   select e.ename, e.sal, e.deptno, d.loc  
   from emp e, dept d 
   where e.DEPTNO = d.DEPTNO;
   ```

   ```
   select 사원.ename, 사원.sal, 사원.deptno, 부서.loc  
   from emp 사원, dept 부서;
   ```

   ```
   SELECT ename as 이름, sal as 봉급, deptno as '부서 번호'
   FROM emp;
   ```

   ```
   SELECT ename, sal, deptno
   FROM emp;
   ```

10. ```
    select ename as 이름, sal as 봉급, deptno as "부서 번호"
    from emp;
    
    *VARCHAR2, VARCHAR, CHAR 이 문자를 나타내는 데이터 타입, ''로 값을 입력함 
    ```

    

11. 연결 문자열 ||

    ```
    select ename || '님'
    from emp;
    ```

12. 사원의 이름과 봉급과 커미션을 출겨하되 봉급은 연봉이라고 출력하고 연봉을 구하자 

    ```
    select ename, sal*12 연봉, comm
    from emp;
    ```

13. 사원 테이블의 봉급을 구하자 

    ```
    select ename, sal, comm, sal+comm 봉급
    from emp;
    ```

14. NVL(컬럼명, 초기값) : 컬럼에 NULL을 찾아서 초기값으로 대입함 

    ```mariadb
    select ename, sal, comm, NVL(sal+comm,0) 봉급
    from emp;
    ```

15. 커미션이 책정되지 않은 사원은 sal로 대처해서 봉급을 구하자 

    ```
    select ename, sal, comm, NVL(comm,sal) + sal 봉급
    from emp;
    ```

16. select from where 

    ```
    select --- 3
    from --- 1
    where --- 2
    ```

17. 사원 테이블에서 이름이 ford인 데이터만 출력 

    ```
    select *
    from emp
    where ename='FORD';
    ```

18. 부서번호가 10인 사원 출력

    ```mariadb
    select *
    from emp
    where deptno=10;
    ```

    

19. 봉급이 2500 이상인 사원을 출력

    ```mariadb
    select *
    from emp
    where sal>=2500;
    ```

    

20. 입사일이 81년도 5월 이전에 입사한 사원을 출력 

    ```mariadb
    select *
    from emp
    where hiredate<'81/05/01';
    ```

    

## Create

```mariadb
CREATE TABLE TEST(
SU NUMBER,
MY_DATE DATE
);
```

```mariadb
CREATE TABLE TEST02 AS SELECT * FROM EMP;

CREATE TABLE TEST03(ENAME,SAL) AS SELECT ENAME, SAL FROM EMP;

CREATE TABLE TEST04(MYNAME,MYSAL) AS SELECT ENAME, SAL FROM EMP;
```



```mariadb
DESC 
USER_TABLES;
```

```mariadb
SELECT TABLE_NAME, STATUS, LOGGING
FROM USER_TABLES;
```

```mariadb
DROP TABLE TEST02;
DROP TABLE TEST03;
DROP TABLE TEST04;
```



```mariadb
select *
from emp
where deptno=10;

select *
from emp
where sal>=2500;

select *
from emp
where hiredate<'81-05-01';


CREATE TABLE TEST(
SU NUMBER,
MY_DATE DATE
);

CREATE TABLE TEST02 AS SELECT * FROM EMP;

DESC TEST02;

CREATE TABLE TEST03(ENAME,SAL) AS SELECT ENAME, SAL FROM EMP;

CREATE TABLE TEST04(MYNAME,MYSAL) AS SELECT ENAME, SAL FROM EMP;

SELECT * FROM TAB;

SELECT TABLE_NAME, STATUS, LOGGING
FROM USER_TABLES;

DROP TABLE TEST02;
DROP TABLE TEST03;
DROP TABLE TEST04;

HELP SHOW;

SELECT TABLE_NAME FROM USER_TABLES;

SELECT * FROM USER_RECYCLEBIN;

INSERT INTO TEST VALUES(1, '70/01/01');

SELECT * FROM TEST;

INSERT INTO TEST VALUES(1, '71-01-01');

INSERT INTO TEST VALUES(1, '1972-03-01');

INSERT INTO TEST VALUES(1, '2000-03-01');

INSERT INTO TEST VALUES(1, '2020-05-01');

INSERT INTO TEST VALUES(1, SYSDATE);

SELECT * FROM TEST WHERE my_date='70/01/01';

```

javaFX

![image-20200518165846882](https://tva1.sinaimg.cn/large/007S8ZIlgy1gewnsu2yvbj316y0k2k0p.jpg)

