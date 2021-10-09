---
layout: post
title: "[WINSOCK] 1. 소켓 초기화 -> 생성-> 닫기 -> 종료"
categories: NETWORK
tags: [NETWORK, WINSOCK]
---

## 소켓 초기화와 종료

모든 윈속 프로그램은 소켓 함수를 호출하기 전에 반드시 윈속 초기화 함수인 WSAStartup()을 호출해야 한다.  WSAStartup()함수는 프로그램에서 사용할 윈속 버전을 요청함으로써 윈속 라이브러리(WSA_32.DLL)를 초기화하는 역할을 한다. WSAStartup()함수가 실패할 경우 WSA_32.DLL이 메모리에 로드되지 않는다.

프로그램을 종료할 때는 윈속 종료 함수인 WSACleanup()을 호출해야 한다. WSACleanup()함수는 윈속 사용을 중지함을 운영체제에 알리고, 관련 리소스를 반환하는 역할을 한다.

~~~c++
#pragma comment(lib, "ws2_32")
#include <winsock2.h>

int main(int arc, char *argv[]){
    WSDATA wsa;
    if(WSAStartup(MAKEWORD(2,2), &wsa) != 0)
        return 1;
    MessageBox(NULL, "윈속 초기화 성공", "알림", MB_OK);

    WSACleanup();
    return 0;
}
~~~

윈속 초기화가 성공했으면 소켓을 생성할 준비가 되었다. 

## 소켓 생성

소켓을 사용해 통신하기 윈한 기본 요건은 통신 양단이 같은 프로토콜을 사용하는 것이다. 예를들면 TCP를 사용할 것인지 UDP를 사용할 것인지를 약속해야한다.

socket()함수는 사용자가 요청한 프로토콜을 사용해 통신할 수 있도록 내부적으로 리소스를 할당하고, 이에 접근할 수 있는 일종의 핸들 값(SOCKET타입, 32비트 정수)를 리턴한다.
이 값을 소켓 디스크립터라 부르며 각종 소켓 함수를 호출할 때 인자로 전달해 사용한다.

~~~c++
SOCKET socket(
    int af,       // 주소 체계를 지정
    int type,     // 소켓 타입을 지정
    int protocol, // 사용할 프로토콜을 지정
)
~~~

### 주소 체계(address family)

통신을 하려면 통신 상대를 유일하게 지정할 수 있는 주소가 필요하다. 주소 체계란 이러한 주소 지정 방법을 지칭하는 용어다. 주소 체계는 네트워크 프로토콜의 종류에 따라 달라지므로, 주소 체계 지정은 자신이 사용할 프로토콜을 선택하기 위한 첫 관문이 된다.

### 소켓 타입

소켓 타입은 사용할 프로토콜의 특성을 나타내는 값이다. 소켓 타입은 네트워크 프로토콜의 종류에 따라 달라지므로, 소켓 타입 지정은 자신이 사용할 프로토콜을 선택하기 위한 두번째 관문이 된다. 

|소켓 타입|특성|
|--|---|
|SOCKET_STREAM|신뢰성 있는 데이터 전송 기능 제공, 연결형 프로토콜|
|SOCKET_DGRAM|신뢰성 없는 데이터 전송 기능 제공, 비연결형 프로토콜|

|사용할 프로토콜|주소 체계|소켓 타입|
|--|--|--|
|TCP|AF_INET 또는 AF_INET6|SOCKET_STREAM|
|UDP|AF_INET 또는 AF_INET6|SOCKET_DGRAM|

### 프로토콜

주소 체계와 소켓 타입만으로 프로토콜을 결정할 수 있는 경우도 있지만, 일반적으로 주소 체계와 소켓 타입이 같아도 이에 해당한는 프로토콜이 두 개 이상 존재할 수 있다. 이 때는 프로토콜을 명시적으로 지정해야 하는데, socket()함수의 3번째 인자가 이 역할을 수행한다.

|사용할 프로토콜|주소 체계|소켓 타입|프로토콜|
|--|--|--|--|
|TCP|AF_INET 또는 AF_INET6|SOCKET_STREAM|IPPROTO_TCP|
|UDP|AF_INET 또는 AF_INET6|SOCKET_DGRAM|IPPROTO_UDP|

## 소켓 닫기

소켓을 사용중인 통신을 마치면 관련 리소스를 반환해야 한다. closesocket()함수는 해당 소켓을 닫고 관련 리소스를 반환한다.


~~~c++
int main(int argc, char *argv[])
{
	// 윈속 초기화
	WSADATA wsa;
	if(WSAStartup(MAKEWORD(2,2), &wsa) != 0)
		return 1;
	MessageBox(NULL, "윈속 초기화 성공", "알림", MB_OK);

	// socket()
	SOCKET tcp_sock = socket(AF_INET, SOCK_STREAM, 0);
	if(tcp_sock == INVALID_SOCKET) err_quit("socket()");
	MessageBox(NULL, "TCP 소켓 생성 성공", "알림", MB_OK);

	// closesocket()
	closesocket(tcp_sock);

	// 윈속 종료
	WSACleanup();
	return 0;
}
~~~