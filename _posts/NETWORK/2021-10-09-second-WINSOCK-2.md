---
layout: post
title: "[WINSOCK] 2. 소켓 주소 구조체"
categories: NETWORK
tags: [NETWORK, WINSOCK]
---

## 소켓 주소 구조체(socket address structures)

소켓 네트워크 구조체는 네트워크 프로그램에서 필요한 주소 정보를 담고 있는 구조체로, 다양한 소켓 함수의 인자로 사용한다. 프로토콜 체계에 따라 주소 지정 방식이 다르므로 다양한 소켓 구조체가 존제한다. 기본이 되는 것은 SOCKDDR구조체로 ws2def.h파일에 정의되어 있다.

실제 프로그래밍에서는 응용 프로그램이 사용할 프로토콜의 종류에 맞는 별도의 소켓 주소 구조체를 사용한다. 예를들어 TCP/IP에서는 SOCKADDR_IN 또는 SOCKADDR_IN6, IrDA에서는 SOCKADDR_IRDA를 사용한다. 

소켓 주소 구조체는 크기기 크기 때문에 함수 인자로 전달할 때는 항상 주소 값을 사용하며, 반드시 SOCKADDR 포인터형으로 변환해야 한다는 점을 주의해야한다

## 바이트 정렬 함수

바이트 정렬(byte ordering)은 메모리에 데이터를 저장할 때 바이트 순서를 나타내는 용어로. 빅엔디안과 리틀 엔디안 방식이 있다.
빅 엔디안은 최상위 바이트(MSB)부터 차례로 저장하는 방식이며, 리틀 엔디안은 최하위 바이트(LSB)부터 차례대로 저장하는 방식이다. 시스템에서 사용되는 바이트 정렬방식은 CPU와 운영체제에 따라 다르다.

파일에 데이터를 읽어오는 경우나 네트워크를 통해 데이터를 송신하고 수신하는 경우에 발생할 수 있는 에러에 대비하기 위해 IP주소와 포트 번호의 바이트 정렬 방식은 빅 엔디안으로 통일해 사용한다. 네트워크 용어로는 빅 엔디안을 네트워크 바이트 정렬이라 부른다.

### ntoh*()

ntoh*()함수는 호스트 바이트 정렬로 저장된 값을 입력으로 받아서 네트워크 바이트 정렬로 반환한 값을 리턴한다. *s()함수는 16비트값을. *l()함수는 32비트 값을 입력으로 받으므로 데이터의 크기에 따라 적합한 버전을 선택해서 사용하면 된다.

일반적으로 ntoh*()함수는 응용 프로그램이 소켓 함수에 데이터를 넘겨주기 전에 호출하며 소켓 함수가 결과로 리턴한 데이터를 응용 프로그램이 출력 등의 목적으로 사용하기 전에 호출한다.

~~~c++
#pragma comment(lib, "ws2_32")
#include <winsock2.h>
#include <stdio.h>

int main(int argc, char *argv[])
{
	WSADATA wsa;
	if(WSAStartup(MAKEWORD(2, 2), &wsa) != 0)
		return 1;

	u_short x1 = 0x1234;
	u_long  y1 = 0x12345678;
	u_short x2;
	u_long  y2;

	// 호스트 바이트 -> 네트워크 바이트
	printf("[호스트 바이트 -> 네트워크 바이트]\n");
	printf("0x%x -> 0x%x\n", x1, x2 = htons(x1));
	printf("0x%x -> 0x%x\n", y1, y2 = htonl(y1));

	// 네트워크 바이트 -> 호스트 바이트
	printf("\n[네트워크 바이트 -> 호스트 바이트]\n");
	printf("0x%x -> 0x%x\n", x2, ntohs(x2));
	printf("0x%x -> 0x%x\n", y2, ntohl(y2));

	// 잘못된 사용 예
	printf("\n[잘못된 사용 예]\n");
	printf("0x%x -> 0x%x\n", x1, htonl(x1));

	WSACleanup();
	return 0;
}
~~~

## IP주소 변환 함수

응용프로그램에서 IP주소를 편리하게 변환할 수 있도록 inet_addr()함수가 제공된다. 문자열 형태로 IPv4주소를 입력받아 32비트(네트워크 바이트 정렬) 숫자로 리턴한다. inet_ntoa()함수는 32비트 숫자로 IPv4주소를 입력받아 문자열 형태로 리턴한다.

~~~c++
#pragma comment(lib, "ws2_32")
#include <winsock2.h>
#include <ws2tcpip.h>
#include <stdio.h>

