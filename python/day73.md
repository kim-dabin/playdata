# DAY73

> 모듈 - [c,c#] [java_웹로직] [python + 영상, 이미지, 스트림작업]



- 비어있는 변수[list]는 값이 있는 변수[a]의 타입으로 형변환 됨(주의할것)

```python
>>> a
(1, 2, 3, 4)
>>> list=[]
>>> list=a;
>>> list
(1, 2, 3, 4)
```



- 값을 기준으로 변수의 타입이 정해짐

```python
>>> res=()
>>> type(res)
<class 'tuple'>
>>> res = [1,2,3,4]
>>> type(res)
<class 'list'>
```



- 튜플이 가장 큼

```python
>>> a=1,2,(3,4),[1,2,3]
>>> a
(1, 2, (3, 4), [1, 2, 3])
>>> type(a)
<class 'tuple'>
```

```python
>>> a
(1, 2, (3, 4), [1, 2, 3])
>>> type(a)
<class 'tuple'>
>>> a
(1, 2, (3, 4), [1, 2, 3])
>>> a[2]
(3, 4)
>>> a[2][0]
3
>>> a[3]
[1, 2, 3]
>>> a[3][1]
2
```



```python
>>> a='a1111
  File "<stdin>", line 1
    a='a1111
           ^
SyntaxError: EOL while scanning string literal
>>> a=''a1111
  File "<stdin>", line 1
    a=''a1111
        ^
SyntaxError: invalid syntax
>>> a='''a1111
... 
... d'''
>>> a
'a1111\n\nd'
```

