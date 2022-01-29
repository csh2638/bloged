---
layout: post
title: "[JSP] JSP - 1"
categories: JSP
tags: [JSP]
---

Modle 1

browser 및 user의 request와 respond를 모두 jsp가 처리.

1. 브라우저에서 jsp로 request
2. jsp가 JavaBean과 연동해 request 처리를 위해 관련된 데이터베이스 접근 및 처리
3. 처리된 request를 jsp가 다시 브라우저로 Response 


Model 2

browser 및 user의 request를 servlet이 처리하고 jsp가 response를 처리.



jsp 처리과정

1. PC/웹 브라우저에서 URL 입력
2. DNS(Domain Name System) 서버에서 URL을 IP주소로 변환
3. 변환된 IP주소의 JSP 페이지 요청 ex. index.jsp
4. 특정한 서버 포트번호(ex. tomcat = 8080)를 통해 웹 서버에 접근한다.
5. 웹 서버에서 jsp/서블릿 컨테이너에 jsp 페이지의 요청/분석을 요청한다.
6. 받은 jsp를 jsp/서블릿 컨테이너가 서블릿(자바 파일)으로 만든다.
7. 만들어진 서블릿(자바 파일)을 클래스 파일로 컴파일 한다.
8. 클래스 파일과 DB 데이터를 추가한 데이터를 버퍼에 담아 한 번에 웹 서버로 전송한다.
9. 웹 서버에서 다시 PC/웹 브라우저로 보낸다. 이때 PC/웹 브라우저마다 가지고 있는 JVM을 통해 클래스 파일을 읽어 HTML 페이지로 만들어 사용자에게 화면을 출력하게 된다. 



jsp 페이지 디렉티브

~~~
<%@ page language="java" contentType="text/html; charset=EUC-KR"pageEncoding="EUC-KR"%>
~~~

|속성|설명|기본 값|
|:---:|:---:|:---:|
|language|jsp 스크립트 코드에서 사용되는 프로그래밍 언어를 지정|Java|
|contentType|jsp가 생성할 문서의 타입 지정|text/html|
|import|jsp 페이지에서 사용할 자바 클래스 지정, 자바를 쓰기 때문에 필요한 패키지 import|true|
|session|세션의 사용 여부, 데이터의 유지를 선택, false시 사용 안함|true|
|buffer|jsp 페이지의 출력 버퍼크기 지정. 기본 8kb|최소 8kb|
|autoFlush|출력 버퍼가 다 차지 않아도 자동으로 버퍼에 있는 데이터를 출력 스트림에 보내고 비울지의 여부를 나타낸다. true일 경우 버퍼의 내용을 웹 브라우저에 보낸 후 버퍼를 비우며 false일 경우 에러를 발생시킨다.|true|
|info|jsp페이지에 대한 설명을 입력|-|
|errorPage|현재 페이지에서 에러가 발생될때 보여지는페이지의 여부를 지정. true일때 여러페이지,false 일때 여러페이지 아님|false|
|pageEncoding|jsp 페이지 자체의 캐릭터 인코딩 지정|-|
 
+ include

~~~jsp
<%@ page language="java" contentType="text/html; charset=EUC-KR"
    pageEncoding="EUC-KR"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
</head>
<body>
	<center>
		<table width = "600" border = "1">
		<!--Top-->
		<tr height = "150">
			<td width = "600" align = "center">
			<%@ include file = "Top.jsp" %>
		</td>
	</tr>
	
	<!--Center-->
	<tr height ="400">
		<td width = "600" align = "center">
		<img alt="" src="img/mark.gif" width = "400" height = "300">
		</td>
	</tr>
	
	<!--Bottom-->
	<tr height = "100">
			<td width = "600" align = "center">
			<%@ include file = "Bottom.jsp" %>
		</td>
		
	</table>
	</center>
</body>
</html>
~~~

