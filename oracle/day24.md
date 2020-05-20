# Day24

## 학습 목표

1. 단일 SELECT 쿼리 문장



## where

```mariadb
select *
from emp;

select empno, ename, sal
from emp
where empno = 7788;

select empno, ename, sal
from emp
where ename = 'SMITH';

select * 
from emp
where hiredate = '1980/12/17';

select ename, sal, job
from emp
where job like 'SALESMAN';

select deptno,ename, sal
from emp
where deptno=10;

select ename, sal
from emp
where sal>=3000;

select ename, job
from emp
where job != 'SALESMAN';

select e.ename, (select e2.ename from emp e2 where e.mgr = e2.empno ) , e.deptno 
from emp e
where e.sal>=2000;

select ename,sal, job
from emp
where job = 'SALESMAN' and sal>=2000;

select ename, sal
from emp 
where sal >= 1000 and sal <=3000;

select ename, sal
from emp 
where sal BETWEEN 1000 AND 3000;


select ename, sal
from emp 
where empno = 7788 or empno =  7092 or empno =  7369;

select ename, sal
from emp 
where empno in (7788, 7092, 7369);

```



## 패턴 연산자 

### Like 연산자 %, 한글자 _

> 사원테이블에서 이름이 S로 시작하는 사원의 이름을 출력

```mariadb
select ename
from emp
where ename like 'S%';
```



사원테이블에서 이름 중 두번째 글자가 O인 사원의 이름을 출력 

```mariadb
select ename
from emp 
where ename like '_O%';
```



사원테이블에서 이름 중 두번째 글자가 O이고 마지막은 S로 끝나는 이름

```mariadb
select ename
from emp 
where ename like '_O%S';
```



LIKE '추출조건' ESCAPE '와일드대체문'

ex) like '%#_%' ESCAPE '#'; a_abc 02_787_000 98%

ex) like '%@_%' ESCAPE '@';

TEST의 구조를 보자 

DESC TEST;



### DELETE

TEST가 가진 모든 데이터를 삭제하자

DELETE TEST;

SELECT COUNT(*) FROM TEST;



### ALTER TABLE

TEST 컬럼에다가 NAME 컬럼을 추가하자(기존 테이블을 수정함, ADD)

SU NUMBER

MY_DATE DATE

ALTER TABLE TEST

ADD NAME VARCHAR2(10);



테이블 삭제 

DROP TABLE TEST;



주소록 테이블을 생성 

address 테이블의 이름 중 두번째 글자가 '길'인 데이터 전체 출력

주소가 '시'로 끝나는 데이터 출력 

이름이 '최'로 시작하거나 주소가 '시'로 끝나는 데이터 전체 출력

전체 데이터 삭제

```mariadb
CREATE TABLE ADDRESS(
NAME VARCHAR2(10),
ADDR VARCHAR2(10)
);

insert into address values('홍길동', '서울시');
insert into address values('정길동', '부산');
insert into address values('최길동', '인천');

select * from address where name like '_길%';
select * from address where addr like '%시';
select * from address where addr like '%시' or name like '최%';

delete address;

select * from address where tel like '___@_%' ESCAPE '@';
select * from address where tel like '##_##_##' escape '#';
```





### ORDER BY

```mariadb
SELECT 
FROM
WHERE
ORDER BY 컬럼명, 컬럼의 인덱스[ASC | DESC] 
```



TEST 테이블을 이름순으로 정렬 

```mariadb
select *
from test
order by ename;
```



TEST 테이블을 내림차순으로 봉급순으로 정렬 

```mariadb
select *
from test
order by 2 desc;
```



```mariadb
SELECT ROWID FROM TEST;
```



## 집계 함수 