int main(int argc, char *argv[])
{
	WSADATA wsa;
	if(WSAStartup(MAKEWORD(2, 2), &wsa) != 0)
		return 1;

	/*----------------*/
	/* IPv4 변환 연습 */
	/*----------------*/
	// 원래의 IPv4 주소 출력
	char *ipv4test = "147.46.114.70";
	printf("IPv4 주소(변환 전) = %s\n", ipv4test);

	// inet_addr() 함수 연습
	printf("IPv4 주소(변환 후) = 0x%x\n", inet_addr(ipv4test));

	// inet_ntoa() 함수 연습
	IN_ADDR ipv4num;
	ipv4num.s_addr = inet_addr(ipv4test);
	printf("IPv4 주소(다시 변환 후) = %s\n", inet_ntoa(ipv4num));

	printf("\n");

	/*----------------*/
	/* IPv6 변환 연습 */
	/*----------------*/
	// 원래의 IPv6 주소 출력
	char *ipv6test = "2001:0230:abcd:ffab:0023:eb00:ffff:1111";
	printf("IPv6 주소(변환 전) = %s\n", ipv6test);

	// WSAStringToAddress() 함수 연습
	SOCKADDR_IN6 ipv6num;
	int addrlen = sizeof(ipv6num);
	WSAStringToAddress(ipv6test, AF_INET6, NULL,(SOCKADDR *)&ipv6num, &addrlen);
	printf("IPv6 주소(변환 후) = 0x");
	for(int i=0; i<16; i++)
		printf("%02x", ipv6num.sin6_addr.u.Byte[i]);
	printf("\n");

	// WSAAddressToString() 함수 연습
	char ipaddr[50];
	DWORD ipaddrlen = sizeof(ipaddr);
	WSAAddressToString((SOCKADDR *)&ipv6num, sizeof(ipv6num),
		NULL, ipaddr, &ipaddrlen);
	printf("IPv6 주소(다시 변환 후) = %s\n", ipaddr);

	WSACleanup();
	return 0;
}
~~~

## 도메인 이름 시스템과 이름 변환 함수

도메인 이름은 IP주소와 마찬가지로 호스트나 라우터의 고유한 식별자인데, www.naver.com과 같이 숫자보다는 문자로 구성되어 있다. IP주소보다 기억하고 사용하기 쉽다는 장점이 있다.

TCP/IP 프로토콜은 내부적으로 숫자 형태의 IP 주소를 기반으로 동작하므로 사용자가 입력한 도메인 이름은 반드시 IP주소로 변환해야 한다.

도메인 이읆과 IP주소의 변환 정보는 인터넷에 존재하는 여러 도메인 이름 서버(DNS Server : Domain Name Server System Server)가 관리하며, 어느 한 도메인 이름 서가 모든 정보를 갖고 있지 않는다는 점에서 일종의 분산 데이터베이스라고 할 수 있다.

응용 프로그램이 도메인 이름과 IP주소를 상호 변환할 수 있도록 gethostbyname()과 gethostbyaddr()함수를 제공한다. 이 두 함수는 모두 hostnet 구조체형 포인터를 리턴하므로, 이 구조체를 먼저 이해해야 한다.

~~~c++
typedef struct hostnet{
    char* h_name;  
    char** h_aliases; 
    short h_addrtype;
    short h_length;
    char** h_addr_list;
    #define h_addr h_addr_list[0]
}HOSTNET;
~~~

h_name : 공식 도메인 이름

h_aliases : 한 호스트가 공식 도메인 이름 외에 다른 이름을 여러개 가질 수 있는데 이를 별명(alias name)이라고 한다. 호스트가 여러 별명을 가진 경우 이 포인터를 따라갈 경우 모든 별명을 얻을 수 있다.

h_addrtype : IP주소 체계를 나타내는 값으로 AF_INET 또는 AF_INET6값이 저장된다.

h_length : IP주소의 길이(byte단위)이며 4(IPv4)또는 6(IPv6)가 저장된다

h_addr_list : 네트워크 바이트 정렬된 IP주소이다. 한 호스트가 여러 IP주소를 가진 경우 이 포인터를 따라가면 모든 IP주소를 얻을 수 있다. 특정 호스트에 접속할 때는 대게 첫 번째 IP주소만 사용하므로 h_addr_list[0]에 접근하는데 매크로를 통해 재정의된 h_addr을 사용하면 편리하다.

~~~c++
#pragma comment(lib, "ws2_32")
#include <winsock2.h>
#include <stdio.h>

#define TESTNAME "www.example.com"

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

// 도메인 이름 -> IPv4 주소
BOOL GetIPAddr(char *name, IN_ADDR *addr)
{
	HOSTENT *ptr = gethostbyname(name);
	if(ptr == NULL){
		err_display("gethostbyname()");
		return FALSE;
	}
	if(ptr->h_addrtype != AF_INET)
		return FALSE;
	memcpy(addr, ptr->h_addr, ptr->h_length);
	return TRUE;
}

// IPv4 주소 -> 도메인 이름
BOOL GetDomainName(IN_ADDR addr, char *name, int namelen)
{
	HOSTENT *ptr = gethostbyaddr((char *)&addr, sizeof(addr), AF_INET);
	if(ptr == NULL){
		err_display("gethostbyaddr()");
		return FALSE;
	}
	if(ptr->h_addrtype != AF_INET)
		return FALSE;
	strncpy(name, ptr->h_name, namelen);
	return TRUE;
}

int main(int argc, char *argv[])
{
	WSADATA wsa;
	if(WSAStartup(MAKEWORD(2, 2), &wsa) != 0)
		return 1;

	printf("도메인 이름(변환 전) = %s\n", TESTNAME);

	// 도메인 이름 -> IP 주소
	IN_ADDR addr;
	if(GetIPAddr(TESTNAME, &addr)){
		// 성공이면 결과 출력
		printf("IP 주소(변환 후) = %s\n", inet_ntoa(addr));
	
		// IP 주소 -> 도메인 이름
		char name[256];
		if(GetDomainName(addr, name, sizeof(name))){
			// 성공이면 결과 출력
			printf("도메인 이름(다시 변환 후) = %s\n", name);
		}
	}

	WSACleanup();
	return 0;
}
~~~