---
layout: post
title: "[WINSOCK] 7. UDP 서버 - 클라이언트"
categories: NETWORK
tags: [NETWORK, WINSOCK]
---

## 1. UDP 서버-클라이언트 구조

### 1. UDP 프로토콜 개요

TCP와 UDP의 공통점

1. 포트 번호를 이용해 주소를 지정한다. 두 응용프로그램이 TCP나 UDP를 이용해 통신하려면 반드시 포트번호를 결정해야 한다.
2. 데이터 오류를 체크한다. TCP와 UDP는 IP의 패킷 전송 기능을 기반으로 동작한다. 전송 중 여러 원인으로 오류가 발생할 수 있는데, IP는 프로토콜 동작에 필수적인 IP 헤더에 대해서만 오류를 체크하고 데이터는 체크하지 않는다.


|TCP|vs|UDP|
|------|---|---|
|연결형 프로토콜(연결 설정 후 통신가능)||비연결형 프로토콜(연결 설정 없이 통신가능)|
|신뢰성 있는 데이터 전송(데이터를 재전송함)||신뢰성 없는 데이터 전송(데이터를 재전송하지 않음)|
|일대일 통신||일대일 통신, 일대다 통신|
|데이터 경계 구분 안함, 바이트 스트림||데이터의 경계 구분, 데이터그램|


1. 연결 설정을 하지 않으므르 connect()함수 불필요하다.
2. 프로토콜 수준에서 신뢰성 있는 데이터 전송을 보장하지 않으므로, 필요하다면 응용 프로그램 수준에서 신뢰서 있는 데이터 전송 기능을 구현해야 한다.
3. 간단한 소켓 함수 호출 절차만 따르면 다자 간 통신을 쉽게 구현할 수 있다.
4. TCP와 달리 응용 프로그램이 데이터 경계 구분을 위한 작업을 별도로 할 필요가 없다.

### 2. UDP 서버-클라이언트 동작 원리

UDP서버는 TCP서버와 달리 멀티스레드 등의 프로그래밍 기법을 사용하지 않고도 한 소켓으로 여러 클라이언트를 처리할 수 있다.

(a) 서버 실행

서버는 소켓을 생성하고 클라이언트가 데이터를 보내기를 기다린다. 이 때 서버가 사용하는 소켓은 특정 포트번호와 결합되어있어서 이 포트 번호로 도착한 데이터만 수신할 수 있다.

(b) 클라이언트 #1 데이터 통신

첫 번째 클라이언트는 연결 설정 없이 서버와 데이터를 곧바로 주고받는다.

(c) 클라이언트 #1 #2 데이터 통신

두 번째 클라이언트도 연결 서버와 데이터를 곧바로 주고받는다. TCP서버와 달리 UDP서버는 소켓을 한 개만 사용한다는 점에 주의하자.

(d) 클라이언트 #1 #n 데이터 통신

UDP 서버-클라이언트가 통신하는 일반적인 상황이다. 'UDP 클라이언트 #n'처럼 한 클라이언트가 소켓을 두 개 이상 사용해 서버와 통신할 수도 있다.


### UDP에 대한 오해

UDP는 신뢰성 없는 데이터 전송을 하므로 데이터 오류를 체크하지 않을것이라 생각한다면 안된다. UDP는 TCP처럼 체크섬(checksum)을 이용해 데이터 오류를 체크한다. 그렇다면 TCP는 신뢰성 있는 데이터 전송을 하는데 왜 UDP는 그렇지 못할까? 답은 UDP에서 데이터 재전송과 데이터 순서 유지 작업을 하지 않기 때문이다. UDP는 도착한 데이터에 오류가 있다고 판단하면 이 데이터를 응용 프로그램에 전달하지 않고 그대로 삭제해 버린다. 따라서 응용 프로그램은 데이터에 오류가 있어 버려졌다는 사실을 알지 못한다. 또한 TCP는 데이터 순서 유지를 위해 각 바이트마다 번호를 부여하지만 UDP는 비슷한 기능을 제공하지 않는다.

