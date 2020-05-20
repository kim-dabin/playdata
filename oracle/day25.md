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