# chapter04
## 정규식

### (?:...)
- 정규표현식을 갖고 와서 원하는 값만 빼고 사용 
- Q) 'def'를 제외시켜서 출력


```python
# -*-coding:utf-8-*-
import re
```


```python
p = re.compile(r'(\w+)\s+(?:\w+)\s+(\w+)')
a= p.search('abc def ghi') #공백으로 그룹을 나눔 
print(a.groups())
```

    ('abc', 'ghi')


### (? P < name > ...) , (? P = name) 
- (? P < name > ...) : 그룹 이름 name을 지정
- (? P = name) : 그룹 name을 지정해서 참조
- Q) 


```python
p = re.compile(r'(?P<word>\w+)\s+(?P=word)') #선언 (?P< word >\w+) => 대입 (?P=word)
a = p.search('python01 python01') # 그룹에 속하는 문자가 같아야함
print(a.group('word'))
print(a.group(1))
```

    python
    python


### 도전과제

- Q 4-4)


```python
telcheck = re.compile(r'(\d{2,3})-(\d{3,4})-(\d{4})')
a= bool(telcheck.match('02-123-4567'))
print(a)
```

    True