UDP를 이용하는 응용 프로그램에서 신뢰성 있는 데이터 전송을 하려면 데이터 재전송 + 데이터 순서 유지라는 두 가지 기능을 유지해야 한다. 또한 UDP를 이용해 대량의 대이터(bulk data)를 주고 받으려면 흐름 제어 기능(상대의 여유 버퍼 용량에 따라 데이터 송신량을 조절함)도 구현하는 것이 좋다. UDP에는 TCP와 같은 흐름 제어 기능이 없어서 통신 상대의 현재 상태와 관계 없이 대량의 데이터를 보낼 경우, 여유 버퍼 공간의 부족으로 데이터가 버려질 수 있다.


### 3. UDP-서버 클라이언트 실습

- UDP 서버 : 클라이언트가 보낸 데이터를 받고(recvfrom), 이를 문자열로 간주해 무조건 화면에 출력한다(printf). 그리고 받은 데이터를 변경 없이 다시 클라이언트에 보낸다(sendto). 받은 데이터를 그대로 다시 보낸다는 뜻으로 에코서버(echo server)라고 부른다.
- UDP 클라이언트 : 사용자가 키보드로 입력한(fgets) 문자열을 서버에 보낸다(sendto). 서버가 데이터를 그대로 돌려보내면, 클라이언트는 이를 받아(recvfrom) 화면에 출력한다(printf). 에코 서버와 통신한다는 의미로 에코 클라이언트(echo client)라고 부른다.


### UDP SERVER
~~~c++

#pragma comment(lib, "ws2_32")
#include <winsock2.h>
#include <stdlib.h>
#include <stdio.h>

#define SERVERPORT 9000
#define BUFSIZE    512

// 소켓 함수 오류 출력 후 종료
void err_quit(char *msg)
{
	LPVOID lpMsgBuf;
	FormatMessage(
		FORMAT_MESSAGE_ALLOCATE_BUFFER|FORMAT_MESSAGE_FROM_SYSTEM,
		NULL, WSAGetLastError(),
		MAKELANGID(LANG_NEUTRAL, SUBLANG_DEFAULT),
		(LPTSTR)&lpMsgBuf, 0, NULL);
	MessageBox(NULL, (LPCTSTR)lpMsgBuf, msg, MB_ICONERROR);
	LocalFree(lpMsgBuf);
	exit(1);
}

// 소켓 함수 오류 출력
void err_display(char *msg)
{
	LPVOID lpMsgBuf;
	FormatMessage(
		FORMAT_MESSAGE_ALLOCATE_BUFFER|FORMAT_MESSAGE_FROM_SYSTEM,
		NULL, WSAGetLastError(),
		MAKELANGID(LANG_NEUTRAL, SUBLANG_DEFAULT),
		(LPTSTR)&lpMsgBuf, 0, NULL);
	printf("[%s] %s", msg, (char *)lpMsgBuf);
	LocalFree(lpMsgBuf);
}

int main(int argc, char *argv[])
{
	int retval;

	// 윈속 초기화
	WSADATA wsa;
	if(WSAStartup(MAKEWORD(2,2), &wsa) != 0)
		return 1;

	// socket()
	SOCKET sock = socket(AF_INET, SOCK_DGRAM, 0);
	if(sock == INVALID_SOCKET) err_quit("socket()");

	// bind()
	SOCKADDR_IN serveraddr;
	ZeroMemory(&serveraddr, sizeof(serveraddr));
	serveraddr.sin_family = AF_INET;
	serveraddr.sin_addr.s_addr = htonl(INADDR_ANY);
	serveraddr.sin_port = htons(SERVERPORT);
	retval = bind(sock, (SOCKADDR *)&serveraddr, sizeof(serveraddr));
	if(retval == SOCKET_ERROR) err_quit("bind()");

	// 데이터 통신에 사용할 변수
	SOCKADDR_IN clientaddr;
	int addrlen;
	char buf[BUFSIZE+1];

	// 클라이언트와 데이터 통신
	while(1){
		// 데이터 받기
		addrlen = sizeof(clientaddr);
		retval = recvfrom(sock, buf, BUFSIZE, 0,
			(SOCKADDR *)&clientaddr, &addrlen);
		if(retval == SOCKET_ERROR){
			err_display("recvfrom()");
			continue;
		}

		// 받은 데이터 출력
		buf[retval] = '\0';
		printf("[UDP/%s:%d] %s\n", inet_ntoa(clientaddr.sin_addr),
			ntohs(clientaddr.sin_port), buf);

		// 데이터 보내기
		retval = sendto(sock, buf, retval, 0,
			(SOCKADDR *)&clientaddr, sizeof(clientaddr));
		if(retval == SOCKET_ERROR){
			err_display("sendto()");
			continue;
		}
	}

	// closesocket()
	closesocket(sock);

	// 윈속 종료
	WSACleanup();
	return 0;
}