- COUNT, MAX, MIN, SUM, AVG
- [오라클 문서](https://docs.oracle.com/cd/E11882_01/server.112/e41084/functions003.htm#SQLRF20035)
- GROUP BY || HAVING 할 때 사용  

사원 테이블을 이용하여 집계함수를 사용하자 

```mariadb
SELECT COUNT(*), SUM(SAL), MAX(SAL), MIN(SAL),AVG(SAL)
FROM EMP;
```

```mariadb
SELECT COUNT(COMM) FROM EMP;
SELECT COMM FROM EMP; <-- NULL값은 계산 안됨 
SELECT DISTINCT JOB FROM EMP; <-- 중복 데이터 제거
```



## GROUP BY

```mariadb
SELECT  ----- 5 
FROM ----- 1 
WHERE ----- 2 [조건]
GROUP BY ----- 3
HAVING ----- 4[조건]
ORDER BY ----- 6
```

> Having 절 
>
> - where 절은 집계 이전에 having절은 집계 이후에 필터링 작업을 수행
> - having절을 사용하여 그룹을 제한
> - having절은 GROUP BY 뒤에 사용하는 것을 권장



사원테이블에서 부서별 봉급의 합계를 구하자 

```mariadb
SELECT DEPTNO, SUM(SAL)
FROM EMP
GROUP BY DEPTNO;
```



부서별 평균 월급을 구하라 

```mariadb
SELECT DEPTNO, AVG(SAL)
FROM EMP
GROUP BY DEPTNO;
```



직업별 평균 월급

```mariadb
SELECT JOB, AVG(SAL)
FROM EMP
GROUP BY JOB;
```



10번 부서의 평균 월급 

```mariadb
SELECT DEPTNO, AVG(SAL)
FROM EMP
WHERE DEPTNO = 10
GROUP BY DEPTNO;
```



직위가 'SALESMAN'인 사람들 중 최대 월급 

```mariadb
SELECT JOB, MAX(SAL)
FROM EMP
WHERE JOB = 'SALESMAN'
GROUP BY JOB;
```



커미션의 합계 

```mariadb
SELECT SUM(COMM)
FROM EMP;
```



커미션의 평균(NULL값을 꼭 처리해줘야 함 )

```mariadb
SELECT AVG(NVL(COMM,0))
FROM EMP;
```



ROLLUP(전체 합)

```mariadb
SELECT DEPTNO, SUM(SAL)
FROM EMP
GROUP BY ROLLUP(DEPTNO);
```

CUBE(전체합을 위로 올려서 보여줌)

```mariadb
SELECT DEPTNO, SUM(SAL)
FROM EMP
GROUP BY CUBE(DEPTNO);
```



```mariadb
CREATE TABLE STUDENT(
	NAME VARCHAR2(10),
  SUBJECT VARCHAR2(20),
  DEGREE NUMBER(4),
  GRADE NUMBER(4)
);
```



```mariadb
INSERT INTO STUDENT VALUES('영희','국어',1,10);
INSERT INTO STUDENT VALUES('영희','국어',2,20);
INSERT INTO STUDENT VALUES('영희','영어',1,30);
INSERT INTO STUDENT VALUES('영희','영어',2,40);
INSERT INTO STUDENT VALUES('철수','국어',1,50);
INSERT INTO STUDENT VALUES('철수','국어',2,60);
INSERT INTO STUDENT VALUES('철수','영어',1,70);
INSERT INTO STUDENT VALUES('철수','영어',2,80);
```



```mariadb
SELECT NAME, SUBJECT, AVG(GRADE)
FROM STUDENT
GROUP BY CUBE(NAME, SUBJECT)
ORDER BY NAME, SUBJECT;
```

```mariadb
SELECT NAME, SUBJECT, AVG(GRADE)
FROM STUDENT
GROUP BY ROLLUP(NAME, SUBJECT)
ORDER BY NAME, SUBJECT;
```

```mariadb

-- 사원 테이블에서 사원이름과 월급을 출력하되 월급이 내림차순으로 출력하라 
select ename, sal 
from emp
order by sal desc;

-- 직업별 평균 월급을 구하되 컬럼 alias를 '평균'으로 하고, 평균 월급이 높은 순으로 정렬하라 
select job, avg(sal) 평균
from emp
group by job
order by 2 desc;

-- 직업별 월급이 5000 이상인 것만 출력하라 
select job, sal
from emp 
where sal > = 5000
group by job,sal;

-- 부서별 총월급을 구하되 30번 부서를 제외하고, 그 총월급이 8000달러 이상인 부서남 나오게 하고 총 월급이 높은 순서로 출력하라 
select DEPTNO, sum(sal) 총월급
from emp
where DEPTNO != 30  
group by DEPTNO
having sum(sal)>=8000
order by 2 desc;

-- 부서별 평균 월급을 구하되 커미션이 책정된 사원만 가져오고, 그 평균 월급이 1000달러 이상인 부서만 나오게 하고 평균 월급이 높은 순으로 출력 
select DEPTNO ,avg(sal) 평균월급
from emp 
where comm is not null 
group by DEPTNO 
having avg(sal) >= 1000
order by 2 desc;

```



jdbc:oracle:thin:@localhost:1521:xe

oracle.jdbc.OracleDriver

oracle.jdbc.driver.OracleDriver