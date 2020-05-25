# Day27

## 학습목표

1. 서브 쿼리를 이용해서 여러개의 엔티티를 사용하여 값의 결과를 단일행, 다중행, 다중 컬럼 형식으로 추출할 수 있다. 

    



```tex
1. SELECT 
2. 함수(그룹함수, 문자, 숫자, 날짜, 형변환)
3. 서브쿼리 
4. SET 명령

5. JOIN
<테이블 생성, 제약규칙, INSERT, DELETE, UPDATE>
<VIEW> -----> 간단 모델링 
6. 분산함수
7. 탐색쿼리 

8. PL/SQL
9. 사용자함수, 프로시저
10. 트리거 _ [INDEX]
```



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

> 사원이 속한 부서의 평균 급여보다 많은 급여를 받는 사원의 이름, 급여, 부서번호, 입사일, 직업을 출력

```mariadb
select e.ename, e.sal, e.deptno, e.hiredate, d.avgsal
from emp e,(select deptno, avg(sal) as avgsal from emp e group by deptno) d
where e.deptno = d.deptno and e.sal > d.avgsal;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf0xv58urnj30y00dgteb.jpg" alt="image-20200522094917949" style="zoom:50%;" /> 



> 직업이 MANAGER인 사원의 이름과 직업, 부서이름, 부서위치를 출력 

```mariadb
select e.ename, e.job, d.dname, d.loc
from (select ename, job, deptno
from emp
where job = 'MANAGER') e, dept d
where e.deptno =  d.deptno;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf0z63vnu8j30va074416.jpg" alt="image-20200522100147702" style="zoom:50%;" /> 



### TOP_N 서브쿼리 

- 컬럼의 데이터를 ROWNUM을 이용해서 정렬과 함께 순위를 매김 

> 사원테이블에서 급여를 받는 상위 3명만 이름, 급여 

