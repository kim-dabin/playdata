# Day28

## 학습목표

1. JDBC connection을 살펴벼고 각 흐름을 숙지할 수 있음
2. Join을 사용하여 Entity의 조건 병합에 대해서 살펴보고 활용할 수 있음
3. 분산함수를 살펴보고 활용할 수 있음





## JDBC connection

### 순서

1. Class.forName() -> 드라이버 연결
2. Connection conn = DriverManager.getConnection() -> db연결
3. Statement stmt = conn.createStatement() -> 명령 준비
4. 쿼리를 실행해서 결과를 리턴
   - 리턴 타입 : [boolean, int[], ResultSet, int]
   - boolean  stmt.execute(String sql) 
     - 지정된 쿼리의 실행 유무를 리턴
   - int[] stmt.executeBatch()  
     - 추가된 쿼리들을 일괄[insert,delete, update]로 실행한 결과를 리턴 
   - ResultSet stmt.executeQuery(String sql) 
     - 지정된 쿼리 중 SELECT 쿼리를 테이블의 데이터로 저장해서 리턴 
   - int stmt.executeUpdate(String sql)
     - [insert,delete, update] 실행결과 
5. SELECT 쿼리를 ResultSet의 getDataType형 메소드로 결과를 컬럼형으로 리턴 받음



### JDBC + MVC 

- Model : 연산, 디비처리등의 비지니스 로직 (biz+dao)

- View : 화면 설계단 (회원가입화면, 게시판 화면)

- Controller : view에서 입력받은 값들을 조건에 따라 모델로 전달한 후 결과를 다시 view로 리턴하는 기능[데이터 검증 후 로직을 제어]

  > 로그인 MVC 과정

