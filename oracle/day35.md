# DAY 35

```sql
create table dept_original
as 
select * from dept where 0=1;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gfeszmlklfj30y4088acn.jpg" alt="image-20200603093828645" style="zoom:50%;" />

```sql
create table dept_copy
as 
select * from dept where 0=1;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gfeszx2thjj30y4088mzq.jpg" alt="image-20200603093848335" style="zoom:50%;" />

```sql
create or replace trigger trigger_sample1
	after insert
	on dept_original
	for each row
begin 
	if inserting then 
		DBMS_OUTPUT.PUT_LINE('creating insert Trigger');
		insert into dept_copy
			values(:new.DEPTNO, :new.DNAME, :new.LOC);
		end if;
end;
/
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gfet5y33rnj30l606wq52.jpg" alt="image-20200603094435513" style="zoom:50%;" />



![image-20200603094905281](https://tva1.sinaimg.cn/large/007S8ZIlgy1gfetamp2y1j30t00rcq68.jpg)

````sql
SET SERVEROUTPUT ON;
CREATE OR REPLACE TRIGGER t
  BEFORE
    INSERT OR
    UPDATE OF sal, deptno OR
    DELETE
  ON rec_emp
BEGIN
  CASE
    WHEN INSERTING THEN
      DBMS_OUTPUT.PUT_LINE('Inserting');
    WHEN UPDATING('sal') THEN
      DBMS_OUTPUT.PUT_LINE('Updating salary');
    WHEN UPDATING('deptno') THEN
      DBMS_OUTPUT.PUT_LINE('Updating department ID');
    WHEN DELETING THEN
      DBMS_OUTPUT.PUT_LINE('Deleting');
  END CASE;
END;
/
````

![image-20200603095504069](https://tva1.sinaimg.cn/large/007S8ZIlgy1gfetgugpoaj30nm054ta5.jpg)

![image-20200603095519131](https://tva1.sinaimg.cn/large/007S8ZIlgy1gfeth3mkttj30qe05475w.jpg)

![image-20200603095550018](https://tva1.sinaimg.cn/large/007S8ZIlgy1gfethmydpnj30m205475l.jpg)





> [**Example 9-2 Trigger Logs Changes to EMPLOYEES.SALARY**](https://docs.oracle.com/cd/E11882_01/appdev.112/e25519/triggers.htm#BCFBDJEC)
>
> -> 많이 쓰이는 트리거 

```sql
DROP TABLE Emp_log;
CREATE TABLE Emp_log (
  Emp_id     NUMBER,
  Log_date   DATE,
  New_salary NUMBER,
  Action     VARCHAR2(20));
```

```sql
CREATE OR REPLACE TRIGGER log_salary_increase
  AFTER UPDATE OF sal ON emp
  FOR EACH ROW
BEGIN
  INSERT INTO Emp_log (Emp_id, Log_date, New_salary, Action)
  VALUES (:NEW.empno, SYSDATE, :NEW.sal, 'New Salary');
END;
/
```

```sql
UPDATE emp
SET sal = sal + 1000.0
WHERE deptno = 20;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gfetoua9wpj30l40gsaf9.jpg" alt="image-20200603100244540" style="zoom:50%;" /> 





> 보통 new는 키워드인데 new로 테이블을 생성할 때는 키워드와 테이블 이름이 같기 때문에 Newest라는 별칭(내가 임의로 만들수 있음)을 키워드로 사용함 

```sql
CREATE TABLE new (
  field1  NUMBER,
  field2  VARCHAR2(20)
);

CREATE OR REPLACE TRIGGER Print_salary_changes
BEFORE UPDATE ON new
REFERENCING new AS Newest -- Newest == new 
FOR EACH ROW
BEGIN
  :Newest.Field2 := TO_CHAR (:newest.field1);
END;
/
```



