# Day25

## 학습목표

1. 집계함수, 문자열 함수, 날짜 함수를 사용하여 데이터를 관리할 수 있다.
2. 데이터를 TO_oooo 함수를 이용해 형식을 전환할 수있다. 
3. 서브 쿼리를 이용해서 여러개의 엔티티를 사용하여 값의 결과를 단일행, 다중행, 다중 컬럼 형식으로 추출할 수 있다. 



## GROUP BY에 ROLLUP[총계, 누계 집계] 및 CUBE 연산자 사용

- 상호 참조열에 따라 상위 집계행을 산출한다. 
- ROLLUP 및 CUBE 연산자를 질의의 GROUP BY 절에 지정할 수 있다. 
- ROLLUP 그룹화는 정규 그룹화 행과 하위 총계 값을 퐇마하는 결과 집합을 산출한다. 
- CUBE 연산자를 GROUP BY 절에 상용하면 지정된 표현식에서 가능한 모든 조합 값에 따라 선택된 행이 그룹화 되고 각 그룹에 대한 요약 정보를 나타내는 행이 반환된다. 

### ROLLUP[총계, 누계 집계]

- 보고서 작성시에 사용되며 집합에서 통계 요약 정보를 나타낼때 사용 
- ROLLUP을 GROUP BY에 사용하게 되면 지정된 열 목록에 따라 오른쪽에서 왼쪽 방향으로 하나씩 그룹을 만들어 그룹함수를 생성한 다음 그룹을 적용함
- 만일 ROLLUP을사용하지 않을 경우는 N차원의 하위총계를 추출할 경우 N+1개의 SELECT문을 UNIONALL로 구현함 -> 이 경우 각 SELECT문을 엑세스하게 되어 다중 탐색한 결과로 컴파일의 속도가 느려짐
- 하위 총계를 산출하는데 필요한 열(컬럼)이 많을 경우 사용됨 



#### 예제

1. 부서별 인원수, 급여의 합 조회시 ROLLUP을 사용해 총 집계를 조회하자

   1. ROLLUP(deptno) 적용한 경우 

      ```mariadb
      select deptno, count(*), sum(sal) 
      from emp 
      group by rollup(deptno);
      ```

   2. ROLLUP(deptno, job) 적용한 경우

      ```mariadb
      select deptno, job, sum(sal) 
      from emp 
      group by rollup(deptno,job);
      ```

      <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geym4bgc20j30ko0qeagi.jpg" alt="image-20200520093147477" style="zoom:33%;" /> 

   3. ROLLUP(job,deptno ) 적용한 경우

      ```mariadb
      select deptno, job, sum(sal) 
      from emp 
      group by rollup(job, deptno);
      ```

      <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geym4tus1pj30ko0s4wlv.jpg" alt="image-20200520093216136" style="zoom:33%;" /> 

   4. ROLLUP(deptno, job, mgr ) 적용한 경우

      ```mariadb
      select deptno, job, mgr ,sum(sal) 
      from emp 
      group by rollup(deptno,job, mgr);
      ```

      <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geym3otemaj30q016o4b8.jpg" alt="image-20200520093111285" style="zoom:33%;" /> 



### CUBE

- CUBE 연산자는 AVG, SUM, MAX, MIN, COUNT 를 포함한 모든 그룹함수에 적용될 수 있음
- 일반적으로 CROSS TABULATION(십자가 도표) 결과 집합을 산출하는데 사용됨 
- ROLLUP은 가능한 하위 총계 조합 중 일부만 산출하지만, CUBE는 group by 절에 지정된 모든 그룹의 조합에 대해 하위 총계와 최상의 총계를 산출
- CUBE연산자를 집계함수에 사용하면 결과 집합에 추가 행이 만들어짐
- group by 절에 N개의 열이 있을 경우 가능한 상위 집계 조합수는 2의 N승 개이다. 



#### 예제

