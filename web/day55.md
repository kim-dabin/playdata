# DAY55

## 학습목표

1. EL을 사용할 수 있다
2. JSTL을 사용하고 이해할 수 있다
3. 커스텀 태그를 작성할 수 있다
4. servlet의 필터링을 할 수 있다



## EL

- jsp2
- **EL 이란** 데이터를 표현하기 위한 언어
- 다양한 위치에 있는 데이터에 접근하기 위한 언어로 JSP의 기본 문법을 보완하는 역할
- JSP에서 자바 코드를 대신하여 다른 표현식을 사용함으로서 간단한 방법으로 값을 코딩을 할 수 있음

> 형변환 문제

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ggb958vbasj30go052t9a.jpg" alt="image-20200701111532132" style="zoom:50%;" /> 

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ggb95rnmg4j318k0rmdlt.jpg" alt="image-20200701111604754" style="zoom:50%;" />



### 라이브러리

- jsp를 확장해서 만든 태그 라이브러리 
     - taglibs-standard-spec-1.2.5.jar
     - taglibs-standard-impl-1.2.5.jar
- jsp와 el태그로 만든 확장 라이브러리

     - taglibs-standard-jstlel-1.2.5.jar
- xml 태그를 xpath로 만들어 연동하는 라이브러리 

     - xalan-2.7.1.jar
     - serializer-2.7.1.jar

  jostle-1_0-fr-javadoc.zip

  https://docs.oracle.com/javaee/5/jstl/1.1/docs/tlddocs