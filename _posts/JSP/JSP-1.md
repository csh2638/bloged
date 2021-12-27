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
    
위 코드와 같이 jsp 내에서 <%></%>로 둘러쌓인게 자바로 인식함.

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
 