1. CUBE를 적용하여 도표를 만들어 보자 

   - CUBE(deptno, job) 적용한 경우 

     ```mariadb
     select deptno, job, sum(sal)
     from emp 
     group by CUBE(deptno, job);
     ```

   - CUBE(job, deptno) 적용한 경우 

     ```mariadb
     select deptno, job, sum(sal)
     from emp 
     group by CUBE(job, deptno);
     ```

   - CUBE(deptno, job, mgr) 적용한 경우 

     ```mariadb
     select deptno, job, mgr ,sum(sal)
     from emp 
     group by CUBE(deptno, job, mgr );
     ```



### GROUPING 함수

- GROUPING 함수는 하나의 열을 인수로 사용함 
- GROUPING 함수에 있는 인수는 GROUP BY절에 잇는 표현식 중 하나와 반드시 일치해야 함
- GROUPING 함수는 0또는 1을 반환
- GROUPING 함수의 반환된 0의 값은 해당 열을 사용하여 집계 값을 계산 했거나 해당열에 나오는 NULL이 저장된 NULL임을 의미함 
- GROUPING 함수의 반환된 1의 값은 해당 열을 사용하지 않고 집계값을 계산했거나 해당 열이 나오는 NULL값이 그룹화의 결과로 ROLLUP이나 CUBE에 의해 만들어진 의미임



#### 예제 : 

1. ROLLUP(deptno, job)으로 그룹

   ```mariadb
   select deptno, job, sum(sal), GROUPING(deptno), GROUPING(job)
   from emp 
   GROUP BY ROLLUP(deptno, job);
   ```

   <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geymhcidzgj31000pw7de.jpg" alt="image-20200520094418758" style="zoom:33%;" /> 

2. CUBE(deptno, job)으로 그룹

   ```mariadb
   select deptno, job, sum(sal), GROUPING(deptno), GROUPING(job)
   from emp 
   GROUP BY CUBE(deptno, job);
   ```

   <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geymj4643fj30xe0u0n8l.jpg" alt="image-20200520094600018" style="zoom:33%;" /> 



### GROUPING SETS 

- GROUP BY 절의 확장기능으로 데이터를 여러개로 그룹화 하도록 지정할 수 있음
- 집계를 효율적으로 수행할 수 있으며 여러 차원에 걸쳐 데이터를 분석하기 쉬워짐
- UNION ALL 연산자를 사용해 여러개의 SELECT문을 결합하는 대신 GROUPING SETS을 사용하여 하나의 SELECT문으로 작성함으로써 다양한 그룹화를 지정할 수 있음



#### 예제

1. ```mariadb
   select deptno, job, mgr, AVG(sal)
   from emp
   GROUP BY GROUPING SETS((deptno, job, mgr), (deptno, mgr), (job,mgr));
   ```

   <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geymq412dcj30q41bih06.jpg" alt="image-20200520095244494" style="zoom:33%;" /> 



### UNION ALL

```mariadb
select deptno
from emp
union all
select deptno
from dept;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geymttfpf4j308m0wi41w.jpg" alt="image-20200520095618136" style="zoom:33%;" /> 



```mariadb
select deptno, job
from emp
union all
select deptno, loc
from dept;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geymu8feqvj30iu0wi0zr.jpg" alt="image-20200520095641880" style="zoom:33%;" /> 

```mariadb
select deptno, job, mgr, AVG(sal)
from emp 
GROUP BY deptno, job, mgr;
UNION ALL
select deptno, NULL, mgr, AVG(sal)
from emp 
GROUP BY deptno, mgr
UNION ALL
select NULL, job, mgr, AVG(sal)
from emp 
GROUP BY job, mgr;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyodfos0jj30q40hw0yr.jpg" alt="image-20200520104944793" style="zoom:33%;" /> 

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geynvmzw6ij30q40ui7cz.jpg" alt="image-20200520103239417" style="zoom:33%;" /> 

ex) ROLLUP(a,b,c) -> GROUPING SETS((a,b,c), (a,b), (a),());

​	CUBE(a,b,c) -> GROUPING SETS((a,b,c), (a,b), (a), (b,c), (a),(b),(c),());

```mariadb
select deptno, job, mgr, sum(sal)
from emp
group by rollup(deptno, (job,mgr));

