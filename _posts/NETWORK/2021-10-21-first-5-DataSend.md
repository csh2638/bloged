---
layout: post
title: "[WINSOCK] 4. 데이터 전송"
categories: NETWORK
tags: [NETWORK, WINSOCK]
---

### 응용 프로그램 프로토콜

응용 프로그램 프로토콜은 응용 프로그램 수준에서 주고받는 데이터의 형식과 의미 그리고 처리방식을 정의한 프로토콜이다. TCP/IP 같은 표준 통신 프로토콜에서 자유롭게 주고받는 패킷의 형식과 의미 그리고 처리 방식을 정의한 것과 비슷하다. 응용 프로그램마다 자유롭게 정의한다는 점, 즉 표준화되어 있지 않다는 것과 비슷하다. 응용 프로그램 프로토콜이 결정되면 데이터를 정해진 형식과 절차에 따라 주고받아 처리하도록 소켓 함수를 구현하면 된다.

### 데이터 전송

### 경계 구분

TCP처럼 메시지 경계를 구분하지 않는 프로토콜을 사용할 경우에는 응용 프로그램 수준에서 메시지 경계를 구분하기 위한 추가 작업을 해야 한다. 다음과 같이 네 가지 방법을 고려할 수 있다.

1. 송신자는 항상 고정 길이 데이터를 보낸다. 수신자는 항상 고정길이 데이터를 읽는다.
2. 송신자는 가변 길이 데이터를 보내고 끝 부분에는 특별한 표시(EOR:End of Record)를 붙인다. 수신자는 EOR이 나올때 까지 데이터를 읽는다.
3. 송신자는 보낼 데이터 크기를 고정 길이 데이터로 보내고 이어서 가변 길이 데이터를 보낸다. 수신자는 고정 긹이 데이터를 읽어서 뒤따라올 가변 데이터의 길이를 알아내고, 이 길이만큼 데이터를 읽는다.
4. 송신자는 가변 길이 데이터를 전송 후 접속을 항상 종료한다. 수신자는 recv()함수의 리턴 값이 0(=정상종료)이 될때까지 데이터를 읽는다.

### 바이트 정렬

서로 다른 바이트 정렬 방식을 사용하는 시스템 사이에 데이터를 교환할 때는 바이트 정렬 방식을 통일해야 한다. 그렇지 않으면 데이터 해석에 문제가 생긴다. 특별한 전제가 없으면 빅 엔디안 방식으로 통일하는 것이 좋다.

### 구조체 멤버 맞춤

구조체 멤버 맞춤(structure member alignment)는 구조체 멤버의 메모리 시작주소를 결정하는 컴파일러 규칙이다. 양쪽 프로그램이 동일한 구조체 멤버 맞춤을 사용한다면 메시지를 전송해도 문제가 되지 않는다.


## 고정길이 데이터 전송