> [**Example 9-6 Trigger References OBJECT_VALUE Pseudocolumn**](https://docs.oracle.com/cd/E11882_01/appdev.112/e25519/triggers.htm#BCFFIBBA)
>
> 나만의 타입을 만들어서 타입을 동일하게 관리할 때
>
> TYPE [type name] AS OBJECT (type1, type2,,,)

```sql
CREATE OR REPLACE TYPE t AS OBJECT (n NUMBER, m NUMBER)
/ -- Type created.
CREATE TABLE tbl OF t
/ -- Table created.
BEGIN
  FOR j IN 1..5 LOOP
    INSERT INTO tbl VALUES (t(j, 0));
  END LOOP;
END;
/ -- PL/SQL procedure successfully completed.
SELECT * FROM tbl ORDER BY n;
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gfeu93w1eij30hg0bqabu.jpg" alt="image-20200603102213744" style="zoom:50%;" /> 



> Create history table and trigger:

```sql
CREATE TABLE tbl_history ( d DATE, old_obj t, new_obj t)
/
CREATE OR REPLACE TRIGGER Tbl_Trg
  AFTER UPDATE ON tbl
  FOR EACH ROW
BEGIN
  INSERT INTO tbl_history (d, old_obj, new_obj)
  VALUES (SYSDATE, :OLD.OBJECT_VALUE, :NEW.OBJECT_VALUE);
END Tbl_Trg;
/
```



## ORACLE PACKAGE(패키지)

- ```sql
  CREATE [ OR REPLACE ] PACKAGE [ schema. ] package_name
     [ invoker_rights_clause ]
     { IS | AS } item_list_1 END [ package_name ] ;
  ```

- 연관성이 높은 함수나 PROCEDURE를 하나의 그룹으로 묶어서 관리하고 사용하는 것

- PACKAGE는 PACKAGE선언부(Spec)와 PACKAGE 몸체부(body)로 구성

  - PACKAGE 선언부 : 해당 패키지에 사용될 함수나 프로시저, 변수 등에 대한 정의를 선언
  - PACKAGE 몸체부 : 선언부에 있는 정의부분, 식별에 실제 CODE로 구현되는 부분
  - 패키지 삭제
    - 패키지 선언부와 몸체부 모두 삭제 : DROP PACKAGE PACKAGE_NAME;
    - 패키지 몸체부만 삭제 :  DROP PACKAGE BODY PACKAGE_NAME;

> 1. 사원 테이블에서 총 급여 합계와 평균 급여를 구하는 my_package를  만들어 실행하자

```sql
-- 1. 선언
CREATE OR REPLACE PACKAGE my_package
	IS
		procedure my_sum;
		procedure my_avg;
END my_package;
/

-- 2. body 부분 코드 
create or replace package body my_package
is 
	procedure my_sum
is 
	cursor e_sum is select sum(nvl(sal,0))	from emp;
	total_sum number;
begin
	open e_sum;
	fetch e_sum into total_sum;
	dbms_output.put_line('봉급합계:'||total_sum);
	close e_sum;
end my_sum; -- 합계 구하는 procedure 끝

procedure my_avg -- my_avg 평균 구하는 procedure 시작 
	is
		cursor e_avg is select avg(nvl(sal,0))	from emp; -- cursor는 select를 받음
		total_avg number;
	begin 
		open e_avg;
		fetch e_avg into total_avg;
		dbms_output.put_line('급여 평균:'||total_avg);
		close e_avg;
	end my_avg; -- 평균 구하는 procedure 끝
end my_package;
/

-- 실행
exec my_package.my_sum;
exec my_package.my_avg;
```

<img src="../../../Library/Application Support/typora-user-images/image-20200603111014289.png" alt="image-20200603111014289" style="zoom:50%;" /> 



[**Example 10-2 Passing Associative Array to Standalone Subprogram**](https://docs.oracle.com/cd/E11882_01/appdev.112/e25519/packages.htm#BABDFGED)

```sql
CREATE OR REPLACE PACKAGE aa_pkg IS
  TYPE aa_type IS TABLE OF INTEGER INDEX BY VARCHAR2(15);
END;
/
CREATE OR REPLACE PROCEDURE print_aa (
  aa aa_pkg.aa_type
) IS
  i  VARCHAR2(15);
BEGIN
  i := aa.FIRST;
 
  WHILE i IS NOT NULL LOOP
    DBMS_OUTPUT.PUT_LINE (aa(i) || '  ' || i);
    i := aa.NEXT(i);
  END LOOP;
END;
/
DECLARE
  aa_var  aa_pkg.aa_type;
BEGIN
  aa_var('zero') := 0;
  aa_var('one') := 1;
  aa_var('two') := 2;
  print_aa(aa_var);
END;
/
```



> 선언과 구현부가 맞지 않음 

```sql
drop package emp_bonus;

CREATE PACKAGE emp_bonus AS
  PROCEDURE calc_bonus (date_hired emp.hiredate%TYPE);
END emp_bonus;
/

-- 밑에꺼는 오류남 
CREATE PACKAGE BODY emp_bonus AS
  -- DATE does not match employees.hire_date%TYPE
  PROCEDURE calc_bonus (date_hired DATE) IS
  BEGIN
    DBMS_OUTPUT.PUT_LINE
      ('Employees hired on ' || date_hired || ' get bonus.');
  END;
END emp_bonus;
/

-- 이게 오류 안남 
CREATE OR REPLACE PACKAGE BODY emp_bonus AS
  PROCEDURE calc_bonus
    (date_hired emp.hiredate%TYPE) IS
  BEGIN
    DBMS_OUTPUT.PUT_LINE
      ('Employees hired on ' || date_hired || ' get bonus.');
  END;
END emp_bonus;
/
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gfevzlrz57j311k0g0wl6.jpg" alt="image-20200603112217978" style="zoom:50%;" /> 





## Exceptions

- 에러 발생 : divisor is equal to zero

```sql
DECLARE
  stock_price   NUMBER := 9.73;
  net_earnings  NUMBER := 0;
  pe_ratio      NUMBER;
BEGIN
  pe_ratio := stock_price / net_earnings;  -- raises ZERO_DIVIDE exception
  DBMS_OUTPUT.PUT_LINE('Price/earnings ratio = ' || pe_ratio);  2    3    4    5    6    7  
 END;
 / -- 에러 발생 : divisor is equal to zero
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gfewcan8bij30ie06qabs.jpg" alt="image-20200603113429478" style="zoom:50%;" /> 



- exception 처리 

````sql
DECLARE
  stock_price   NUMBER := 9.73;
  net_earnings  NUMBER := 0;
  pe_ratio      NUMBER;
BEGIN
  pe_ratio := stock_price / net_earnings;  -- raises ZERO_DIVIDE exception
  DBMS_OUTPUT.PUT_LINE('Price/earnings ratio = ' || pe_ratio);
EXCEPTION
  WHEN ZERO_DIVIDE THEN
    DBMS_OUTPUT.PUT_LINE('Company had zero earnings.');
    pe_ratio := NULL;
END;
/
````

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gfewdmwnnqj30kk03y75i.jpg" alt="image-20200603113547115" style="zoom:50%;" /> 



### [Raising User-Defined Exception with RAISE Statement](https://docs.oracle.com/cd/E11882_01/appdev.112/e25519/errors.htm#BABBCBII)

- RAISE : java의 throw와 비슷함 

```sql
SET SERVEROUTPUT ON;

CREATE OR REPLACE PROCEDURE account_status (
  due_date DATE,
  today    DATE
) AUTHID DEFINER
IS
  past_due  EXCEPTION;  -- declare exception
BEGIN
  IF due_date < today THEN
    RAISE past_due;  -- explicitly raise exception (except을 걸겠다)
  END IF;
EXCEPTION
  WHEN past_due THEN  -- handle exception
    DBMS_OUTPUT.PUT_LINE ('Account past due.');
END;
/
 
BEGIN
  account_status ('1-JUL-10', '9-JUL-10');
END;
/
```



```sql
DECLARE
  salary_too_high   EXCEPTION;
  current_salary    NUMBER := 20000;
  max_salary        NUMBER := 10000;
  erroneous_salary  NUMBER;
BEGIN

  BEGIN
    IF current_salary > max_salary THEN
      RAISE salary_too_high;   -- raise exception
    END IF;
  EXCEPTION
    WHEN salary_too_high THEN  -- start handling exception
      erroneous_salary := current_salary;
      DBMS_OUTPUT.PUT_LINE('Salary ' || erroneous_salary ||' is out of range.');
      DBMS_OUTPUT.PUT_LINE ('Maximum salary is ' || max_salary || '.');
      RAISE;  -- reraise current exception (exception name is optional)
  END;

EXCEPTION
  WHEN salary_too_high THEN    -- finish handling exception
    current_salary := max_salary;

    DBMS_OUTPUT.PUT_LINE (
      'Revising salary from ' || erroneous_salary ||
      ' to ' || current_salary || '.'
    );
END;
/
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gfewtg9szhj30j2044gn9.jpg" alt="image-20200603115058606" style="zoom:50%;" /> 



### RAISE_APPLICATION_ERROR Procedure

- ```sql
  PRAGMA EXCEPTION_INIT ( exception, error_code ) ;
  ```



### Exception Propagation

<img src="https://docs.oracle.com/cd/E11882_01/appdev.112/e25519/img/lnpls009.gif" /> 

<img src="https://docs.oracle.com/cd/E11882_01/appdev.112/e25519/img/lnpls010.gif" /> 

<img src="https://docs.oracle.com/cd/E11882_01/appdev.112/e25519/img/lnpls011.gif" /> 





### 예제

> SQL 구문을 이용해서 프로시저를 만들자 

```java
String myemp_all = "select * from my_emp";
String myemp_vo = "select * from my_emp where ename = ?";
String  myemp_vo2 = "select * from my_emp where ename = ? and empno=?";


//insert, delete, update//
String myemp_insert = "insert into my_emp values(?,?,?)";
String myemp_delete = "delete from my_emp where empno=?";

//사원 번호를 찾아서 이름과 부서번호를 수정하자 
String myemp_update = "update my_emp set ename = ?, deptno? where empno=?";
```



````sql

-- select는 한 줄 이상이라 커서로 받음
create or replace procedure myemp_all(myres out sys_refcursor)
is 
begin 
	open myres for select * from my_emp;
end;
/

var res refcursor; 
SQL> exec myemp_all(:res)
-- String myemp_vo = "select * from my_emp where ename = ?";
create or replace procedure myemp_vo(myname in my_emp.ENAME%type, myres out SYS_REFCURSOR)
is 
begin 
	open myres for
		select * into  from my_emp where ename = myname;
end myemp_vo;
/

-- String  myemp_vo2 = "select * from my_emp where ename = ? and empno=?";
create or replace procedure myemp_vo2(myname in my_emp.ENAME%type, 
                                      myno in my_emp.empno%type,
                                      myres out SYS_REFCURSOR)
is
begin 
	open myres for 
	select * from my_emp where ename = myname and empno=myno;
end myemp_vo2;
/

-- String myemp_insert = "insert into my_emp values(?,?,?)";

create or replace procedure myemp_insert(myno in my_emp.empno%type, myname in my_emp.ENAME%type, myDEPTNO in my_emp.DEPTNO%type)
is
begin 
	insert into my_emp values(myno,myname,myDEPTNO);
end;
/

-- String myemp_delete = "delete from my_emp where empno=?";
create or replace procedure myemp_delete(myno in my_emp.empno%type)
is
begin 
	delete from my_emp where empno=myno;
end;
/

-- String myemp_update = "update my_emp set ename = ?, deptno? where empno=?";
create or replace procedure myemp_update(myno in my_emp.empno%type, myname in my_emp.ENAME%type, myDEPTNO in my_emp.DEPTNO%type)
is 
begin 
	update my_emp set ename = myname, deptno = myDEPTNO where empno=myno;
end;
/
````





## Oracle JDBC Drivers

[docs](https://docs.oracle.com/cd/E11882_01/java.112/e16548/overvw.htm#JJDBC28026)

- Thin driver
  - Oracle 클라이언트 설치 없이 클라이언트 측에서 사용하는 순수 Java 드라이버 입니다. 그것은 애플릿과 어플리케이션 둘 다와 함께 사용될 수 있다.
- Oracle Call Interface (OCI) driver
  - Oracle 클라이언트 설치 시 클라이언트 측에서 사용되며, 어플리케이션으로만 사용할 수 있다.
- Server-side Thin driver
- Server-side internal driver



-  the architecture of Oracle JDBC drivers and Oracle Database.

<img src="https://docs.oracle.com/cd/E11882_01/java.112/e16548/img/dbarch.gif"/> 



### Java Native Interface (JNI)

- native : 모든 머신에 붙어 있음 
- 개발코드는 자바/운영체제를 움직이는 것은 C







```java
{call myemp_all(?)} -> ?안에 out 객체가 기본으로 들어 가있음 //refcursor reference cursor -> 주소값 리턴
  
```