```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyn23rlefj30qa0r8ai9.jpg" alt="image-20200520100415561" style="zoom:33%;" />  

	1. group by deptno, job, mgr
 	2. group by deptno
 	3. group by null



#### 예제 GROUP BY GROUPING SETS (a,b,c)

1. GROUP BY GROUPING SETS (a,b,c)
   1. GROUP BY a UNION ALL
   2. GROUP BY b UNION ALL
   3. GROUP BY c 

```mariadb
select deptno, job, mgr, AVG(sal)
from emp
GROUP BY GROUPING SETS((deptno, job, mgr), (deptno, mgr), (job,mgr));
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geynfcn04cj30py1botna.jpg" alt="image-20200520101659505" style="zoom:33%;" /> 

```mariadb
select deptno, job, mgr, AVG(sal)
from emp
GROUP BY GROUPING SETS((deptno, job, mgr), (deptno, mgr), ());
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geynguz2tmj30pu0ygk1p.jpg" alt="image-20200520101826226" style="zoom:33%;" /> 

2. GROUP BY GROUPING SETS (a,b,(b,c))
   1. GROUP BY a UNION ALL
   2. GROUP BY b UNION ALL
   3. GROUP BY b,c 
3. GROUP BY GROUPING SETS ((a,b,c))
   1. GROUP BY a,b,c 
4. GROUP BY GROUPING SETS (a,(b),())
   1. GROUP BY a UNION ALL
   2. GROUP BY b UNION ALL
   3. GROUP BY ()



## 단일 행 함수 

- 각 행을 각각 일정한 형태로 변환하여 결과를 돌려주는 함수 
- 함수의 인자 값으로는 상수, 컬럼값, 변수, 표현식을 사용할 수 있음
- 단일 행 함수의 인수(사용자 지정 상수, 변수 값, 컬럼(열) 이름, 표현식)
- 적용되는 데이터 타입에 따라 단일 행 함수의 종류를 구분 
  - 문자함수: 문자를 입력하면 문자 또는 숫자 값을 반환
    - 입력 값 타입 : CHARACTER
    - 리턴값 타입 : CHARACTER/NUMBER
  - 숫자함수: 숫자를 입력하면 숫자 값을 반환
  - 날짜함수: 날짜 유형의 값을 연산
  - 변환함수: 데이터 유열을 변환
  - 기타함수: 타입에 상관없이 적용

### 문자열 함수

- 문자를 입력하면 문자 또는 숫자 값을 반환
- 입력 값 타입 : CHARACTER
- 리턴값 타입 : CHARACTER/NUMBER

#### LENGTH

- 주어진 컬럼 값/문자열 길이(문자 개수)를 반환하는 함수 

##### 예제

```mariadb
select ename, length(ename) from emp;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyudupobhj30hk0ro0xr.jpg" alt="image-20200520141742538" style="zoom:33%;" /> 

```mariadb
SELECT SYSDATE FROM DUAL;
SELECT LENGTH('ABC'), LENGTH('가나다') FROM DUAL;
```

```mariadb
CREATE TABLE TEST02(NAMEC CHAR(20), NAMEV VARCHAR2(20));
INSERT INTO TEST02 VALUES('ENCORE', 'ENCORE');
INSERT INTO TEST02 VALUES('엔코아', '엔코아');
SELECT NAMEC, LENGTH(NAMEC), NAMEV,LENGTH(NAMEV) FROM TEST02;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyuq3a7f6j30ys05c0ul.jpg" alt="image-20200520142931220" style="zoom:33%;" /> 

- 1글자 3byte 엔코아*3 = 9byte = 데이터 길이 
- 컬럼 사이즈(20) - 데이터 길이(9) = 11byte(여유공간)
- 데이터 글자수(3) + 여유공간 글자(11) = 14

```mariadb
INSERT INTO TEST02 VALUES('빅데이터', '빅데이터');
SELECT NAMEC, LENGTH(NAMEC), NAMEV,LENGTH(NAMEV) FROM TEST02;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyuso3eexj30ys06mq5b.jpg" alt="image-20200520143159583" style="zoom:33%;" /> 



