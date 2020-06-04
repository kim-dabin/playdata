# Day34



```sql
SET SERVEROUTPUT ON;

create procedure sp_salary
is
v_salary emp.ename%type;
begin
	select sal into v_salary
	from emp
	where ename='smith';
	dbms_output.put_line('smith의 급여는 '||v_salary);
end;
/



create procedure exsalary_ename(
v_ename in emp.ename%type)
is
v_salary emp.sal%type;
begin
	select sal into v_salary
	from emp
	where ename = v_ename;
	dbms_output.put_line(v_ename||'의 급여는 '||v.salary);
end;
/


create or replace function fn_salary_ename(
v_ename in emp.ename%type)
return number
is
v_salary number(7,2);
begin
select sal into v_salary
from emp
where ename=v_ename;
return v_salary;
end;
/

variable v_salary number;
execute :v_salary :=fn_salary_ename('JAMES');
print v_salary;


select ename,fn_salary_ename(ename)
from emp
where ename='FORD'

```



##### sql 구문으로 프로시저, 함수를 자유롭게 구현할 수 있다.

Q1.사원테이블의 사원번호와 이름을 출력하자. EX_VIEW01

```sql
create or replace procedure EX_VIEW01
is
v_empno emp.empno%type;
v_ename emp.ename%type;

cursor emp_cursor is
select empno, ename from emp;

begin
open emp_cursor;
loop
fetch emp_cursor into v_empno, v_ename;
exit when emp_cursor%notfound;
dbms_output.put_line(v_empno||' '||v_ename);
end loop;
close emp_cursor;
end EX_VIEW01;
/

exec EX_VIEW01;
```

Q2. 사원테이블에서 사원의 이름과 연봉을 구해서 리턴하자.EX_VIEW02

```
create or replace procedure EX_VIEW02(myres out SYS_REFCURSOR)--리턴하는 지역변수값을 받는 
is
begin 
open myres for
	select ename, sal*12 from emp;
end EX_VIEW02;
/

VAR MY REFCURSOR;

EXEC EX_VIEW02(:MY)

PRINT MY

```

Q3. 사원의 이름을 입력받아 봉급과 커미션을 구하자.EX_VIEW03

```SQL
--PROCEDURE
create or replace procedure EX_VIEW03(myname in emp.ename%type, myres out SYS_REFCURSOR)
is
begin 
open myres for
	select sal, NVL(comm,0) from emp where ename=myname;
end EX_VIEW03;
/

VAR MYres REFCURSOR;

EXEC EX_VIEW03('SMITH',:MYres)

print MYres

--FUNCTION
create or replace FUNCTION FN_VIEW03(myname in emp.ename%type)
is
myres out SYS_REFCURSOR
begin 
open myres for
	select sal, NVL(comm,0) from emp where ename=myname;
	RETURN MYRES;
end EX_VIEW03;
/


```

Q4. 사원의 부서번호와 부서이름 직업을 리턴하자.EX_VIEW04

```SQL
--PROCEDURE
create or replace procedure EX_VIEW04(myres out SYS_REFCURSOR)
is
begin 
open myres for
	select DEPTNO, DNAME, JOB from emp JOIN DEPT USING(DEPTNO);
end EX_VIEW04;
/

--FUNCTION
create or replace function FN_VIEW04()
is
myres out SYS_REFCURSOR;
begin 
open myres for
	select DEPTNO, DNAME, JOB from emp JOIN DEPT USING(DEPTNO);
	return myres;
end EX_VIEW04;
/

```

Q5. 사원의 번호를 입력받아 부서 이름, 직업, 봉급을 리턴하자.EX_VIEW05

```SQL
--PROCEDURE
create or replace procedure EX_VIEW05(myno in emp.empno%type, myres out SYS_REFCURSOR)
is
begin 
open myres for
	select dname, job, sal from emp join dept USING(DEPTNO) WHERE EMPNO=MYNO;
end EX_VIEW05;
/

--FUNCTION	
create or replace FUNCTION FN_VIEW05(myno in emp.empno%type) RETURN SYS_REFCURSOR
is
myres SYS_REFCURSOR;
begin 
open myres for
	select dname, job, sal from emp join dept USING(DEPTNO) WHERE EMPNO=MYNO;
	RETURN MYRES;
end;
/
	
SELECT FN_VIEW05(EMPNO) FROM EMP;
```





https://docs.oracle.com/cd/E11882_01/appdev.112/e25519/composites.htm#LNPLS502

```sql
CREATE OR REPLACE PACKAGE My_Types AUTHID DEFINER IS
  TYPE My_Rec IS RECORD (a NUMBER, b NUMBER, name varchar2(10)); 
  FUNCTION Init_My_Rec RETURN My_Rec;
END My_Types;
/
CREATE OR REPLACE PACKAGE BODY My_Types IS
  FUNCTION Init_My_Rec RETURN My_Rec IS 
    Rec My_Rec;
  BEGIN
    Rec.a := 0;
    Rec.b := 1;
    Rec.name := 'aaa';
    RETURN Rec;
  END Init_My_Rec;
END My_Types;
/
DECLARE
  r CONSTANT My_Types.My_Rec := My_Types.Init_My_Rec();
BEGIN
  DBMS_OUTPUT.PUT_LINE('r.a = ' || r.a);
  DBMS_OUTPUT.PUT_LINE('r.b = ' || r.b);
  DBMS_OUTPUT.PUT_LINE('r.name = ' || r.name);
END;
/


DROP TABLE schedule;
CREATE TABLE schedule (
  week  NUMBER,
  Mon   VARCHAR2(10),
  Tue   VARCHAR2(10),
  Wed   VARCHAR2(10),
  Thu   VARCHAR2(10),
  Fri   VARCHAR2(10),
  Sat   VARCHAR2(10),
  Sun   VARCHAR2(10)
);
 
DECLARE
  default_week  schedule%ROWTYPE;
  i             NUMBER;
BEGIN
  default_week.Mon := '0800-1700';
  default_week.Tue := '0800-1700';
  default_week.Wed := '0800-1700';
  default_week.Thu := '0800-1700';
  default_week.Fri := '0800-1700';
  default_week.Sat := 'Day Off';
  default_week.Sun := 'Day Off';
 
  FOR i IN 1..6 LOOP
    default_week.week    := i;
    
    INSERT INTO schedule VALUES default_week;
  END LOOP;
END;
/
 
COLUMN week FORMAT 99
COLUMN Mon  FORMAT A9
COLUMN Tue  FORMAT A9
COLUMN Wed  FORMAT A9
COLUMN Thu  FORMAT A9
COLUMN Fri  FORMAT A9
COLUMN Sat  FORMAT A9
COLUMN Sun  FORMAT A9
 
SELECT * FROM schedule;
```