![image-20200525101415281](https://tva1.sinaimg.cn/large/007S8ZIlgy1gf4fg3ziw9j321o0rs1ii.jpg)



#### Day28 : my_emp 테이블을 사용한 MVC 프로젝트

1. Model
   1. com.biz.MyEmpBIZ 
      1. 계산 있음 -> 계산후 DAO 호출 
      2. 계산 없음 -> DAO 호출 
   2. com.dao.MyEmpDAO
2. View + Controller : MTest.main()
3. VO : MVC간에 데이터를 전달할 객체 
   - com.vo.MyEmp
4. Entity : com.entity.MyEmpEntity



## Join

### 정의 

- 데이터베이스에서 여러 테이블의 데이터가 필요한 경우 조인 사용
- 조인은 관계형 데이터베이스에서 가장 기본적이고 가장 중요한 기능
- 어떤 테이블을 기준으로 다른 테이블에 있는 Row를 찾아오는 것
- 관계형 데이터베이스에서는 서로 독립적인 데이터들간의 조인을 이용하여 필요시 원하는 다양한 정보를 참조 
- 행당 열에 존재하는 공통 값, 일반적으로 기본키 및 외래키 열을 조인 조건으로 사용하여 한 테이블의 행을 다른 테이블의 행에 조인 가능 

### 테이블간의 관계에 따른 분류

1. inner join(기본)
2. cross join
3. outer join
4. self join
5. nonequl join



#### INNER JOIN

- 각 테이블의 조인 컬럼(공통 컬럼)을 비교하여 조인 조건을 만족하는 레코드만 선택하는 조인

  > 테이블 M,S 에서 M1=S1 조건을 만족하는 레코드만 가져오는 join문을 작성

  ```mariadb
  -- Ansi join
  select * from m inner join s on m1=s1;
  -- sql server join
  select * from m,s where m1=s1;
  ```

> inner join을 이용하여 사원의 이름과 그 사원이 속해있는 부서이름을 출력해보라 

```mariadb
select ename, dname
from emp, dept
where emp.deptno = dept.deptno;

-- Ansi join
select ename, dname
from emp join dept
using(deptno);
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf4mompvf9j30h40s0tfb.jpg" alt="image-20200525142439982" style="zoom:50%;" /> 

> 사원의 이름과 그 사원이 속해있는 부서 이름과 부서번호를 출력하라 

```mariadb
select ename, dname, deptno
from emp, dept
where emp.deptno = dept.deptno

-- Ansi 
select ename, dname, deptno
from emp join dept using(deptno);
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf4mqef3hmj30us0s011g.jpg" alt="image-20200525142624593" style="zoom:50%;" /> 



#### cross join

- 각 테이블의 모든 로우에 대해서 가능한 모든 조합을 가지는 쿼리 결과를 만들어 내는 조인

```mariadb
-- ansi 
select * from m cross join s;

-- sql
select *
from m,s;
```



#### outer join

- 두 테이블의 주,종 관계를 두고 주 테이블의 모든 레코드와 종 테이블에서 조인 조건을 만족하는 레코드만 가져올 때 사용
- 주 테이블의 위치에 따라서 left outer join , right outer join, 그리고 두 개의 결과를 합한 full outer join이  있다 

#### left outer join

- m 테이블을 주 테이블로 놓고, s테이블을 종테이블로 하여 조인을 걸어서 m1=s1 조건을 만족하는 레코드를 가져오는 left outer join을 작성

```mariadb
select *
from m left outer join s
on m1=s1;

-- sql
select *
from m,s
where m1=s1(+);

```



#### right outer join

- S테이블을 주테이블로 놓고, m테이블을 종테이블로 한 right outer join을 작성

```mariadb
-- ansi
select *
from m right outer join s
on m1=s1;

-- sql
select *
from m,s
where m1(+)=s1;
```



#### full outer join

- 두 테이블을 left outer join, right outer join을 한 결과를 합한 것과 동일한 결과를 가져옴

```mariadb
-- ansi
select * from m full outer join s 
on m1=s1;
```



#### left, right, outer join을 사용한 쿼리를 union으로 합했을 경우 

```mariadb
select *
from m,s
where m1(+)=s1
union
select *
from m,s
where m1=s1(+);
```



#### 세 개 이상의 테이블에서 조인 걸기 

```mariadb
-- ansi
select *
from m inner join s 
on m1=s1
inner join x 
on s1=x1;

-- sql
select *
from m,s,x
where m1=s1 and s1=x1;
```



#### self join

- 하나의 테이블 내에서 서로 다른 컬럼간에 참조 관계가 있을 때 걸리는 join

```mariadb
select 사원.empno, 사원.ename, 관리자.empno, 관리자.ename
from emp 사원, emp 관리자
where 사원.mgr=관리자.empno(+);
-- ansi
SELECT EMPNO, ENAME, MGR, ENAME 
FROM EMP LEFT OUTER JOIN EMP ON MGR = EMPNO;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf4qb8mfeqj30ls0rc11f.jpg" alt="image-20200525163013615" style="zoom:50%;" /> 



#### nonequi join

> 각 사원의 이름과 월급, 그리고 그 사원의 급여 등급을 출력 

```mariadb
select e.ename, e.sal, s.grade
from emp e, salgrade s
where e.sal between s.losal and s.hisal;

-- ansi 
SELECT ENAME, SAL, GRADE 
FROM EMP JOIN SALGRADE ON(SAL BETWEEN LOSAL AND HISAL);
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gf4ngykqy7j30ls0rctf5.jpg" alt="image-20200525145155974" style="zoom:50%;" /> 



> 각 사원의 이름, 월급, 급여등급, 그가 속한 부서이름을 출력

```mariadb
select e.ename, e.sal, d.dname, s.grade
from emp e, dept d, salgrade s
where e.deptno = d.deptno and e.sal between s.losal sand s.hisal;

-- ansi 
select ename, sal, dname, grade
from emp join dept using(deptno)
join salgrade on(sal between losal and hisal);
```



### join 문제 

1. 사원들의 이름, 부서번호, 부서 이름을 출력

   ```mariadb
   -- oracle
   select e.ename, e.deptno, d.dname
   from emp e, dept d
   where e.deptno = d.deptno;
   
   -- ansi 
   select ename, deptno, dname
   from emp join dept using(deptno);
   ```

   

2. DALLAS에서 근무하는 사원의 이름, 직위, 부서번호, 부서 이름을 출력

   ```mariadb
   -- oracle
   select e.ename, e.JOB, e.deptno, d.dname 
   from emp e, dept d
   where e.deptno = d.deptno and LOC='DALLAS';
   
   -- ansi
   select ename, JOB, deptno, dname 
   from emp join dept using(deptno)
   where LOC='DALLAS';
   ```

   

3. 이름에 'A'가 들어가는 사원들의 이름과 부서이름을 출력

   ```mariadb
   -- oracle
   select e.ename, d.dname
   from emp e, dept d 
   where e.deptno = d.deptno and e.ename like '%A%';
   
   -- ansi
   select ename, dname
   from emp join dept using(deptno)
   where ename like '%A%';
   ```

   

4. 사원이름과 그 사원이 속한 부서의 부서명, 그리고 월급을 출력하는데 월급이 3000 이상인 사원을 출력 

   ```mariadb
   select e.ename, d.dname, e.sal
   from emp e, dept d 
   where e.deptno = d.deptno and e.sal >= 3000;
   
   -- ansi
   select ename, dname, sal
   from emp join dept using(deptno)
   where sal >= 3000;
   ```

   

5. 직위가 'SALESMAN'인 사원들의 직위와 그 사원이름, 그리고 그 사원이 속한 부서 이름을 출력하라 

   ```mariadb
   -- oracle
   select e.job, e.ename, d.dname
   from emp e, dept d 
   where e.deptno = d.deptno and e.job = 'SALESMAN';
   
   -- ansi 
   select job, ename, dname
   from emp join dept using(deptno)
   where job = 'SALESMAN';
   ```

   

6. 커미션이 책정된 사원들의 사원번호, 이름, 연봉, 연봉+커미션, 급여등급을 출력하되, 각각의 컬럼명을 '사원번호', '사원이름', '연봉', '실급여', '급여등급'으로 하여 출력하라 

   ```mariadb
   -- oracle
   select e.empno 사원번호, e.ename 이름, e.sal*12 연봉, (e.sal+e.comm)*12 실급여, s.grade 급여등급
   from emp e, salgrade s 
   where e.sal between s.losal and s.hisal and e.comm is not null;
   
   -- ansi 
   select empno 사원번호, ename 이름, sal*12 연봉, (sal+comm)*12 실급여, grade 급여등급
   from emp join salgrade on (sal between losal and hisal)
   where comm is not null;
   ```

   

7. 부서번호가 10번인 사원들의 부서번호, 부서이름, 사원이름, 월급, 급여등급을 출력하라

   ```mariadb
   -- oracle
   select e.deptno, d.dname, e.ename, e.sal, s.grade
   from emp e, dept d, salgrade s 
   where e.deptno = d.deptno 
   and e.sal between s.LOSAL and s.HISAL 
   and e.deptno = 10;
   
   -- ansi 
   select deptno, dname, ename, sal, grade
   from emp join dept using(deptno)
   join salgrade on (sal between losal and hisal)
   where deptno = 10;
   ```

   

8. 부서번호가 10번, 20번인 사원들의 부서번호, 부서이름, 사원이름, 월급, 급여등급을 출려하라. 그리고 그 출력된 결과물을 부서번호가 낮은 순으로, 월급이 높은 순으로 정렬하라 

   ```mariadb
   -- oracle
   select e.deptno, d.dname, e.ename, e.sal, s.grade
   from emp e, dept d, salgrade s 
   where e.deptno = d.deptno 
   and e.sal between s.LOSAL and s.HISAL 
   and e.deptno in (10, 20)
   order by e.deptno, e.sal desc;
   
   -- ansi 
   select deptno, dname, ename, sal, grade
   from emp join dept using(deptno)
   join salgrade on (sal between losal and hisal)
   where deptno in (10,20)
   order by deptno, sal desc;
   ```

   

9. 사원번호와 사원이름, 그리고 그 사원을 관리하는 관리자의 사원번호와 사원이름을 출력하되 각각의 컬럼명을 '사원번호', '사원이름', '관리자번호', '관리자이름'으로 하여 출력하라 

   ```mariadb
   -- oracle 
   select e1.empno 사원번호, e1.ename 사원이름, e2.empno 관리자번호, e2.ename 관리자이름
   from emp e1, emp e2
   where e1.mgr = e2.empno;
   
   -- ansi
   select e1.empno 사원번호, e1.ename 사원이름, e2.empno 관리자번호, e2.ename 관리자이름
   from emp e1 left outer join emp e2 on e1.mgr = e2.empno;
   ```

   

10. 자신의 관리자보다 먼저 입사한 모든 사원의 이름 및 입사일을 해당 관리자의 이름 및 입사일과 함께 표시하고 열이름을 각가 --EMPLOYEE, EMPHIREDATE, MANAGER, MGRHIREDATE로 저장한다

    ```mariadb
    -- orcle
    select e1.ename EMPLOYEE , e1.hiredate EMPHIREDATE , e2.ename MANAGER, e2.hiredate MGRHIREDATE
    from emp e1, emp e2
    where e1.mgr = e2.empno and e1.hiredate < e2.hiredate;
    
    -- ansi
    select e1.ename EMPLOYEE , e1.hiredate EMPHIREDATE , e2.ename MANAGER, e2.hiredate MGRHIREDATE
    from emp e1 left outer join emp e2 on e1.mgr = e2.empno
    where e1.hiredate < e2.hiredate;
    ```

    

11. 해당 부서의 모든 사원에 대한 부서이름, 위치, 사원 수 및 평균 급여를 -- 표시하는 정의를 작성한다
    열 이름을 각각 DNAME, LOC, NUMBER OF PEOPLE, SALARY로 한다

```mariadb
-- oracle 
select d.dname DNAME, d.loc LOC, count(e.empno) "NUMBER OF PEOPLE", avg(e.sal) SALARY
from emp e, dept d
where d.deptno = e.deptno
group by d.dname, d.loc;

-- ansi
select dname, loc, count(empno) "NUMBER OF PEOPLE", TRUNC(avg(sal),2) SALARY
from dept join emp using(deptno)
group by dname, loc;
```



