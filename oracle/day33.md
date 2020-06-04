# DAY33



## 학습목표

1. 함수와 프로시저를 구현할 수 있다
2. 쿼리를 사용하여 함수와 프로시저를 접목할 수 있다
3. jdbc를 사용하여 프로시저를 구현할 수 있다 .



```sql
set serveroutput on
declare
	v_emp employee%rowtype;
	cursor c1
	is 
	select * from employee
	where commission is not null;
begin 
	dbms_output.put_line('사번	이름	급여');
	dbms_output.put_line('------------------');
	for v_emp in c1 loop
		exit when c1%notfound;
    dbms_output.put_line(v_emp'	'||v_emp||'	'||v_sal);
    end loop;
end;
/
```



> cmd에서 입력받는 키워드 : accept 변수
>
> 사원의 이름을 입력받아 출력하는 구문
>
> -- 사원의 이름을 입력받아 출력하느 ㄴ구문
>
> 
>
> 

CMD에서 입력받는 키워드 : accept 변수

accept a

accept a prompt 'input : '

ex)사원의 이름을 입력받아 출력하는 구문을 만들어 보자.

```
select ename, sal
from emp
where ename='king';

declare
   name emp.enmae%type
   sal emp.sal&type;
   
begin

   select ename, sal into name, sal
   from emp
   where ename =&input_name;
   DBMS_OUTPUT.PUT_LINE
```



```sql
declare
   name emp.enmae%row;fff
begin

   select ename, sal into name, sal
   from emp
   where ename =&input_name;
   DBMS_OUTPUT.PUT_LINE
   
  //$
  
```





## 프로시져(procedule)

- 특정 작업을 수행할 수있고, 이름이 잇는 pl/sql 불록으로서 매개변수를 받을 수 잇고 반복적으로 사용할 수 있는 모듈

### 형식 

```sql
create porcedure -- 프로시져 이름
in argumet -- (택1) in - 실행 환경에서 program을 값으로 전달 
out argument -- (택1) out - program에서 실행환경을 값으로 전달
int out argument -- (택1) in 
```



[PL/SQL BLOCK]

-- sql 문장, pl/sql 제어 문장

[exception] -> 선택

-- error가 발생할 때 수행하는 문장 

end 프로시져 이름;



*** 만들어진 stored procedure list 보기 

- select 



> 1. 부서번호 20번인 사원의 사원번호, 이름, 봉급을 구하는 프로시저를 만들자 

select empno, name





```sql
create or replace procedure emp_ex01
is 
	v_empno emp.empno%type; -- 변수 선언
	v_ename emp.ename%type;
	v_sal number(7,2);

	cursor emp_cursor is -- 1. 커서 선언 : 하나 이상의 row를 담을 객체 
		select empno, ename, sal from emp where deptno=20;

begin -- 실제 실행 코드
	open emp_cursor; -- 2. 커서 시작
		loop -- 한 줄 이상 대입변수를 출력, loop 구문 사용
			fetch emp_cursor into v_empno, v_ename, v_sal; -- 3. 변수 대입
			exit when emp_cursor%rowcount > 5 or emp_cursor%notfound;
			dbms_output.put_line(v_empno||' '||v_ename||' '||v_sal);
		end loop;
	close emp_cursor;
end emp_ex01;
/
```



```sql
SQL> exec emp_ex01
```

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gfcqtwgwfpj30k804adgz.jpg" alt="image-20200601145238782" style="zoom:50%;" /> 



```mariadb
create or replace procedure ex_view
	is 
		v_ename emp.ename%type;
		v_sal emp.sal%type;
	cursor res is -- 1. 커서 선언 : 하나 이상의 row를 담을 객체 
		select ename, sal from emp;
begin -- 실제 실행 코드
	open res; -- 2. 커서 시작
		loop -- 한 줄 이상 대입변수를 출력, loop 구문 사용
			fetch res into v_ename, v_sal; -- 3. 변수 대입
			exit when res%notfound or res%rowcount > 3;
			dbms_output.put_line(v_ename||' '||v_sal||' '||res%rowcounts);
		end loop;
	close res;
end ex_view;
/
```



```sql
SET SERVEROUTPUT ON ;
create or replace procedure ex_view01
	is 
		r_emp emp%rowtype;
	cursor res is 
		select ename, sal, comm from emp where comm is not null;
	begin 
		open res;
		loop
			fetch res into r_emp.ename, r_emp.sal, r_emp.comm;
			exit when res%notfound;
			dbms_output.put_line(r_emp.ename||' '||r_emp.sal||' '||r_emp.comm);
		end loop;
	close res;
end ex_view01;
/
```



```sql
create or replace procedure emp_ex02
is 
	v_emp emp%rowtype; -- 변수 선언
	cursor emp_cursor is -- 1. 커서 선언 : 하나 이상의 row를 담을 객체 
		select empno, ename, sal from emp where deptno=20;

begin -- 실제 실행 코드
	for v_emp in emp_cursor loop
	exit when emp_cursor%rowcount > 5 or emp_cursor%notfound;
  dbms_output.put_line(v_emp.empno||' '||v_emp.ename||' '||v_emp.sal);
  end loop;
end emp_ex02;
/
```



> 2-1 20번 부서 사원의 번호, 이름, 봉급을 출력하자[for loop]

```mariadb
create or replace procedure emp_ex02
is 
	v_emp emp%rowtype; -- 변수 선언
	cursor emp_cursor is -- 1. 커서 선언 : 하나 이상의 row를 담을 객체 
		select empno, ename, sal from emp where deptno=20;

begin -- 실제 실행 코드
	for v_emp in emp_cursor loop
	exit when emp_cursor%rowcount > 5 or emp_cursor%notfound;
  dbms_output.put_line(v_emp.empno||' '||v_emp.ename||' '||v_emp.sal);
  end loop;
end emp_ex02;
/
```



