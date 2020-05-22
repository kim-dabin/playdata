# Day27

## 학습목표



## 서브쿼리 

### 다중행(Multiple-Row) 서브 쿼리 

- 하나 이상의 행을 return 하는 서브쿼리를 다중 행 서브퀴리라고 함
- 복수행 연산자(IN,ANY,ALL)를 사용함
  -  in : 목록에 잇는 임의의 값과 동일하면 true;
  - any: 서브쿼리에서 반환된 가각의 값과 비교하여 하나라도 true이면 true(=ANY는 IN과 동일)
  - all: 서브쿼리에서 반환된 모든 값과 비교하여 모두 true이어야 true
- not 연산자는 in, any, all 연산자와 함께 사용될 수 있음

### 다중열(Multiple-Column)

- 서브쿼리의 결과 값이 두개 이상의 컬럼을 리턴하는 쿼리를 말함
- 두개 이상의 열을 비교하려면, 논리연산자를 사용하여 혼합 where 절을 작성해야함 
- 다중 열 서브쿼리를 사요하면 중복된 where조건을 하나의 where절로 결합 가능 



> 30번 부서번호의 사원의 커미션과 같은 부서의 같은 월급을 받는 사원의 정보를 출력 
>
> Non-Pairwise 서브쿼리 

````mariadb
select empno, sal, deptno
from emp
where sal in(select sal from emp where deptno = 30 and comm is not null);
````

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf0x8262pvj30gu08ctag.jpg" alt="image-20200522092706428" style="zoom:50%;" /> 

> 30번 부서번호의 사원의 커미션과 같은 부서의 같은 월급을 받는 사원의 정보를 출력 
>
> Pairwise 서브쿼리 

```mariadb
select empno, sal, deptno
from emp
where (sal, deptno) in(select sal, deptno from emp where deptno = 30 and comm is not null);
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf0x8fwojhj30gu08cjt4.jpg" alt="image-20200522092729154" style="zoom:50%;" /> 



### 상호연관(Correlated) 서브 쿼리

- 메인(바깥쪽) 쿼리의 한 ROW에 대해서 서브쿼리가 한번씩 실행
- 테이블에서 행을 먼저 읽어서 각 행의 값을 관련된 데이터와 비교하는 방법
- 기본 질의에서 고려된 각 후보 행에 대해 서브 쿼리ㅣ가 다른 결과를 반환하는 경우에 사용
- 각 행의 값에 따라 응답이 달라지는 다중 질의에 응답할 때 상호연고나 서브쿼리를 사용
- 서브쿼리에서 메인 쿼리의 컬럼명을 사용할 수 있으나, 메인 쿼리에서는 서브쿼리의 컬럼명을 사용할 수 없음
- 상호 연관 서브쿼리 실행
  1. 후보 행을 가져옴(메인 쿼리에서 인출)
  2. 후보 행의 값을 사용하여 서브쿼리를 실행
  3. 서브쿼리의 결과 값을 사용하여 후보 행의 조건을 확인
  4. 후보 행이 남지 않을 때까지 반복 

```mariadb
select column1,,, 
from table1 main ---- 별칭이 꼭 있어야함
where column1 OPERRATOR(select column1 form table2 where expr1 = main.expr2);
```



> 사원이 속한 부서의 평균 급여보다 많은 급여를 받는 사원의 이름, 급여, 부서번호, 입사일, 직업을 출력 

```mariadb

select e.ename, e.sal, e.deptno, e.hiredate, e.job
from emp e , dept d
where e.sal > (select avg(sal) from emp where e.deptno = deptno) AND e.deptno = d.deptno
order by 2 desc;


select e.ename, e.sal, e.deptno, d.DNAME , e.hiredate, e.job
from emp e , dept d
where e.sal > (select avg(sal) from emp where e.deptno = deptno) AND e.deptno = d.deptno
order by 2 desc;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf0xmxdnj5j30y00dgdlh.jpg" alt="image-20200522094124122" style="zoom:50%;" /> 



### FROM절상의 서브쿼리(INLINE VIEW)

- FROM절의 SELECT 구문을 말함
- 하나의 테이블의 자료 양이 많을 경우 FROM절에 테이블 전체를 기술하여 사용합몀 효율이 떨어질 수 있음
- 이런 경우 필요한 행과 열만 선택하여 FROM절에 기술하면 오라클 서버가 최적화 단계에서 효율적인 검색을 함 
- 해당 SELECT문에서만 데이터 소스를 정의 
- FROM절에 있는 서브쿼리는 인라인(inline)뷰 라고도 함 

```mariadb
SELECT E.name, D.mymgr
from emp E, (select mgr as mymgr from emp) D;
```

