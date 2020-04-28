# Day 14

## 학습목표 

1. java.util의 컬렉션 객체를 이해할 수 있다. 
2. 데이터 자료 관리를 원하는 객체를 통해 탐색(CRUD)할 수 있다. 
3. 컬렉션의 추상화를 설계할 수 있다.



## 문자열을 관리하는 클래스

1) java.lang.String (static-불변 데이터)

2) java.lang.StringBuffer (가변)- 스레드에 안전함 (데이터를 임시 저장소에 담았다가 보내줌)

3) java.lang.StringBuilder (가변) - 스레드에 안전하지 못함(데이터를 바로 바로 보내줌)

> 스레드는 메서드 단위로 실행하는 것을 말함(메서드 단위로 실행 모듈을 만듬)

- 자료를 관리하는 것들은 **CRUD**를 할 수 있어야함
- **index, value, key** 이 세가지로 데이터를 관리함

### java.lang.StringBuffer 

- 스레드에 안전해서 네트워크 통신등에 유용
- index가 있음 -> 자료를 순차적으로 관리 

#### CRUD

- Create
  - StringBuffer()
  - StringBuffer(String)
  - StringBuffer(int) // capacity() -> 기본 용량은 16 글자 //capacity가 늘어나기 때문에 가변 가능 
  - append()
  - length()
- Read
  - toString()
- Update
  - insert()
- Delete
  - delete()
- trimToSize()



### java.lang.StringBuilder

- 문자열을 가변으로 받을 때 사용

- Create
  - StringBuilder()
  - StringBuilder(String)
  - StringBuilder(int)
  - append()
- Read
  - toString()
- Update
  - insert()
- Delete
  - delete()
- trimToSize()



## java.util의 컬렉션 객체 

- Iterator, Enumeration :자료를 탐색하는 메소드를 가진 인터페이스 
- Collection 
  - utill 팩키지의 root class 
  - CRUD 메소드가 선언된 인터페이스 
  - add() -> [Create/Read] , clear() -> [Delete], remove() -> [Delete], size(), toArray(), iterator() -> [Read]

#### 데이터를 관리하는 방법(index, value, key)

1. index : **List** Interface

   - [0],[1],[2],[3],...

   - 중복데이터 O, NULL O
   - ArrayList, Vector
   - get(index), set(index, Element)

2. value : **Set** Interface

   - 1,2,3,'a'
   - 중복데이터 X, NULL X

3. key : **Map** Interface

   - a = 'b', b = 'd', ex) {1:'3'} , {1 = '3'}
   - key는 중복 데이터 X, NULL X
   - value는 중복데이터 O, NULL O
   - key(Set), value(List)

### index로 데이터를 관리하는 List 객체



