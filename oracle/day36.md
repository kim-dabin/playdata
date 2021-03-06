# Day36



## 데이터베이스 설계 

### 데이터베이스 정의

- 한 조직의 여러 응용 시스템들을 다수의 사용자에 의해 공용(shared)되기 위해 통합(integrated) 저장된(stored) 운영(operational)데이터의 집합

- 업무와 파일 연관성
  - 응용 프로그램 <-> 데이터 파일 
  - ex) 수강업무 <-> 수강화일 // 성적업무 <-> 성적화일 // 학적업무 <-> 학적화일

### 파일 관리 시스템, 데이터베이스 시스템

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gffyd83hb8j30i405ujv3.jpg" alt="image-20200604093006631" style="zoom:50%;" />  ex) 하둡

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gffye3tpvvj30i409ogpn.jpg" alt="image-20200604093059758" style="zoom:50%;" /> ex) 오라클 



### File & Database

- 데이터 독립성
  - 논리적 독립성
  - 물리적 독립성
- 데이터 중복성의 문제 
  - 일관성 
    1. 여러가지 관계성 데이터를 join(데이터무결성을 전제로함)
    2. index화 함 
    3. view
  - 보안성
  - 경제성
- 무결성(**integrity**)
  - 데이터 정확성 보장
  - 무결성 유지 방법
    - 검증 프로그램에 의한 통제 
- 동시 액세스(Concurrent access)
  - 동일 데이터의 다수 사용자 접근
  - 트랜잭션이 원활해야함



### 데이터베이스 특징

- 데이터베이스 시스템의 자기 기술성(self-describing)

  - 메타데이터는 데이터베이스 구조, 
  - 데이터항목의 타입 및 저장형식 데이터에 대한 제약조건 등을 포함

- 프로그램과 데이터의 격리 및 추상화 - MVC 패턴으로 분리 

  - 프로그램-데이터 독립성(Model)
    - 프로그램 -> 실행단 
    - 데이터 -> 관리단 
    - 실행 부분과 관리하는 부분은 분리 되어야함
  - 프로그램-연산 독립성(Controll)
  - 사용자에게 데이터에 대한 개념적 표현 제공(View)

- 다중 뷰(view) 제공

  - 각 사용자에게 자신이 관심을 가지는 데이터베이스의 일부만을 제공하는 뷰를 제공

- 데이터 공유와 다수 사용자 트랜잭션 처리 

  - 여러 사용자가 동시에 dbms를 통해서 데이터베이스의 데이터를 공유