~~~

### UDP CLIENT

~~~c++

#pragma comment(lib, "ws2_32")
#include <winsock2.h>
#include <stdlib.h>
#include <stdio.h>

#define SERVERIP   "127.0.0.1"
#define SERVERPORT 9000
#define BUFSIZE    512

// 소켓 함수 오류 출력 후 종료
void err_quit(char *msg)
{
	LPVOID lpMsgBuf;
	FormatMessage(
		FORMAT_MESSAGE_ALLOCATE_BUFFER|FORMAT_MESSAGE_FROM_SYSTEM,
		NULL, WSAGetLastError(),
		MAKELANGID(LANG_NEUTRAL, SUBLANG_DEFAULT),
		(LPTSTR)&lpMsgBuf, 0, NULL);
	MessageBox(NULL, (LPCTSTR)lpMsgBuf, msg, MB_ICONERROR);
	LocalFree(lpMsgBuf);
	exit(1);
}

// 소켓 함수 오류 출력
void err_display(char *msg)
{
	LPVOID lpMsgBuf;
	FormatMessage(
		FORMAT_MESSAGE_ALLOCATE_BUFFER|FORMAT_MESSAGE_FROM_SYSTEM,
		NULL, WSAGetLastError(),
		MAKELANGID(LANG_NEUTRAL, SUBLANG_DEFAULT),
		(LPTSTR)&lpMsgBuf, 0, NULL);
	printf("[%s] %s", msg, (char *)lpMsgBuf);
	LocalFree(lpMsgBuf);
}

int main(int argc, char *argv[])
{
	int retval;

	// 윈속 초기화
	WSADATA wsa;
	if(WSAStartup(MAKEWORD(2,2), &wsa) != 0)
		return 1;

	// socket()
	SOCKET sock = socket(AF_INET, SOCK_DGRAM, 0);
	if(sock == INVALID_SOCKET) err_quit("socket()");

	// 소켓 주소 구조체 초기화
	SOCKADDR_IN serveraddr;
	ZeroMemory(&serveraddr, sizeof(serveraddr));
	serveraddr.sin_family = AF_INET;
	serveraddr.sin_addr.s_addr = inet_addr(SERVERIP);
	serveraddr.sin_port = htons(SERVERPORT);

	// 데이터 통신에 사용할 변수
	SOCKADDR_IN peeraddr;
	int addrlen;
	char buf[BUFSIZE+1];
	int len;

	// 서버와 데이터 통신
	while(1){
		// 데이터 입력
		printf("\n[보낼 데이터] ");
		if(fgets(buf, BUFSIZE+1, stdin) == NULL)
			break;

		// '\n' 문자 제거
		len = strlen(buf);
		if(buf[len-1] == '\n')
			buf[len-1] = '\0';
		if(strlen(buf) == 0)
			break;

		// 데이터 보내기
		retval = sendto(sock, buf, strlen(buf), 0,
			(SOCKADDR *)&serveraddr, sizeof(serveraddr));
		if(retval == SOCKET_ERROR){
			err_display("sendto()");
			continue;
		}
		printf("[UDP 클라이언트] %d바이트를 보냈습니다.\n", retval);

		// 데이터 받기
		addrlen = sizeof(peeraddr);
		retval = recvfrom(sock, buf, BUFSIZE, 0,
			(SOCKADDR *)&peeraddr, &addrlen);
		if(retval == SOCKET_ERROR){
			err_display("recvfrom()");
			continue;
		}

		// 송신자의 IP 주소 체크
		if(memcmp(&peeraddr, &serveraddr, sizeof(peeraddr))){
			printf("[오류] 잘못된 데이터입니다!\n");
			continue;
		}

		// 받은 데이터 출력
		buf[retval] = '\0';
		printf("[UDP 클라이언트] %d바이트를 받았습니다.\n", retval);
		printf("[받은 데이터] %s\n", buf);
	}

	// closesocket()
	closesocket(sock);

	// 윈속 종료
	WSACleanup();
	return 0;
}