```mariadb
select rownum, ename, sal
from (select * from emp order by sal desc)
where rownum < 4;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf0z5xgi8kj30gk0743zx.jpg" alt="image-20200522100547406" style="zoom:50%;" /> 



> 사원테이블에서 급여를 받는 사원중에 이름, 급여를 조회 
>
> 단, 4~7등 순위에 속하는 사원만 출력 

```mariadb
select  r , ename, sal
from (select rownum r , ename, sal
from (select * from emp order by sal desc))
where r between 4 and 7;
```

 <img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf0yvo137cj30gk08a0ud.jpg" alt="image-20200522102423878" style="zoom:50%;" /> 



> rownum 은 반환되는 결과행의 번호입니다. 따라서 1부터 차례대로 나와야 합니다.
>
> 1~9 없이 10 이 나올수는 없는 것입니다. rownum은 결과 행수를 의미합니다. 
> 결과행이 10행부터 시작할수는 없습니다. 무조건 1행부터 차례대로 나오기 때문에 
> rownum < 10 는 가능하나 rownum = 5 나 rownum > 5 는 불가능합니다.
>
> 출처: https://aboutitdev.tistory.com/12 [About IT Dev]



> 사원테이블에서 월급을 가장 적게 받는 사원을 1~3 추려서 출력 
>
> 사원의 이름과 월급을 출력 

```mariadb
select 
from (select fro)
```



### 문제

1. 'SMITH'보다 월급을 많이 받는 사원들의 이름과 월급을 출력하라 

   ```mariadb
   select ename, sal 
   from emp
   where sal > (select sal from emp where ename = 'SMITH');
   ```

   

2. 10번 부서의 사원들과 같은 월급을 받는 사원들의 이름, 월급, 부서번호를 출력하라

   ```mariadb
   select ename, sal, deptno
   from emp 
   where sal in (select sal from emp where deptno=10);
   ```

   

3. 'BLAKE'와 같은 부서에 있는 사원들의 이름과 입사일을 뽑는데 'BLAKE'는 빼고 출력하라

   ```mariadb
   select ename, hiredate 
   from emp
   where deptno in (select deptno from emp where ename ='BLAKE') and ename !='BLAKE' ;
   ```

   

4. 평균급여보다 많은 급여를 받는 사원들의 사원번호, 이름, 월급을 출력하되, 월급이 높은 사람 순으로 출력

   ```mariadb
   select empno, ename, sal
   from emp 
   where sal > (select avg(sal) from emp) 
   order by sal desc;
   ```

   

5. 이름에 'T'를 포함하고 있는 사원들과 같은 부서에서 근무하고 있는 사원의 사원번호와 이름을 출력하라

   ```mariadb
   select empno, ename
   from emp 
   where deptno in (select deptno from emp where ename like '%T%');
   ```

   

6. 30번 부서에 있는 사원들 중에서 가장 많은 월급을 받는 사원보다 많은 월급을 받는 사원들의 이름, 부서번호, 월급을 출력하라 

   (단, ALL 또는 ANY연산자를 사용할 것)

   ```mariadb
   select ename, deptno, sal
   from emp
   where sal > ALL (select max(sal) from emp where deptno = 30);
   ```

   

7. 'DALLAS'에서 근무하고 있는 사원과 같은 부서에서 일하는 사원의 이름, 부서번호, 직업을 출력하라 

   ```mariadb
   select e.ename, e.deptno, e.job
   from emp e
   where e.deptno in (select deptno from dept where LOC = 'DALLAS');
   ```

   

8. SALES부서에서 일하는 사원들의 부서번호, 이름, 직업을 출력하라

   ```mariadb
   select e.ename, e.deptno, e.job
   from emp e, (select * from dept where DNAME = 'SALES') d
   where e.deptno = d.deptno;
   ```

   

9. 'KING'에게 보고하는 모든 사원의 이름과 급여를 출력하라 

   ```mariadb
   select ename, sal 
   from emp 
   where mgr in (select empno from emp where ENAME = 'KING');
   ```

   

10. 자기의 급여가 평균 급여보다 많고, 이름에 'S'가 들어가는 사원과 동일한 부서에서 근무하는 모든 사원의 사원번호, 이름, 급여를 출력하라.

    ```mariadb
    select e.empno, e.ename, e.sal
    from emp e
    where e.sal> (select avg(sal)from emp) and deptno in (select deptno from emp like '%S%'); 
    ```

    

11. 커미션을 받는 사원들과 월급과 부서번호가 같지 않은 사원들의 이름, 월급, 부서번호 출력하라.

    ```mariadb
    select ename, sal, deptno
    from emp
    where (sal, deptno) not in (select sal, deptno from emp where comm is not null);
    ```

    

12. 30번 부서 사원들과 월급과 커미션이 같지 않은 사원들의 이름, 월급, 커미션을 출력하라.

    ```mariadb
    select ename, sal, comm 
    from emp 
    where (sal, comm) not in (select sal, nvl(comm,0) from emp where deptno = '30');
    ```

    

13. 사원번호, 이름, 월급 그리고 월급누적을 출력하라.

    ```mariadb
    select e.empno, e.ename, e.sal, sum(d.sal)
    from emp e, (select empno, sal from emp) d 
    where e.empno >= d.empno
    group by e.empno,  e.ename, e.sal
    order by 4;
    ```

    

14. 7369번 업무와 같고, 급여가 7876번 사원보다 많은 사원의 이름, 직업을 출력하라

    ```mariadb
    select ename, job 
    from emp 
    where job = (select job from emp where empno=7369) and sal > (select sal from emp where empno=7876);
    ```



### SET연산자(복합질의 연산자)

- UNION : 두 질의 중 어느 것 하나에 의해서라도 선택된 모든 구분(distinct)행을 결과로 함

- UNION ALL: 중복행을 포함하여 두 질의 중 어느 것하나에 의해서 선택된 모든 행을 결과로함

- INTERSECT:  두 질의에 공통으로 선택된 모든 구분(distinct)행을 결과로 

- MINUS: 첫째 select문에 선택되고, 둘째 select문에서 선택되지 않은 모든 구분행이 결과 

- SET연산자 특징 

  - UNION ALL을 제외한 당른 연산자를 사용할 경우에는 중복행이 자동으로 제거도미 
  - 첫째 질의의 열 이름이 결과에 표시됨
  - UNION ALL을 제외한 다른 연산자의 출력은 기본적으로 오름차순으로 정렬
  - ORDER BY절에서 사용된 열이름이나 별칭은 첫번째 select목록에 잇어야 함
  - SET연산자는 서브쿼리에서도 사용할 수 잇음
  - select 문장은 왼쪽(위)에서 오른쪽(아래)로 실행
  - 괄로를 사용하는 연산자의 우선순위를 바꿀 수 있음

  

  

#### UNION 

- WHERE 절에서 UNION을 사용하는 질의는 SELECT 목록에 있는 것과 똑같은 수와 데이터 타입의 열을 가져야 함(**칼럼명은 달라도 상관 없음**)



> UNION = EMP + EMP_RES = 중복데이터 제거 후 리턴 

```mariadb
select ename, job, deptno
from emp 
union 
select ename, job, deptno
from emp_res;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf129r8flzj30gk0ri7bc.jpg" alt="image-20200522122144211" style="zoom:50%;" /> 