- 데이터베이스 관리시스템(DBMS) 구조 - 데이터의 사용 영역과 관리 영역이 분리되어 있기에 파일 처리 시스템의 많은 문제를 해결

  ![데이터베이스 관리시스템(DBMS) 구조 이미지입니다. 데이터의 사용 영역과 관리 영역이 분리되어 있기에 파일 처리 시스템의 많은 문제를 해결할 수 있습니다.](https://tva1.sinaimg.cn/large/007S8ZIlgy1gffza1tje5j30sg0g0q3t.jpg) 

### 데이터 모델 유형

- 개념적 데이터 모델
  - E-R모델, 이진 모델, 의미 모델, 정보논리 모델, 함수적 모델
- 물리적 데이터 모델
  - 프레임 메모리, 연합 모델
- 구현 데이터 모델
  - 계층형, 망형, 관계형, 객체 지향형, 객체 관계형



#### E-R 데이터 모델

- 실세계 인식에 기촨 것으로, 실세계 객체를 나타내는 개체(entity)들과 개체들간의 관계(relationship)로 구성
  - 개체 : 실세계의 정보 대상
  - 관계 : 여러 개체들 간의 연관성
  - 속성 : 개체의 특성이나 상태 기술
  - 영역(도메인) : 속성이 가질 수 있는 값의 범위 
- 테이블 형태의 릴레이션(relation)의 집합
  - 튜블(tuple)
  - 속성
  - schema
- SQL이라는 표준 질의어 사용



### 데이터 모델링

- 데이터베이스를 표현하는 과정

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gfg0a78pyuj31cc0mq1dy.jpg" alt="image-20200604103625953" style="zoom: 33%;" /> 



### 데이터베이스 사용자

- 데이터베이스 관리자 : DBA
- 최종 사용자(end users)
  - 일반 사용자 
  - 전문 사용자
  - 독자적인 사용자
- 시스템 분석가 및 응용 프로그래머(system analysts/ application programmers)



### 데이터베이스 사용의 장점

- 백업과 회복 기능
- **표준화된 데이터 관리** 
- 응용 프로그램의 개발 시간 단축
- 데이터 구조 변경에 융통성 부여 
- 항상 최신이 정보를 제공
- 규모의 경제성  



### 데이터베이스 사용의 단점

- 운영비 오버헤드
- 복잡한 자료처리 방법
- 백업 및 회복의 오버헤드

> 오버헤드 : 어떤 처리를 하기 위해 들어가는 간접적인 처리 시간 · 메모리 등



### 데이터베이스를 사용하지 않아도 좋은 경우

- 단일 사용자만 접근하는 경우
- 응용이 단순하고, 변경이 잘 일어나지 않는 경우
- DBMS 오버헤드로 인하여 엄격한 실시간 데이터 처리 요구사항을 만족시키기 힘든경우 



## 데이터베이스 시스템

1. 데이터베이스 시스템 구조
2. 데이터베이스 관리시스템
3. 데이터베이스 관리시스템 구성요소
4. dbms

### 데이터베이스 시스템 구조 

- 추상화와 데이터 독립성 확보
- 외부단계(view level)
  - 개인 사용자 관점, 외부 스키마
- 개념단계(logical level)
  - 데이터베이스의 전체적인 구조 기술, 개념 스키마
- 내부단계(physical level)
  - 저장구조 기술

#### 스키마의 3단계 구조 

- 3단계-스키마 아키텍처의 목적
  - 사용자의 응용과 물리적 데이터베이스의 분리 
- 사용자 접속
  - Host Language: 계산 , 논리 등의 주 처리 기능 제공
  - 데이터 종속언어: 데이터 정의 (DDL)나 조작 (DML)처리 지원
  - 현장 사용자 : 질의어나 폼, 메뉴에 의한 인터페이스
- 사상
  - 외부 스키마로부터 저장된 데이터베이스까지 각 단계별 접근 

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gfg1dzcmkyj30sg0lcq4j.jpg" alt="img" style="zoom:50%;" /> 

### DBMS 수행과정

1. 사용자에 의한 액세스 **요구** 
2. DBMS는 요구를 받아 **분석**
3. DBMS는 외부, 개념, 내부 스키마 **접속**
4. DBMS는 저장 DB의 필수적 연산 **수행**



### 데이터베이스 관리 시스템

- 입출력처리기 
- 파서
  - 처리 요구된 명령어에 대한 구문 분석
- 선행컴파일러
  - 응용 프로그램에서 데이터 조작어 명령들을 추출
- 권한제어
  - 사용자의 권한 검사 
- 무결성 검사기
  - 데이터베이스의 정확성 및 일관성 검사
- 갱신처리기
  - 갱신의 중간형태 저장
- 질의 처리기 
  - 대화식으로 입력도니 고수준 질의들을 처리 
- 최적화기 
  - SQL문장을 구현하기 위한 효율적인 액세스 전략 선정
- 실행 코드 생성
  - 실행할 수 있는 프로그램에 대한 기계어 생성 
- 트랜잭션 관리기 
  - 복수 사용자 환경에서 동시성 문제 해결
- 회복 관리기 
  - 시스템 장애에 관계없이 일관된 상태 유지
- 데이터 관리기 
  - 장치 및 저장관리기 
  - 하드웨어 자원관리 및 DB의 물리적 액세스 
- 사전 관리기 
  - DB의 구조 정보 및 스키마에 대한 정의 

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gfg2br4vovj317q0u0u0k.jpg" alt="image-20200604114707072" style="zoom:50%;" />  



#### DBMS의 프로그램 처리 절차 

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gfg2ay0oosj31180u0tt9.jpg" alt="image-20200604114620735" style="zoom:50%;" /> 

### 데이터베이스 관리자 

- 설계 임무 
  - DB의 설계와 사용규칙 결정
  - 데이터사전, 보안절차 마련, 무결성 유지, 소프트웨어 결정
- 관리임무 
  - 데이터 표현방법과 문서화 기준 결정(데이터 표준화)
- 운용 임무
  - 물리적 저장구조 및 저장매체 선정
  - 액세스 통제 방법 결정, 보안절차 마련, 무결성 유지
- 성능 측정
  - 시스템의 전체적인 효율성 검사 



### 기억장소 정의어(SDL)의 기능

- 기억장치 선택
- 논리적 데이터를 물리적 데이터로 사상
- 색인 구성
- 데이터 물리적 순서 규정
- 물리적 데이터의 표현 규정
- 저장장소의 지정



### 데이터 조작어(DML)

- 조작 기능의 조건
  - 사용하기 쉽고, 자연어에 가까워야 함
  - 정확하고 완전해야 함
  - 데이터 언어의 효율적 구현을 지원해야함
- 제어명령
  - 데이터베이스를 액세스하고, 데이터 자원을 이용할 수 있는 명령
- 검색명령
  - 데이터를 선정하고, 선정된 데이터에 대한 연산을 수행
- 수정명령
  - 삽입, 삭제, 갱신의 연산을 수행 



### 웹 응용을 위한 3-tier DBMS 구조 

- GUI, 웹 인터페이스 - 클라이언트
- 응용프로그램, 웹페이지 - 응용 서버 또는 웹서버
- 데이터베이스 관리 시스템(DBMS) - 데이터베이스 서버



### 데이터베이스 설계단계

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gfg3ah4cjij31d80sl11u.jpg" alt="image-20200604122029823" style="zoom:50%;" /> 





### 데이터베이스 설계과정

1. 사용자 요구분석
   - 개발업무 대상 및 사용자 요구 분석
2. 개념적 데이터베이스 설계
   - DB에 저장해야할 데이터를 모형으로 표현
   - E-R모델(관계형 지원), 객체지향 모델(객체지향DB지원)
3. 논리적 데이터베이스 설계
   - 특정 DBMS를 사용하기 위한 논리적 구조의 스키마 설계
4. 물리적 데이터베이스 설계
   - 데이터베이스 스키마를 실제 구축



### 데이터 모델링의 개념

- 정보 시스템 구축을 위한 데이터와 피룡한 정보 분석 방법
- 모델링 범주 
  - 데이터관점(what, data)
  - 프로세스 관점(how, process)
  - 데이터와 프로세스의 상관 관점(interaction)
- 데이터모델링 진행시 중요사항
  - 업무가 관여하는 어떤 것(thing)
  - 업무가 관여하는 어떤 것 같의 관계(relationship)
  - 어떤 것의 성격(attribute)
  - 예) 학생들이 다음 학기 과목인 데이터베이스를 수강신청할 경우, 학생 중 이름은 홍길동, 학번은 100번 학년은 3학년, 이수구분은 전공필수 과목이다. 