```sql
create table rec_emp
as 
select * from emp
where 0=1;


```

Q6. 사원테이블 rec_emp 테이블에 %rowtype을 이용해서 값을 입력 해보자.

```sql
DECLARE
my_emp rec_emp%rowtype;
BEGIN
for i in 1..5 loop
	my_emp.empno :=i;
	my_emp.ename := i||'님';
	insert into rec_emp values my_emp;	
end loop;
end;
/
```

Q7. 사원테이블 res_emp 테이블에 %rowtype을 이용해서 값을 수정 해보자.

```sql
DECLARE
my_emp rec_emp%rowtype;
BEGIN
my_emp.sal := 1000;
my_emp.job :='SALESMAN';
my_emp.deptno := 30;

for i in 1..5 loop
	update rec_emp
	set ROW = my_emp
	where empno=i;
end loop;
end;
/
```

Q8. SQL% 속성을 이용해서 익명속성 활용을 해보자

```SQL
CREATE OR REPLACE PROCEDURE p (
  dept_no NUMBER
) AUTHID DEFINER AS //권한체계
BEGIN
  DELETE FROM REC_EMP
  WHERE DEPTNO = dept_no;
 
  IF SQL%FOUND THEN //delete 한 결과를 가지고 있다는 것 삭제된 번호 출력
    DBMS_OUTPUT.PUT_LINE (
      'Delete succeeded for department number ' || dept_no
    );
  ELSE
    DBMS_OUTPUT.PUT_LINE ('No department number ' || dept_no);
  END IF;
END;
/

BEGIN
P(30);
P(50);
END;
/
```



Declaring and Defining Explicit Cursors

```SQL
DECLARE
  CURSOR c1 RETURN departments%ROWTYPE;    -- Declare c1
 
  CURSOR c2 IS                             -- Declare and define c2
    SELECT employee_id, job_id, salary FROM employees
    WHERE salary > 2000; 
 
  CURSOR c1 RETURN departments%ROWTYPE IS  -- Define c1,
    SELECT * FROM departments              -- repeating return type
    WHERE department_id = 110;
 
  CURSOR c3 RETURN locations%ROWTYPE;      -- Declare c3
 
  CURSOR c3 IS                             -- Define c3,
    SELECT * FROM locations                -- omitting return type
    WHERE country_id = 'JP';
BEGIN
  NULL;   //이거 잘 봐두기.
END;
/
```

Q9.인라인 뷰의 내용을 수정할 수 있다. 

```
DECLARE
  CURSOR c1 IS
    SELECT t1.DEPTNO, DNAME, ENAME
    FROM DEPT t1,
         ( SELECT DEPTNO, COUNT(*) AS STAFF
           FROM EMP
           GROUP BY DEPTNO
         ) t2
    WHERE (t1.DEPTNO = t2.DEPTNO) AND STAFF >= 5
    ORDER BY STAFF;

BEGIN
   FOR R IN c1
   LOOP
     DBMS_OUTPUT.PUT_LINE ('Department = '
       || dept.DNAME|| ', staff = ' || dept.STAFF);
   END LOOP;
END;
/
```

Q10. 사용자 레코드를 선언한 후 커서로 참조시킨 커서를 변수로 선언한 후 사용자 레코드 멤버에게 값 전달 후 출력 해보자.

```sql
DECLARE
  TYPE EmpRecTyp IS RECORD (
    employee_id NUMBER,
    last_name VARCHAR2(25),
    salary   NUMBER(8,2)); 
    
  TYPE EmpCurTyp IS REF CURSOR RETURN EmpRecTyp; 
  emp_cv EmpCurTyp; 
   v_EmpRecTyp emp_cv%ROWTYPE;
  
BEGIN
  open emp_cv for select empno, ename, sal from emp;
  loop 
  fetch emp_cv into v_EmpRecTyp;
  exit when emp_cv%notfound;
  dbms_output.put_line(v_EmpRecTyp.employee_id||' '||v_EmpRecTyp.last_name||' '||v_EmpRecTyp.salary);
  end loop;
END;
/


DECLARE
  TYPE EmpRecTyp IS RECORD (
    employee_id NUMBER,
    last_name VARCHAR2(25),
    salary   NUMBER(8,2)); 
    
  TYPE EmpCurTyp IS REF CURSOR RETURN EmpRecTyp; 
  emp_cv EmpCurTyp; 
   v_emp emp%ROWTYPE;
  
BEGIN
  open emp_cv for select empno, ename, sal from emp;
  loop 
  fetch emp_cv into v_emp.empno,v_emp.ename,v_emp.sal;
  exit when emp_cv%notfound;
  dbms_output.put_line(v_emp.empno||' '||v_emp.ename||' '||v_emp.sal);
  end loop;
END;
/


```

