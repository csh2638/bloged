---
layout: post
title: "[NETWORK] 소켓"
categories: NETWORK
tags: [NETWORK, SOCKET]
---

## 소켓

소켓은 프로세스가 네트워크를 통해 데이터를 전송하거나 수신하기 위한 실제적인 창구 역할을 한다. 프로세스가 데이터를 내보내거나 받기 위해서는 소켓을 열어서 소켓에 데이터를 써 보내거나 소켓으로 부터 데이터를 읽어들여야 한다.

소켓 프로그래밍에서 소켓이라는 용어의 의미를 **데이터 타입** , **통신 종단점** , **네트워크 프로그래밍 인터페이스** 이 세 관점에서 살펴보자.

## 데이터 타입

소켓은 파일 디스크립터 혹은 핸들과 유사한 개념으로, 일단 만들고 나면 함수를 호출하여 손쉽게 네트워크 통신을 수행할 수 있다. 파일 입출력 코드와 소켓 통신 코드의 형태가 비슷하다.

으용 프로그램이 통신하려면 사용할 프로토콜(TCP/IP, UDP/IP등), 송신 측 IP주소, 송신 측 포트번호, 수신 측 IP번호, 수신 측 포트번호 와 같은 요소들이 결정되어야 한다.
운영체제는 내부적으로 이와 같은 정보를 결정하고 관리하는데, 응용 프로그램의 경우 소켓을 사용해 이 정보에 접근할 수 있다. 프로그래밍 관점에서 소켓은 생성과 설정 과정이 끝나면 운영체제의 통신 관련 정보를 참조해 다양한 작업을 편리하게 할 수 있는 데이터 타입으로 볼 수 있다.

## 통신 종단점(communication end-point)

소켓은 응용 프로그램 관점에서 통신 종단점, 즉 통신의 출발점과 도착점이라고 간주할 수 있다.

클라이언트는 자신의 소켓이 서버 소켓과 연결된 것으로 생각하고 send()함수를 호출해 데이터를 보낸다. 서버도 자신의 소켓이 클라이언트 소켓과 연결된 것으로 생각하고 recv()함수를 호출해 데이터를 받을 수 있따. 서버가 클라이언트에 데이터를 보내는 경우도 이와 같은 원리로 생각할 수 있다.

## 네트워크 프로그래밍 인터페이스

TCP/IP 프로토콜의 관점에서 소켓은 네트워크 프로그래밍 인터페이스에 불과하다. 따라서 응용 프로그램이 통신하기 위해 얀쪽 모두 소켓을 사용해야 하는 것은 아니다. 둘다 같은 프로토콜을 사용하고 정해진 형태와 절차에 따라 데이터를 주고 받으면 된다.

TCP/IP 프로토콜 구조에서 소켓은 일반적으로 응용 계층과 전송 계층 사이에 위치한다고 간주한다. 하지만 전송 계층을 뛰어놈고 곧바로 인터넷 계층을 사용할 수도 있다.


## 윈도우 소켓(윈속)

윈도우 소켓은 유닉스 소켓에 기반을 둔 네트워크 프로그래밍 인터페이스이며 소스코드 수순에서 비교적 호환성이 높다.