- 프로세스 모델링
  - 업무 처리에 필요한 프로세스분석
  - 트리 자료구조로 각 프로세스 분해
- 상관 모델링
  - 데이터, 프로세스 분석
  - E-R 개체 타입과 단위 프로세스 이용
  - 데이터 모델과 프로세스 품질 

### 모델링 도구

- Entity(개체)
- Attribute(속성)
- Relation(관계)



### ER 다이어그램에서 사용되는 기호와 의미

![img](https://tva1.sinaimg.cn/large/007S8ZIlgy1gfg3oy4jv4j30qo0k0taa.jpg)

* 유도된 속성 : 햄버거 세트 사면 콜라가 따라오는거



### 모델링 도구 

#### 엔티티(개체)

- 엔티티는 현실세계의 유무형의 사물
- 사각형으로 표현

#### Attribute 속성

- 속성은 각 엔티티가 가질 수 있는 속성
- 고객 엔티티에서 고객번호는 특정 고갤을 유일하게 구분



#### 속성의 구분 

- 단순속성 VS 복합속성
- 단일 값 속성 VS 다중 값 속성(하나의 속성에 값이 여러개)
- 유도된 속성 VS 저장된 속성
- 널 속성
  - 객체가 속성에 값을 갖지 않을 때 사용
- 개체 집합
- 키 속성
- 값 집합

#### Relation 

- 관계는 여러 엔티티간의 연관성을 의미

- 관계를 나타내기 위해서 두 엔티티들을 연결하는 실 선들이 있고, 그 사이에 마름모 모양의 표시

- 사상 원소수(mapping carinality)

  ![테이블 간의 관계](https://tva1.sinaimg.cn/large/007S8ZIlgy1gfg6fena16j30ur09aq3c.jpg)

  - 일 대 일(1:1)
    - 한명의 고객에게 한번의 대출만 가능하다면 그 관계는 일대일 관계가 성립

  - 일 대 다 (1:M)
    - 한명의 고객이 여러번의 대출을 받았다면 이 관계는 일대 다의 관계가 성립
  - 다 대 일(M:1)
    - 하나의 대출 상품에 대해 여러명의 고객이 대출 받았다면 이 관계는 다대일의 관계가 성립
  - 다 대 다(M:N)
    - 한 고객이 여러번의 대출을 받고 하나의 대출 계좌에 대해 여러 고객이 대출을 받았다면 이 관계는 다대다의 관계가 성립

  

### 역할(Role)

- 관계의 엔티티 집합들이 반드시 구분될 필요는 없음



### 순환(recursive)적 관계의 구조

- 1은 감독(상사)의 역할 

- 2는 사원(부하)의 역할

  r6 : e7	

### 지배개체와 종속개체

- 엔티티 y가 있어야만 엔티티 x가 존재할 때 x는 y에 존재 종속되었다고 함
- y는 지배 엔티티(아래 예시)
- x는 종속 엔티티 
- 대부 엔티티가 삭제되면, 그에 연관된 모든 지불 엔티티 또한 삭제되어야함

### 약 개체 집합

- 주키를 갖지 않는 엔티티 집합을 약 엔티티 집합이라고 함 
- 강 엔티티 집합이 있어야만 약 엔티티집합이 존재
- 약 엔티티 집합은 일 대 다 관계 집합을 통해 강 엔티티 집합에 연결되어야함
- 약 엔티티 집합의 구별자(또는 부분 키)는 약 엔티티 집합내의 엔티티들을 구분하는 애트리뷰트의 집합
- 약엔티티 집합의 주 키는 약 엔티티 집합이 존재 종속 관계에 있는 강 엔티티 집합의 주 키와 약 엔티티 집합의 구별자가 합쳐져 이루어짐
- 약 엔티티 집합은 이중 사각형으로 나타냄
- 약 엔티티 집합의 구별자에는 점선으로 밑줄을 그음
- 지급번호 - 지불 엔티티 집합의 구별자
- 지불의 주 키 - (대부번호, 지급번호)



### 세분화

- 하향식 설계 절차: 집합내의 서로 다른 엔티티와 구분되는 엔티티 집합내의 서브 그룹핑으로 지정
- 이들 서브 그룹핑은 상위 단계 엔티티 집합에는 적용되지 않는 애트리뷰트를 가지거나 관계에 참여하는 하위단계 엔티티 집합이 됨
- ISA라는 라벨을 가진 역삼각형으로 나타낸다(즉, 정기 예금은 "IS A" 계좌 -> 다형성)



### 일반화 

- 상향식 설계 절차: 동일한 기능을 공유하는 여러 엔티티 집합을 상위 단계 엔티티 집합으로 결합
- 세분화와 일반화는 서로가 단순한 역관계이다. : E-R도에서는 같은 방법으로 표현
- 애트리뷰트 상속 - 하위 단계 엔티티 집합은 연결된 상위 단계 엔티티 집합의 모든 애트리뷰트와 관계참여를 상속





### 논리적 데이터 모델링

- E-R 개념적 구조를 관계 데이터 모델(RDB)로 변환
- 단계1: 개체타입 
- 단계2: 약한 개체 타입
- 단계3: 1대1 관계 
- 단계4: 1대 N
- 단계5: N:M
- 단계6: 다중값속성
- 단계7: n차 관계



### 데이터베이스 설계 과정 예

- 데이터 모델링 
  - 기본 객체와 이들 객체 간의 관계 집합
  - 전체 논리 구조 
  - 실세계 조직의 의마와 상호작용을 개념적 스키마로 적용





1. 엔티티 만들기 
   1. 사원 테이블, 부서테이블, 프로젝트 테이블이 있음 
2. 관계형을 나타내는 말을 씀
   1. 사원과 부서는 직무관계에 있음
   2. 사원과 프로젝트는 프로젝트 관리관계
   3. 부서와 프로젝트는 관리통제로 엮어져 있음 
3. 주종관계
   1. 감독이 있음
4. 부양관계
   1. 사원은 부양가족의 종속관계를 갖고 잇음 
   2. 부서는 프로젝트를 할 수도 있고 안할 수도 있음 
5. 사상 관계
   1. 사원
   2. 부서는 프로젝트를 할 수도 있고 안할 수도 있음 ??
6. 속성
   1. 사원테이블에는 사원번호를 주키로 갖고 있고, 이름