```mariadb
SELECT NAMEC, LENGTHB(NAMEC), NAMEV,LENGTHB(NAMEV) FROM TEST02;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyuulre46j30zu06mq5f.jpg" alt="image-20200520143351226" style="zoom:33%;" /> 



#### INSTR

- 내가 원하는 컬럼의 값에 내가 원하는 구문이 포함되어 있는 여부를 확인할 때 사용
- ('sg_ahn@vcc.com','c',-1,2) 
  - -1은 문장 뒤에서 부터 시작한다는 뜻
  - 2는 'c'가 2번 중복된 위치를 확인 

```mariadb
SELECT INSTR('sg_ahn@vcc.com','c',-1,2) 위치 FROM DUAL; 
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyv14qqyvj305g04674h.jpg" alt="image-20200520144008003" style="zoom:33%;" /> 

```mariadb
SELECT ename, job, INSTR(job,'A',-1,2) 위치 FROM emp; 
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyv28uxgmj30fy0r8dmf.jpg" alt="image-20200520144111588" style="zoom:33%;" /> 



```mariadb
SELECT INSTR('sg_ahn@vcc.com','c',INSTR('sg_ahn@vcc.com','.')-1) 위치 FROM DUAL; 
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyv711sefj305g04674h.jpg" alt="image-20200520144541597" style="zoom:33%;" /> 



#### LPAD, RPAD

- LPAD : 왼쪽을 채움
- RPAD : 오른쪽을 채움



##### 예제

```mariadb
SELECT ENAME, LPAD(ENAME,30,'%') FROM EMP;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyvaqjd9xj30fs0xwah7.jpg" alt="image-20200520144921512" style="zoom:33%;" /> 



#### LTRIM/RTRIM



##### LTRIM 예제

```mariadb
SELECT LTRIM('000123',0) FROM DUAL;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyvdn9rc1j3028044aa5.jpg" alt="image-20200520145209532" style="zoom:50%;" /> 

```mariadb
SELECT LTRIM('123123Tech123','123') FROM DUAL;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyvfxlof1j304404cglw.jpg" alt="image-20200520145421068" style="zoom:50%;" /> 

```mariadb
SELECT LTRIM('xzzyzyzyTech','xyz') FROM DUAL;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyvg54kf7j302o04g3yn.jpg" alt="image-20200520145432896" style="zoom:50%;" /> 

```mariadb
SELECT LTRIM('6 372Tech', '0 123456789') FROM DUAL;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyvwb25ooj302i04iwem.jpg" alt="image-20200520151005337" style="zoom:50%;" /> 

```mariadb
SELECT LTRIM('6 T 372Te ch', '0 123456789 ') FROM DUAL;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyvxi5ndfj305g04idg8.jpg" alt="image-20200520151114411" style="zoom:50%;" /> 

##### RTRIM 예제

```mariadb
SELECT RTRIM('123Tech123', '123') FROM DUAL;
SELECT RTRIM('6 372Tech', '0 123456789') FROM DUAL;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyvy3qjaxj305g04i74p.jpg" alt="image-20200520151148766" style="zoom:50%;" /> 

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyvycjaauj305g04imxj.jpg" alt="image-20200520151203615" style="zoom:50%;" /> 

#### TRIM

```mariadb
SELECT TRIM('  Tech  ') FROM DUAL;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyvm1pmrtj302o04gmxb.jpg" alt="image-20200520150013876" style="zoom:50%;" /> 

```mariadb
SELECT TRIM('a' FROM 'aatechaaa') FROM DUAL;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyvnsttbsj302o04gwen.jpg" alt="image-20200520150155222" style="zoom:50%;" /> 



```mariadb
SELECT TRIM(BOTH '1' FROM '123Tech111') FROM DUAL;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyvnfqp94j303e04gglv.jpg" alt="image-20200520150134125" style="zoom:50%;" /> 



