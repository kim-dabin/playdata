# DAY56

## 학습목표

1. 커스텀 태그를 작성할 수 있다 
2. servlet의 필터링을 할 수 있다 
3. ServletContext, ServletConfig



## 커스텀 태그

### ver 1.2

- JavaFX.servlet.jsp.tagext.* 중 상속을 받아 메소드를 재정의 함
- 태그가 생성되면 /WebConect/WEB-INF/tld 폴더를 만든 다음 생성된 태그를 호출해서 사용함 



### ver 2.0 

- SimpleTagSupport 상속받아 메소드 정의 
- 추가로 bean 객체에 값을 담아 속성을 만들어 저장하거나 전역변수를 만들어 저장한 것을 .tld에 정의한 다음 .jsp에서 태그로 호출 

> jsp2-example-taglib.tld

```xml
<?xml version="1.0" encoding="UTF-8"?>

<taglib xmlns="http://java.sun.com/xml/ns/j2ee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-jsptaglibrary_2_0.xsd"
    version="2.0">
    <description>A tag library exercising SimpleTag handlers.</description>
    <tlib-version>1.0</tlib-version>
    <short-name>SimpleTagLibrary</short-name>
    <uri>http://tomcat.apache.org/jsp2-example-taglib</uri>
    <tag>
        <description>Outputs Hello, World</description>
        <name>helloWorld</name>
        <tag-class>com.simpletag.HelloWorldSimpleTag</tag-class>
        <body-content>empty</body-content>
    </tag>
    <tag>
        <description>Populates the page context with a BookBean</description>
        <name>findBook</name>
        <tag-class>com.simpletag.FindBookSimpleTag</tag-class>
        <body-content>empty</body-content>
        <attribute>
            <name>var</name>
            <required>true</required>
            <rtexprvalue>true</rtexprvalue>
        </attribute>
    </tag>
    <function>
        <description>Converts the string to all caps</description>
        <name>caps</name>
        <function-class>com.el.Functions</function-class>
        <function-signature>java.lang.String caps( java.lang.String )</function-signature>
    </function>
</taglib>


```



> book.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib prefix="my" uri="/WEB-INF/tld/jsp2-example-taglib.tld" %>
<html>
  <head>
    <title>JSP 2.0 Examples - Book SimpleTag Handler</title>
  </head>
  <body>
    <br>
    <b><u>Result:</u></b><br>
    <my:findBook var="book"/>
    <table border="1">
        <thead>
        <td><b>Field</b></td>
        <td><b>Value</b></td>
        <td><b>Capitalized</b></td>
    </thead>
    <tr>
        <td>Title</td>
        <td>${book.title}</td>
        <td>${my:caps(book.title)}</td>
    </tr>
    <tr>
        <td>Author</td>
        <td>${book.author}</td>
        <td>${my:caps(book.author)}</td>
    </tr>
    <tr>
        <td>ISBN</td>
        <td>${book.isbn}</td>
        <td>${my:caps(book.isbn)}</td>
    </tr>
    </table>
  </body>
</html>
```





- /WEB-INF/tags/*.tag로 태그를 @로 속성과 변수를 선언한 다음 원하는 값은 <<c:set>>태그로 저장을 하고 .jsp로 바로 호출 => 어노테이션으로 

> ViewFormat.tag

```xml
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt"  prefix="fmt"%>
<%@ attribute name="from" %>
<%@ attribute name="to" %>
<%@ attribute name="by" %>
<%@ variable name-given="f" %>
<%@ variable name-given="c" %>

<c:forEach var="degrees" begin="${from }" end="${to }" step="${by }" >
<c:set var="f" value="${degrees }" />
<c:set var="c">
	<fmt:formatNumber pattern="##0.00">
		${(f - 32) * 5 / 9.0 }
	</fmt:formatNumber>
</c:set>
<jsp:doBody/>
</c:forEach>
```

> TagView.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib prefix="view" tagdir="/WEB-INF/tags" %>
<%@ taglib prefix="calc" tagdir="/WEB-INF/tags" %>

<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
<style>
	th{
	width : 100px;
	}
</style>
</head>
<body>
	<h2>(태그파일을 이용한 섭씨 계산표)</h2>
	<table>
		<tr>
			<th>화씨 온도</th>
			<th>섭씨 온도</th>
		</tr>
		<view:ViewFormat from="32" to="212" by="20">
			<tr>
				<td>${f } </td>
				<td>${c } </td>
			</tr>
		</view:ViewFormat>
	</table>
	<calc:hap a="100" b="200">
	${resultHap }
	</calc:hap>
</body>
</html>
```