#### UNION ALL 

- UNION과 달리 중복되지 않은 행은 제거 되지 않으며, 결과는 디폴트로 정렬되지 않음 
- -> DISTINCT 키워드 사용 못함 

```mariadb
select ename, job, deptno
from emp 
union all
select ename, job, deptno
from emp_res;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf12e6gvbnj30gk0xq7d9.jpg" alt="image-20200522122559012" style="zoom:50%;" /> 



#### intersect 집합 연산자 

- 두 쿼리의 공통 행을 리턴하기 위해 사용
- intersect된 테이블의 순서를 바꾸어도 결과는 바뀌지 않음

```mariadb
select ename, job, deptno
from emp 
intersect
select ename, job, deptno
from emp_res;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf12fzxw6jj30gk09876k.jpg" alt="image-20200522122744081" style="zoom:50%;" /> 



#### minus 집합 연산자 

- 두번째 질의에 의해 리턴되는 행을 제외하고 첫번째 질의에 의해서만 리턴되는 행을 조회하기 위해 사용함
- where절에 있는 모든 열은 minus연산자 질의에 대한 select절에 있어야 함

```mariadb
select ename, job, deptno
from emp 
minus
select ename, job, deptno
from emp_res;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf12hhtzovj30gk0h0wiy.jpg" alt="image-20200522122910312" style="zoom:50%;" /> 



## oracle & java

1. java에서 file(txt, xml, csv, sql,,)에서 csv를 직접 생성하고 읽을 수 있음
2. oracle에서 데이터를 csv를 내보낸 것을 java에서 불러올 수 있음
3. csv를 .sql로 변환한 다음 oracle 서버에 테이블로 올리고 싶음
4. java로 오라클을 접속해서 데이터를 SELECT할 수 있음



### JDBC의 주요 패키지 java.sql.*

- 주요 인터페이스와 주요 클래스 
  1. java.lang.Class -> Class.forName("참조자료형"); 
     - 드라이버 클래스를 참조함 
  2. java.sql.DriverManager.getConnection(url, username, password);
     - 참조된 드라이버 클래스를 이용하여 지정된 값으로 접속
  3. java.sql.Connection
     - 연결된 객체를 통해 dml,ddl을 생성하고 commit, rollback, close명령을 가짐
     - createStatement(), createPreparedStatement()등의 주요 메소드로 명령의 객체를 주어진 sql로 실행 후 결과를 sql구문에 따라 리턴하도록 구현
  4. Statement -> exec000()메소드로 sql를 실행함
  5. ResultSet -> sql실행 결과를 가진 테이블 객체 메모리로 내용을 호출함 