#### SUBSTR

- 주어진 컬럼/문자열에서 지정한 위치부터 지정한 개수만큼이 문자열을 잘라내어 반환하는 함수 

```mariadb
SELECT SUBSTR('This is a test', 6, 2) FROM DUAL; 
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyvssk3w1j302804gdfx.jpg" alt="image-20200520150642839" style="zoom:50%;" /> 

```mariadb
SELECT SUBSTR('This is a test', -3, 3) FROM DUAL; 
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyvsirvt0j302804gdfy.jpg" alt="image-20200520150627702" style="zoom:50%;" /> 

```mariadb
SELECT SUBSTR('이것은 연습입니다', 3, 4) FROM DUAL; 
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyvs4v6f7j305g04g74o.jpg" alt="image-20200520150604966" style="zoom:50%;" /> 





### 예제

1. 10번 부서의 사원에 대해 담당 업무 중 좌측에 'A'를 삭제하고 급여중 좌측의 '1'을 삭제해 출력

```mariadb
select ltrim(JOB,'A'), ltrim(sal,1)
from emp 
where DEPTNO = 10;
```

2. replace 함수를 사용해 20번 부서의 사원 이름에 JO문자열을 *?로 변경해서 조회

```mariadb
select ename, replace(ename,'JO','*?') 변경결과
from emp
where deptno=20;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geywovvkv6j30ai09m3zx.jpg" alt="image-20200520153733409" style="zoom:33%;" /> 

3. translate 함수를 사용해 20번 부서의 사원 이름에 JO문자열을 *?로 변경해서 조회

```mariadb
select ename, translate(ename,'JO','*?') 변경결과
from emp
where deptno=20;

select ename, translate(ename,'SMI','*?!') 변경결과 from emp;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geywp47aedj30dm09mmyo.jpg" alt="image-20200520153747009" style="zoom:33%;" /> 



4. emp 테이블에서 이름의 첫글자가 'K'보고 크고 'Y'보다 작은 사원의 사원번호, 이름, 업무, 급여, 부서번호를 조회하고 이름순으로 정렬

   ```mariadb
   select empno,ename,job,sal,deptno  
   from emp 
   where substr(ename,1,1)>'K' and substr(ename,1,1)<'Y'
   order by ename;
   ```

   <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geywxsnovgj30r40dwq7q.jpg" alt="image-20200520154607280" style="zoom:33%;" /> 

5. 사원 테이블에서 사원의 이름, 직업을 출력 하자. 단 이름은 첫글자만 대문자로, 직업은 전체 소문자로 출력해보자

   ```mariadb
   select ename, initcap(ename), lower(ename), upper(ename)
   from emp;
   ```

   <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geywznm0yxj30m60qydp9.jpg" alt="image-20200520154754329" style="zoom:33%;" /> 

   ```mariadb
   select ename, substr(ename,1,1)||lower(substr(ename,2)), lower(ename), upper(ename)
   from emp;
   ```

   <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyx1aafjrj31160qy4a3.jpg" alt="image-20200520154928298" style="zoom:33%;" /> 

6. contcat(str,str2) = str||str2

   ```mariadb
   select concat(ename,empno) from emp;
   ```

   <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyx3j575oj30ca0qyq8g.jpg" alt="image-20200520155138067" style="zoom:33%;" /> 

7. 6번의 결과를 TEST03 테이블로 생성(결과라는 alias를 줘야함)

   ```mariadb
   create table test03
   as select concat(ename,empno) 결과 from emp;
   ```

   <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyx7ahgx5j30dk0tited.jpg" alt="image-20200520155514540" style="zoom:33%;" /> 

   ```mariadb
   create table test04
   as select concat(ename,empno) 결과, substr(ename,1,1)||lower(substr(ename,2)) 결과02  from emp;
   ```

   

### 숫자함수

#### ROUND

- 지정한 자리수에서 반올림 하는 함수 

```mariadb
select round(123.315, -2) from dual;
select round(123.315, -3) from dual;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyxc57z4cj30l20fctc5.jpg" alt="image-20200520155954476" style="zoom:33%;" /> 

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyxev0v1bj30kq0u4agz.jpg" alt="image-20200520160231302" style="zoom:33%;" /> 