> 실행시 부서번호를 전달 [커서를 통해 전달]해서 해당 부서번호를 출력하는 프로시저를 만들자 

```mariadb
select empno, ename, sal
from emp
where deptno = 10; v_deptno

select empno, ename, sal
from emp
where dep
where deptno = 20; v_deptno

create or replace prcedure emp_ex03
is
	v_empno emp.empno%type;
	v_ename emp.ename%type;
	
	cursor emp_cursor(v_deptno number) is 
		select empno, ename, sal from emp where deptno = v_detpno;
	begin 
		open emp_cursor(10);
		loop 
		fetch emp_cursor into v_empno, v_ename, v_sal;
		exit when emp_cursor%rowcount>5 or emp_cursor%notfound
		dbms_output.put_line(v_empno||' '||v_ename||' '||v_sal)
		end loop;
		close emp_cursor(20);
		loop 
		fetch emp_cursor into v_empno, v_ename, v_sal;
		exit when emp_cursor%rowcount > 5 or emp_sursor%notfound;
		dbms_output.put_line(v_empno||' '||v_ename||' '||v_sal);
		end loop
		close emp_cursor;
	end emp_ex03;
	/
```



````mariadb
create or replace procedure emp_ex04(p_empno in emp.empno%type)
	is 
	v_sal emp.sal%type;
	v_update_row numbr;
	begin 
	select sal into v_sal from emp
	where empno = p_empno;
	
	if sql%found then -- 
		dbms_output.put_line('검색한 데이터가 존재합니다.'||v_sal);
		
	update emp set sal = sal*1.1
	where empno = p_empno;
	v_update_row := sql%rowcount;
	dbms_output.put_line('급여가 인상된 사원의 수');
	end if;
	end;
	/
````





> 5. 부서번호에 해당하는 테이블의 봉급을 가져와서 10%인상된 금액으로 수정하고 사원의 번호와 이름, 봉급을 출력

```sql
 create or replace procedure emp_ex05(p_deptno in emp.deptno%type) 
 is
    cursor emp_cursor is
    select empno,ename,sal
    from emp
    where deptno = p_deptno
    for update;
 begin
    for abc in emp_cursor loop
    update emp
    set sal=sal*1.1 --10%급여인상
    where current of emp_cursor; --현재 커서의  row 를 가져와서 수정한다.
    dbms_output.put_line(abc.empno||' '||abc.ename||' '||abc.sal);
 end loop;
end emp_ex05;
/
excute emp_ex05(30);

select ename, sal from emp where deptno=30;

```



## 함수

- 보통 값을 계산하고 결과값을 반환하기 위해서 함수를 많이 사용
- 대부분 구성이 프로시져와 유사하지만 in 파라미터만 사용 반드시 반환될 값의 데이터 타입을 return문에 선언하고 return문을 통해서 반드시 값을 반환



### 형식

```sql
create function 함수명[라파미터] return datatype
is 
[변수 선언 부분]
begin 
-- pl/sql블록에는 적어도 한개의 return 문이 있어야 함
-- pl/sql 블록은 함수가 수행할 내용을 정의한 몸체 부분임
end;
```



> 6. 사원을 입력받아 연봉을 리턴하는 함수를 작성하자

```mariadb
create function getAvg2(v_id in emp.empno%type) return number
is 
	v_sal emp.sal%type := 0;
	vtot number := 0;
	vcomm emp.comm%type;
	begin 
	select sal, comm into v_sal, vcomm
	from emp where empno = v_id; 
	vtot:=v_sal*12 + nvl(vcomm,0);
	return vtot;
end;
/
-- pl/sql블록에는 적어도 한개의 return 문이 있어야 함
-- pl/sql 블록은 함수가 수행할 내용을 정의한 몸체 부분임
end;
```



> 7. 사번을 입력받아서 부서명을 리턴하는 함수를 작성하자

```sql
create or replace function getDept(p_empno in emp.empno%type) return varchar2 --문자열로 리턴하겠다.
is
v_deptno emp.deptno%type; -- select문에서 리턴을 하는 부서번호 대입
v_res varchar2(60); -- 출력구문, 부서명을 저장하는 변수

begin
select deptno into v_deptno from emp
where empno = p_empno; -- 사원번호에 해당하는 부서번호 출력

if v_deptno = 10 then
      v_res :='ACCOUNTING 부서 사원입니다';
elsif v_deptno =20 then
      v_res :='RESEACH 부서 사원입니다';
elsif v_deptno =30 then
      v_res :='SALES 부서 사원입니다';      
elsif v_deptno =40 then
      v_res :='OPERATIONS 부서 사원입니다';
      
end if;

return v_res;
end;
/

var res varChar2(20);
execute :res :=getdept(7900);

[확인]
select ename, empno, deptno, getdept(empno) from emp;


```



```mariadb
create or replace procedure ex_getDept(p_empno in emp.empno%type, v_res out varchar2) is 
	v_deptno emp.deptno%type;

	begin
	select deptno into v_deptno from emp where empno = p_empno;

	IF v_deptno = 10 THEN v_res := 'ACCOUNTING 부서 사원입니다';
	ELSIF v_deptno = 20 THEN v_res := 'RESEARCH 부서 사원입니다';
	ELSIF v_deptno = 30 THEN v_res := 'SALES 부서 사원입니다';      
	ELSIF v_deptno = 40 THEN v_res := 'OPERATIONS 부서 사원입니다';      
	END IF;
end;
/

var res varChar2(50);
execute ex_getDEPT(7900, :res);
print
```