위 코드와 같이 include를 사용해 다른 jsp페이지를 가져올 경우 해당하는 jsp 페이지를 합쳐서 하나의
페이지로 인식해 한번에 컴파일이 실행된다.





스크립트릿

<% 
    자바코드  
%>

스크립트릿에서 선언한 변수는 JSP 페이지가 서블릿으로 변환될 때 지역변수로 사용된다.



선언문

<%!    
    자바코드
%>

선언문의 변수는 서블릿으로 변환될 때 멤버변수로 변환되고 선언문에서의 메소드는 서블릿에서 메소드로 만들어진다.


표현식

<%= 
    자바코드
%>

jsp페이지에서 자바의 System.out.println과 유사하게 사용


jsp 페이지 내장객체

자주 사용하는 객체

|내장객체|설명|
|:---:|:---:|
|request|웹 브라우저의 요청 정보를 저장하고 있는 객체|
|response|웹 브라우저의 요청에 대한 응답 정보를 저장하고 있는 객체|
|out|jsp 페이지에 출력할 내용을 가지고 있는 출력 스트림 객체|
|session|하나의 웹 브라우저의 정보를 유지하기 위한 세션 정보를 저장하고 있는 객체 |
|application|웹 어플리케이션 전체 브라우저에서 공유할 수 있는 데이터를 저장하는 객체|
|pagecontext|jsp페이지에 대한 정보를 저장하고 있는 객체|
|page|jsp 페이지를 구현한 자바 클래스 객체|
|config|jsp 페이지에 대한 설정 정보를 저장하고 있는 객체|
|exception|jsp 페이지에서 예외가 발생한 경우에 사용되는 객체|



jsp 액션 태그


|액션 태그명|설명|
|:---:|:---:|
|include|다른 페이지의 실행 결과를 현제의 페이지에 포함시킬때 사용|
|foward|페이지 사이의 제어를 이동시킬때 사용|
|plug-in||
|useBean|JavaBean을 jsp에서 사용할때 사용|
|setProperty||
|getProperty||


액션태그의 include와 페이지 디렉티브의 include 차이
페이지 디렉티브의 include는 한 페이지에 다른 페이지를 붙일 수 있다면
액션태그의 include는 붙임과 동시에 <jsp:param> 을 통해 값도 넘길 수 있다.

내장객체의 response.Redirect와 액션태그의 forward 차이
내장객체의 response.Redirect의 경우 페이지를 이동할때 데이터를 가져가려면 url뒤에 ?name="name"
이런식으로 붙여야 하는데 forward의 경우 이 과정이 필요 없이 데이터가 유지된다. 

jspBean을 쓰는 이유는 jsp와 db 사이의 데이터를 편하게 가져오고 보내려고 사용



커넥션풀(DBCP) 사용 이유
-JDBC를 통하여 DB에 연결하기 위해서는 드라이버를 로드하고 커넥션 객체를 받아와야 한다.
-JDBC를 사용하면 사용자가 요청을 할 때마다 매번 드라이브를 로드하고 커넥션 객체를 생성하여 연결하고
종료하기 때문에 매우 비효율적이다.
-이런 문제를 해결하기 위해 커넥션풀을 사용한다.

커넥션풀(DBCP)
-웹 컨테이너가 실행되면서 커넥션(connection)객체를 미리 풀(pool)에 생성해 둔다.
-DB와 연결된 커넥션(connection)을 미리 생성해서 풀(pool)속에 저장해 두고 있다가 미리 필요할 때에 가져다 쓰고 반환한다.
- 미리 생성해 두기 때문에 db에 부하를 줄이고 유동적으로 연결을 관리 할 수 있다.

DAO(Data access object) : 데이터 베이스에 접속해서 데이터 추가, 삭제, 수정 등의 작업을 하는 클래스



쿠키 = 클라이언트측에 저장되는 데이터

세션 = 서버측에 저장되는 데이터, 웹브라우저에 1개의 세션이 생성됨