#### TRUNC

- 버림

```mariadb
select trunc(4567.673) 결과1, trunc(4567.673,0) 결과2, trunc(4567.673,2) 결과3,
trunc(4567.673, -2) 결과4
from dual;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyximnlvij30mk04q0tt.jpg" alt="image-20200520160608959" style="zoom:33%;" /> 



#### MOD

- 나머지

```mariadb
select sal, mod(sal,30)
from emp
where deptno = 10;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyxjxz9ipj30c407gwfl.jpg" alt="image-20200520160724142" style="zoom:33%;" /> 



#### SYSDATE

- 날짜 함수 
- 지정된 형식으로 현재 날짜와 시간을 표시함

```mariadb
select sysdate from dual;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyxljj1ktj304k04kmxj.jpg" alt="image-20200520160856661" style="zoom:50%;" /> 



### 날짜 함수 

- DATE TYPE : 세기, 년, 월, 일, 시, 분, 초 
- 2020년도 05월 20일 오후 4시 10분 21초 
  - 20, 20/ 05/ 20 04:10:21 --> 이렇게 잘라서 관리함 
  - DD-MON-RR
- 연산 가능함
- 날짜에 숫자를 더하거나 빼는 값을 계산
- 날짜 - 날짜 = 일수를 계산 할 수 있음
- DATE + NUMBER = 날짜 + 일수 = DATE 형태로 리턴
- DATE - NUMBER = 날짜 - 일수 = DATE 형태로 리턴
- DATE1 - DATE2 = 일수 = NUMBER 로 리턴
- DATE + NUMBER/24 = 날짜에 시간계산을 더해서 리턴, DATE 형태로 리턴



#### ADD_MONTHS 

- 지정한 만큼의 달 수를 더한 날짜를 반환하는 함수
- DATE 형태로 리턴



#### MONTHS_BETWEEN

- 지정한 두 날짜 사이의 월수를 반환하는 함수 
- NUMBER 형태로 리턴 



#### 현재 날짜에 관한 값을 리턴하는 함수 

- SYSDATE: 서버의 현재 날짜와 시간

- SYSTIMESTAMP: 서버의 현재 날짜와 시간 TIMEZONE 값을 내장하고 리턴(SYSDATE를 포함) 

- CURRENT_DATE: 세션 시간대의 현재 날짜를 리턴 

- CURRENT_TIMESTAMP: 세션 시간대의 TIMEZONE 값을 내장하고 리턴(CURRENT_DATE를 포함)

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyy6m7cegj30km0u6104.jpg" alt="image-20200520162911389" style="zoom:33%;" /> 

> 세션이란? 
>
> 클라이언트가 서버에 접속을 하게되면 서버는 클라이언트의 정보를 담는 객체를 생성해서 서버에서 관리를 하게 됨 
>
> 이때 서버가 관리하는 객체는 세션 / 클라이언트에 저장되는 객체는 쿠키



- EXTRACT(datetime): () 안에 데이터의 필드를 추출함 

  <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyyem2dq7j30t60nqacm.jpg" alt="image-20200520163652560" style="zoom:33%;" />

  - ```mariadb
    SELECT EXTRACT(year from sysdate) 
    FROM DUAL;
    ```

    <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyybb2ywvj30co04s3z9.jpg" alt="image-20200520163342367" style="zoom:33%;" /> 

  - ```mariadb
    SELECT ename, EXTRACT(year from hiredate) 
    FROM emp;
    ```

    <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1geyychxu7cj30ig0qyq97.jpg" alt="image-20200520163449507" style="zoom:33%;" /> 