~~~

## UDP 서버-클라이언트 분석


UDP 소켓이 TCP 소켓과 다른점은 데이터 재전송과 흐름 제어를 하지 않으므로 송신 버퍼가 없다는 점이다.

### 1. UDP 서버-클라이언트 모델

UDP 서버

1. socket()함수로 소켓을 생성함으로써 사용할 프로토콜을 결정한다.
2. bind()함수로 지역 IP주소와 지역 포트 번호를 결정한다.
3. 클라이언트가 보낸 데이터를 recvfrom()함수로 받는다. 이때 원격 IP 주소와 원격 포트 번호, 즉 클라이언트의 주소를 알 수 있다.
4. 받은 데이터를 처리한 결과를 sendto()함수로 보낸다.
5. 모든 작업을 마치면 closesocket()함수로 소켓을 닫는다.

UDP 클라이언트

1. socket()함수로 소켓을 생성함으로써 사용할 프로토콜을 결정한다.
2. sendto()함수로 서버에 데이터를 보낸다. 이 때 원격 IP주소와 원격 포트번호는 물론, 지역 IP 주소와 지역 포트번호도 결정된다.
3. 서버가 처리해 보낸 데이터를 recvfrom()함수로 받는다.
4. 모든 작업을 마치면 closesocket()함수로 소켓을 닫는다.

![image](https://user-images.githubusercontent.com/78485996/143779820-35c703c8-5cc7-4325-a9f5-c95ad25a775e.png)

- 블로킹 소켓을 사용할 경우, 송수신 함수의 호출 순서가 맞지 않으면 교착상태(deadlock)가 발생할 수 있다.
- 클라이언트는 데이터를 받은 후 송신자의 주소(IP주소, 포트 번호)를 확인해야 한다. recvfrom()함수는 UDP서버가 보낸 데이터는 물론 전혀다른 UDP응용 프로그램이 보낸 데이터도 수신할 수 있기 때문이다.

UDP소켓에 대해 connect()함수를 호출하면 통신할 상대의 주소 정보가 내부적으로 기억되므로, sendto()/recvfrom()함수 대신 send()/recv()함수를 사용해 특정 UDP서버와 통신할 수 있다.

++ connect()함수를 사용한다는 것은 특정 IP주소와 포트 번호로 나타낼 수 있는 고정된 대상과 통신함을 의미한다. 통신 대상을 변경하려면 언제든지 connect함수를 다시 호출하면 된다.

![image](https://user-images.githubusercontent.com/78485996/143779990-58db86b9-dd89-4268-b721-c4414af1a818.png)

- sendto()함수를 사용한 경우보다 효울적이다. connect()함수로 서버 주소를 한 번만 설정해두면 send()함수가 이 정보를 재사용 하기 때문이다.
- 데이터를 받은 후 송신자의 주소(IP주소, 포트번호)를 확인하지 않아도 된다. recvfrom()함수와 달리 recv()함수는 connect()함수로 설정한 대상을 제외한 다른 UDP 응용 프로그램이 보낸 데이터는 수신하지 않기 때문이다.

그러나 블로킹 소켓을 사용할 경우, 송수신 함수의 호출 순서가 맞지 않으면 교착 상태가 발생할 수 있다.




















































































































































































































































































































































































































































































































