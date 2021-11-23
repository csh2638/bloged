---
layout: post
title: "[WINSOCK] 6. 멀티 스레드"
categories: NETWORK
tags: [NETWORK, WINSOCK]
---

## 1. 스레드 기초

### 1. 소켓 응용 프로그램과 멀티스래드

기존의 TCP 서버 클라이언트 통신에서는 서버가 동시에 두 개 이상에 서비스 할 수 없었다. 처음 접속한 클라이언트가 보낸 메세지는 서버가 받을 수 있었지만 이후에 접속한 클라이언트의 메시지는 받을 수 없다. 이 문제를 해결하기 위해서는 다음과 같은 방법이 있다.

1. 서버가 각 클라이언트와 연결해 통신하는 시간을 짧게 줄인다. 
2. 서버에 접속한 각 클라이언트를 스레드를 이용해 독립적으로 처리한다.
3. 소켓 입출력 모델을 사용한다.

서버와 클라이언트의 recv()와 send()의 호출 순서가 서로 맞지 않아 교착상태가 발생하는 상황도 발생할 수 있다(send - send, recv - recv). 
데이터 송수신 부분을 잘 설계해 교착 상태가 발생하지 않게 한다.
이 문제를 해결하기 위해서는 다음과 같은 방법이 있다.

1. 소켓에 타임아웃 옵션을 적용해 소켓 함수 호출 시 작업이 완료되지 않아도 일정 시간 후에 리턴하게 한다.
2. 넌블로킹 소켓을 사용한다.
3. 소켓 입출력 모델을 사용한다.

### 2. 스레드 기본 개념

윈도우 운영체제를 제외한 대부분의 운영체제에서 프로세스는 CPU 시간을 할당받아 실행 중인 프로그램을 일컫는다. 프로그램이 저장 장치에 파일로 존재하는 정적인 개념인 데 반해, 프로세스 코드(CPU 명령), 데이터(전역변수, 정적 변수), 리소스(그림 파일, 사운드 파일 등)를 읽어들여 작업을 수행하는 동적인 개념이다.


반면에 윈도우 운영체제에서는 일반적인 의미의 프로세스 개념을 프로세스와 스레드 두 개로 구분하고 있다.


프로세스는 코드, 데이터, 리소스를 파일에서 읽어들여 윈도운 운영체제가 할당해놓은 메모리 영역에 담고 있는 일종의 컨테이너로 정적인 개념이다.


스레드는 cpu시간을 할당받아 프로세스 메모리 영역에 있는 코드를 수행하고 데이터를 사용하는 동적인 개념이다.
즉 일반 운영체제의 프로세스 = 윈도우 운영체제의 프로세스 + 스레드라고 보면 된다.

응용 프로그램이 CPU를 할당받아 실행하려면 최소 하나 이상의 스레드가 필요하다. 응용 프로그램 실행시 최초로 생성되는 스레드를 주 스레드(primary thread) 또는 메인 스레드라 부르는데 WinManin()또는 main()함수에서 실행을 시작한다. 만약 응용 프로그램에서 주 스레드와 별도로 동시에 수행하고자 하는 작업이 있다면, 스레드를 추가로 생성해 이 스레드가 해당 작업을 수행하게 하면 된다. 많은 윈도우 응용 프로그램이 이렇게 구현되어 있는데 이를 멀티스레드 응용 프로그램이라고 한다.

멀티스레드를 활용하면 사용자는 응용 프로그램 하나가 동시에 여러 작업을 수행하는 것처럼 느끼게 된다.

CPU하나가 스레드 두 개를 동시에 실행할 수 는 없지만 교대로 실행하는 것은 가능하다. 교대로 실행하는 간격이 충분히 짧다면 두 사용자는 두 스레드가 동시에 실행되는 것처럼 느낀다. 이렇게 하며려면 각 스레드의 최종 실행상태를 저장하고 나중에 복원하는 작업을 반복해야 한다. 스레드의 실행 상태란 CPU와 메모리 상태를 말하며, 구체적으로는 CPU레지스터 값과 메모리의 스텍을 의미한다. 하드웨어(cpu)와 소프트웨어(os)의 협동으로 이루어 지는 스레드의 실행 상태의 저장과 복원 작업을 ***컨텍스트 전환(context switch)*** 이라고 하며 이 덕분에 각 스레드는 다른 스레드의 존재와 무관하게 상태를 유지하며 실행할 수 있다.

### 3. 스레드 생성과 종료

프로세스가 생성되면 main()함수를 실행 시작점으로 하는 주 스레드가 자동으로 생성된다. 이때 또 다른 함수인 f()를 실행 시작점으로 하는 스레드를 생성하려면 다음과 같은 정보를 운영체제에 제공해야 한다.

f()함수의 시작 주소
운영체제는 f()함수의 시작 주소를 알아야 한다. c/c++ 프로그램에서는 함수 이름이 곧 그 함수의 시작 주소를 의미한다. f()함수와 같이 스레드 실행 시작점이 되는 함수를 스레드 함수라고 한다.

f()함수 실행 시 사용할 스택의 크기
c/c++ 프로그램의 모든 함수는 실행 중 인자 전달과 변수 할당을 위해 스택이 필요하다. 만약 f()함수를 실행 시작점으로 하는 스레드 두 개를 생성하고자 한다면, 서로 다른 메모리 위치에 스택 두 개를 할당해야 한다. 스레드 실행에 필요한 스택 생성은 운영체제가 자동으로 해주므로 응용 프로그램은 스택 크기만 알려주면 된다.

윈도우에서 스레드를 생성할 때는 CreateThread() API함수를 사용한다. CreateThread()함수는 스레들르 생성한 후 스레드 핸들을 리턴한다. 스레드 핸들은 파일 디스크립터나 소켓 디스크립터와 비슷한 개념으로, 운영체제의 스레드 관련 데이터 구조체를 간접적으로 참조하는 매개체 역할을 한다. 응용 프로그램은 스레드 핸들을 윈도우 API함수에 전달함으로써 다양한 스레드를 제어 할 수 있다.

~~~c++
Handle CreateThread(
    LPSECURITY_ATTRIBUTES lpThreadAttributes, // 1
    SIZE_T dwStackSize, // 2
    LPTHREAD_START_ROUTINE lpStartAddress, // 3 
    LPVOID lpParameter,// 4
    DWORD dwCreationFlags, // 5
    LPDWORD lpThreadId // 6
);
~~~

1. lpThreadAttributes : SECURITY_ATTRIBUTES 구조체를 통해 상속과 보안 디스크립터 정보를 전달. NULL값을 사용하면 문제업시 사용 가능
2. dwStackSize : 스레드에 할당하는 스택 크기(바이트 단위)이다. 0을 사용하면 실행 파일의 헤더에 들어가 있는 기본 크기를 사용하는데 visual c++의 경우 1MB이다.
3. lpStartAddress : 스레드 함수의 시작 주소이다. 다음과 같은 형태로 정의해야 한다.

~~~c++
DWORD WINAPI Threadproc(LPVOID lpParameter)
{
//...
}

~~~

4. lpParameter : 스레드 함수에 전달할 인자다. void형 포인터므로 포인터 크기보다 같거나 작은 데이터 값 또는 주소 형태로 전달하면 된다. 전달할 인자가 없으면 NULL을 사용한다.
5. dwCreationFlags : 스레드 생성을 제어하는 값으로 0 또는 CREATE_SUSPENDED를 사용한다. 0을 사용하면 스레드는 생성 후 곧바로 실행되고 CREATE_SUSPENDED를 사용하면 스레드가 생성되지만 ResumeThread()함수를 호출하기 전까지 실행되지 않는다.
6. lpThreadid : DWORD형 변수를 전달하면 여기에 스레드 ID가 저장된다. 스레드 ID가 필요없으면 NULL값을 사용해도 된다.

윈도우에서 스레드를 종료하는 방법에는 네 가지가 있다.
1. 스레드 함수가 리턴한다.
2. 스레드 함수가 안에서 ExitThread()를 호출한다.
3. 다른 스레드가 TerminateThread()함수를 호출해 스레드를 강제 종료시킨다.
4. 주 스레드가 종료하면 모든 스레드가 종료된다.

주로 1,2 방법을 사용하는 것이 바람직하다.

~~~c++

void ExitThread(
    DWORD dwExitCode // 종료코드
);

BOOL TerminateThread(
    HANDLE hThread,  // 종료할 스레드를 가리키는 핸들
    DWORD dwExitCode // 종료 코드
);

~~~

다음은 스레드를 2개 생성하고 스레드 함수 인자를 통해 값을 전달하는 응용 프로그램이다.

~~~c++

#include <windows.h>
#include <stdio.h>

struct Point3D  // 스레드 함수에 32비트보다 큰 값을 전달하기 위한 구조체 정의
{
	int x, y, z;
};

DWORD WINAPI MyThread(LPVOID arg)
{
	Point3D *pt = (Point3D *)arg; // void형 포인터를 poind3D형 포인터로 변환하여 구조체 멤버 x,y,z에 접근
	while(1){ // 무한 루프로 돌면서 1초마다 스레드ID, x,y,z 출력
		printf("Running MyThread() %d : %d, %d, %d\n",
			GetCurrentThreadId(), pt->x, pt->y, pt->z);  
		Sleep(1000);
	}
	return 0;
}

int main(int argc, char *argv[])
{
	// 첫 번째 스레드 생성
	Point3D pt1 = {10, 20, 30};
	HANDLE hThread1 = CreateThread(NULL, 0, MyThread, &pt1, 0, NULL);
	if(hThread1 == NULL) return 1; // 스레드 생성중 오류 발생시 1 리턴후 종료
	CloseHandle(hThread1); //CloseHandel()함수를 호출해 핸들을 닫아도 스레드가 종료되지는 않는다.

	// 두 번째 스레드 생성
	Point3D pt2 = {40, 50, 60};
	HANDLE hThread2 = CreateThread(NULL, 0, MyThread, &pt2, 0, NULL);
	if(hThread2 == NULL) return 1; // 스레드 생성중 오류 발생시 1 리턴후 종료
	CloseHandle(hThread2); //CloseHandel()함수를 호출해 핸들을 닫아도 스레드가 종료되지는 않는다.

	while(1){
		printf("Running main() %d\n", GetCurrentThreadId()); // 주 스레드, 무한 루프를 돌면서 1초마다 스레드ID 출력. 이 부분이 젒으면 주 스레드가 0을 리턴하게되어 프로세스가 종료된다. 즉 모든 스레드가 자동적으로 종료된다.
		Sleep(1000);
	}

	return 0;
}

~~~

![image](https://user-images.githubusercontent.com/78485996/142339918-3282fa66-14b8-4427-b9cc-0b6d0663fb47.png)

실행 결과는 다음과 같다. 첫번째 스레드 값인 10,20,30이 먼저 출력되고 이후에 두번째 스레드 값인 40,50,60이 출력된다. 이후에 순서가 바뀌는데 이는 스레드의 실행 순서가 시스템에 존재하는 다른 스레드의 영향을 받기 때문이다.

### 4. 스레드 제어

스레드는 윈도우 운영체제의 실행 단위므로, 우선순위를 변경하거나 실행을 중지하고 재시작하는 등의 제어 기능을 윈도우 API 수준에서 지원한다.


스레드 우선순위 변경하기

윈도우 운영체제에서는 항상 여러 스레드가 CPU 시간을 사용하려고 경쟁한다. 따라서 각 스레드에 CPU 시간을 적절히 분배하기 위한 정책을 사용하는데 이를 스레드 스케줄링 또는 CPU 스케줄링이라고 한다. 윈도우 운영체제의 스케줄링 기법은 우선순위에 기반한 것으로 우선순위가 높은 스레드에 우선적으로 cpu시간을 할당한다. 스레드의 우선순위를 결정하는 요소는 다음과 같다.

프로세스 우선순위 : 우선순위 클래스(priority class)라 부른다.
스레드 우선순위 : 우선순위 레벨(priority class)라 부른다.

우선순위 클래스는 프로세스 속성으로, 한 프로세스가 생성한 스레드는 우선순위 클래스가 모두 같다는 특징이 있다. 윈도우 운영체제에서 제공하는 우선순위 클래스는 다음과 같다.

~~~
우선순위 클래스

REALTIME_PRIORITY_CLASS(실시간)
HIGH_PRIORITY_CLASS(높음)
ABOVE_NORMAL_PRIORITY_CLASS(높은 우선순위)
NORMAL_PRIORITY_CLASS(보통)
BELOW_NORMAL_PRIORITY_CLASS(낮은 우선순위)
IDLE_PRIORITY_CLASS(낮음)
~~~

우선순위 레벨은 스레드 속성으로, 같은 프로세스에 속한 스레드 간 상대적인 우선순위를 결정할 때 사용한다. 윈도우 운영체제에서 제공하는 우선순위 레벨은 다음과 같다.

~~~
우선순위 레벨

THREAD_PRIORITY_TIME_CRITICAL
THREAD_PRIORITY_HIGHEST
THREAD_PRIORITY_ABOVE_NORMAL
THREAD_PRIORITY_NORMAL
THREAD_PRIORITY_BELLOW_NORMAL
THREAD_PRIORITY_LOWEST
THREAD_PRIORITY_IDLE
~~~

우선순위 클래스와 우선순위 레벨을 결합하면 스레드의 기본 우선순위(base priority)가 결정되고, 이 값이 스레드 스케줄링에 이용된다. 윈도웅 스케줄링 방식에서는 우선순위가 가장 높은 스레드에 cpu를 할당하되, 우선순위가 같은 스레드가 여러개 있을 때는 cpu 시간을 번갈아가며 할당한다.

이런 방식에서는 우선순위가 높은 스레드가 계속 cpu시간을 요구하면 우선순위가 낮은 스레드는 cpu시간을 전혀 할당받지 못하는 문제가 생기는데 이를 기아(starvation)이라고 한다. 기아 문제를 해결하려고 윈도우 운영체제는 오랜 시간 cpu시간을 할당받지 못한 스레드의 우선순위를 단계적으로 끌어올려서 우선순위가 낮은 스레드도 궁극적으로 cpu를 사용할 수 있게 한다. 또한 현재 사용자가 작업하고 있는 프로그램의 반응 속도를 빠르게 하려고 동적으로 변경하기도 한다. 예를 들어 같은 프로그램이 두 개 실행중일때 사용자가 작업하고 있는 포그라운드(foreground)프로그램의 스레드가 더 높은 우선순위를 받는다.


멀티스레드를 이용할 때 작업의 중요도에 따라 응용 프로그램이 직접 우선순위를 변경하기도 한다. 이때 우선순위 클래스를 변경하는 경우는 흔치 않으며, 대게는 우선순위 레벨을 변경한다. 우선순위 레벨 관련 API함수는 다음과 같다. SetThreadPriority()함수는 우선순위 레벨을 변경할 때, GetThreadPriority()함수는 우선순위 레벨을 얻을 때 사용한다.

~~~c++

BOOL SetThreadPriority(
    Handle hThread, // 스레드 핸들
    int nPriority // 우선순위 레벨
);

~~~

주 스레드 외에 새로운 스레드를 cpu 개수만큼 생성하고 우선순위 레벨을 변경하는 응용 프로그램.

~~~c++

#include <windows.h>
#include <stdio.h>

DWORD WINAPI MyThread(LPVOID arg)
{
	while(1);
	return 0;
}

int main()
{
	// CPU 개수를 알아낸다.
	SYSTEM_INFO si;
	GetSystemInfo(&si);

	// CPU 개수만큼 스레드를 생성한다.
	for(int i=0; i<(int)si.dwNumberOfProcessors; i++){
		HANDLE hThread = CreateThread(NULL, 0, MyThread, NULL, 0, NULL);
		if(hThread == NULL) return 1;
		// 최고 우선 순위로 변경한다.
		SetThreadPriority(hThread, THREAD_PRIORITY_TIME_CRITICAL);
		CloseHandle(hThread);
	}

	Sleep(1000);
	while(1) { printf("주 스레드 실행!\n"); break; }

	return 0;
}

~~~

실행 결과는 cpu개수만큼 생성한 MyThread 스레드의 우선순위 레벨을 main 스레드보다 높게 설정하고 무한루프를 돌면서 cpu 시간을 계속 요구하므로, 우선순위가 고정되어 있다면 MyThread 스레드만 cpu시간을 할당받게 된다. 이때 기아(starvation)이 발생할 수 있으나, 윈도우 운영체제에서는 오랜 시간 cpu 시간을 할당받지 못한 스레드의 우선순위를 단계적으로 끌어올리기 때문에 main 스레드도 실행할 기회를 얻는다.


### 스레드 종료 기다리기

스레드는 일단 생성되면 cpu 시간을 사용하려고 다른 스레드와 경쟁하면서 독립적으로 실행된다. 하지만 때로는 한 스레드가 다른 스레드의 종료 여부, 즉 작업 완료 여부를 확인해야 할 때가 생긴다. 이때 WaitForSingleObject()함수를 사용해 특정 스레드가 종료할 때 까지 기다릴 수 있다.

~~~c++

DWORD WaitForSingleObject(
    Handle hHandle //1
    DWORD dwMilliseonds//2
);

~~~

1. hHandle : 종료를 기다릴 대상 스레드를 나타낸다.
2. dwMilliseonds : 대기 시간으로, 밀리초 단위를 사용한다. 이 시간 안에 스레드가 종료되지 않으면 WaitForSingleObject()함수는 리턴하고, 이때 리턴값은 WAIT_TIMEOUT이 된다. 스레드가 종료한 경우에는 WAIT_OBJECT_0을 리턴한다. 대기 시간으로 INFINITE 값을 사용하면 스레드가 종료할 때 까지 무한히 기다린다.

여러 스레드가 종료하기를 기다리려면 WaitForSingleObject()함수를 스레드 개수만큼 호출해야 하는데, 대신 WaitForMultiObjects()함수를 사용하면 호출 한 번을 로 끝낼 수 있다.

~~~c++

DWORD WaitForMultiObjects(
    DWORD nCount, // 1
    const HANDLE *lpHandles,
    BOOL bWaitAll, // 2
    DWORD dwMilliseconds // 3 
);

~~~
 
1. nCount, lpHandles, WaitForMultiObjects()함수를 사용할 때는 스레드 핸들을 배열에 넣어서 전달해야한다. nCount는 배열 원소 개수, lpHandles는 배열의 시작 주소를 나타낸다. nCount의 최댓값은 MAXIMUM_WAIT_OBJECTS(=64)로 정의되어있다.
2. bWaitAll : TRUE면 모든 스레드가 종료할 때까지 기다린다. FALSE면 한 스레드가 종료하는 즉시 리턴한다.
3. dwMilliseconds : 사용법과 의미는 WaitForSingleObject()함수의 두 번째 인자와 같다.

WaitForSingleObject()와 WaitForMultiObjects() 함수는 스레드 종료를 기다리는 전용 함수가 아니라는 점에 주의해야한다. 두 함수는 모두 스레드 동기화에 사용하는 범용 함수로, 여기서는 단지 스레드 종료를 기다리는 목적으로 이용한 것 뿐이다.

### 스레드 실행 중지와 재시작하기

스레드 핸들을 보유하고 있으면 SuspendThread() 함수를 호출해 해당 스레드 실행을 일시 중지하거나 ResumeThread()함수를 호출해 해당 스레드 실행을 일시 중지하거나 ResumeThread()함수를 호출해 재시작할 수 있다. 윈도우 운영체제는 스레드의 중지 횟수를 관리하는데, 이 값은 SuspendThread() 함수를 호출할때 마다 1씩 증가하고 ResumeThread()함수를 호출할 때 마다 1씩 감소한다. 중지 횟수가 0보다 크면 스레드는 실행 중지 상태에 있게 된다. 따라서 한 스레드에 대해 SuspendThread()함수를 두 번 호출했다면 ResumeThread()함수를 두 번 호출해야 재시작 할 수 있다.

비슷한 기능을 제공하는것으로 sleep()함수가 있다. SuspendThread()함수를 호출한 경우에는 반드시 ResumeThread()함수를 사용해야 스레드가 재시작하지만, sleep()함수를 호출하면 dwMilliseconds로 지정한 시간이 지나면 재시작한다는 차이가 있다.

~~~c++

void Sleep(
	DWORD dwMilliseconds //밀리초(ms)
)

~~~

스레드를 이용해 1부터 100까지의 합을 구하는 예제 프로그램.


~~~c++

#include <windows.h>
#include <stdio.h>

int sum = 0;

DWORD WINAPI MyThread(LPVOID arg)
{
	int num = (int)arg;
	for(int i=1; i<=num; i++) sum += i;
	return 0;
}

int main(int argc, char *argv[])
{
	int num = 100;
	HANDLE hThread = CreateThread(NULL, 0, MyThread, 
		(LPVOID)num, CREATE_SUSPENDED, NULL);
	if(hThread == NULL) return 1;

	printf("스레드 실행 전. 계산 결과 = %d\n", sum);
	ResumeThread(hThread);
	WaitForSingleObject(hThread, INFINITE);
	printf("스레드 실행 후. 계산 결과 = %d\n", sum);
	CloseHandle(hThread);
	
	return 0;
}

~~~


## 2. 멀티스레드 TCP 서버

멀티스레드 TCP 서버의 기본 형태

~~~c++

DWORD WINAPI ProcessClient(LPVOID arg)
{
	//3 전달된 소켓 저장
	SOCKET client_sock = (SOCKET)arg;

	//4 클라이언트 정보 얻기
	addrlen = sizeof(clientaddr);
	getpeername(client_sock, (SOCKADDR *)&clientaddr, &addrlen);

	//5 클라이언트 정보 얻기
	while(1){

	}
}
int main(int argc, char *argv[])
{
	while(1)
	{
		//1 클라이언트 접속 수용
		client_sock = accept(listen.sock....);

		//2 스레드 생성
		CreateThread(NULL, 0, ProcessClient, (LPVOID)client_sock, 0, NULL);

	}
}

~~~

1. 클라이언트가 접속하면 accept() 함수는 클라이언트와 통신할 수 있는 소켓을 리턴한다.
2. 클라이언트와 통신을 담당할 스레드를 생성한다. 이때 스레드 함수에 소켓을 넘겨준다.
3. 스레드 함수는 인자로 전달된 소켓을 SOCKET타입으로 형변환(casting)하여 저장해둔다.
4. getpeername()함수를 호출해 클라이언트의 IP주소와 포트 번호를 얻는다. 이 코스는 필수는 아니며 클라이언트 정보 출력을 원할때만 필요하다.
5. 클라이언트와 데이터를 주고 받는다.

스레드 함수에 소켓만 전달한 경우에는 별도에 주소 정보가 없으므로, 소켓을 통해 주소 정보를 얻는 기능이 필요하다. 이런 경우를 위해 다음 두 소켓 함수가 준비되어 있다.

~~~c++

int getpeername(
	SOCKET s,
	struct sockaddr *name
	int *namelen
);
	
~~~


~~~c++

int getsockname(
	SOCKET s,
	struct sockaddr *name,
	int *namelen
)

~~~

getpeername() 함수는 소켓 데이터 구조체에 저장된 원격 ip주소와 원격 포트번호를 리턴하고, getsockname()함수는 지역 IP주소와 지역 포트번호를 리턴한다. 두 함수 모두 첫 번째 인자로 소켓, 두 번째 인자로 소켓 주소 구조체, 세 번째 인자로 소켓 주소 구조체의 크기를 전다랗면 된다. 세 번째 인자는 값-결과 인자(value-result argument)므로 함수 호출전에 초기화를 해줘야 한다.

TCPServer에 멀티스레트 기법을 적용하여, 접속한 클라이언트마다 스레드를 하나씩 생성해 처리하는 프로그램

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

// 클라이언트와 데이터 통신
DWORD WINAPI ProcessClient(LPVOID arg)
{
	SOCKET client_sock = (SOCKET)arg;
	int retval;
	SOCKADDR_IN clientaddr;
	int addrlen;
	char buf[BUFSIZE+1];

	// 클라이언트 정보 얻기
	addrlen = sizeof(clientaddr);
	getpeername(client_sock, (SOCKADDR *)&clientaddr, &addrlen);

	while(1){
		// 데이터 받기
		retval = recv(client_sock, buf, BUFSIZE, 0);
		if(retval == SOCKET_ERROR){
			err_display("recv()");
			break;
		}
		else if(retval == 0)
			break;

		// 받은 데이터 출력
		buf[retval] = '\0';
		printf("[TCP/%s:%d] %s\n", inet_ntoa(clientaddr.sin_addr),
			ntohs(clientaddr.sin_port), buf);

		// 데이터 보내기
		retval = send(client_sock, buf, retval, 0);
		if(retval == SOCKET_ERROR){
			err_display("send()");
			break;
		}
	}

	// closesocket()
	closesocket(client_sock);
	printf("[TCP 서버] 클라이언트 종료: IP 주소=%s, 포트 번호=%d\n",
		inet_ntoa(clientaddr.sin_addr), ntohs(clientaddr.sin_port));

	return 0;
}

int main(int argc, char *argv[])
{
	int retval;

	// 윈속 초기화
	WSADATA wsa;
	if(WSAStartup(MAKEWORD(2,2), &wsa) != 0)
		return 1;

	// socket()
	SOCKET listen_sock = socket(AF_INET, SOCK_STREAM, 0);
	if(listen_sock == INVALID_SOCKET) err_quit("socket()");

	// bind()
	SOCKADDR_IN serveraddr;
	ZeroMemory(&serveraddr, sizeof(serveraddr));
	serveraddr.sin_family = AF_INET;
	serveraddr.sin_addr.s_addr = htonl(INADDR_ANY);
	serveraddr.sin_port = htons(SERVERPORT);
	retval = bind(listen_sock, (SOCKADDR *)&serveraddr, sizeof(serveraddr));
	if(retval == SOCKET_ERROR) err_quit("bind()");

	// listen()
	retval = listen(listen_sock, SOMAXCONN);
	if(retval == SOCKET_ERROR) err_quit("listen()");

	// 데이터 통신에 사용할 변수
	SOCKET client_sock;
	SOCKADDR_IN clientaddr;
	int addrlen;
	HANDLE hThread;

	while(1){
		// accept()
		addrlen = sizeof(clientaddr);
		client_sock = accept(listen_sock, (SOCKADDR *)&clientaddr, &addrlen);
		if(client_sock == INVALID_SOCKET){
			err_display("accept()");
			break;
		}

		// 접속한 클라이언트 정보 출력
		printf("\n[TCP 서버] 클라이언트 접속: IP 주소=%s, 포트 번호=%d\n",
			inet_ntoa(clientaddr.sin_addr), ntohs(clientaddr.sin_port));

		// 스레드 생성
		hThread = CreateThread(NULL, 0, ProcessClient,
			(LPVOID)client_sock, 0, NULL);
		if(hThread == NULL) { closesocket(client_sock); }
		else { CloseHandle(hThread); }
	}

	// closesocket()
	closesocket(listen_sock);

	// 윈속 종료
	WSACleanup();
	return 0;
}

~~~



## 3. 스레드 동기화

### 1. 스레드 동기화 필요성

멀티스레드를 이용하는 프로그램에서 스레드 두 개 이상이 공유데이터에 접근할때 발생할 수 있다. 이때 발생할 수 있는 문제들을 해결하기 위한 일련의 작업을 스레드 동기화라 한다. 윈도우 운영체제는 프로그래머가 상황에 따라 적절한 동기화 기법을 선택할 수 있도록 다양한 API함수를 제공한다.

1. 임계영역(critical section) : 공유 자원에 대해 오직 한 스레드에 접근만 허용한다.(한 프로세스에 속한 스레드 간에 사용 가능)
2. 뮤텍스(mutex) : 공유자원에 대해 오직 한 스레드의 접근만 허용한다.(서로 다른 프로세스에 속한 스레드 간에도 사용 가능)
3. 이벤트(event) : 사건 발생을 알려 대기중인 스레드를 깨운다.
4. 세마포어(semaphore) : 한정된 개수의 자원에 여러 스레드가 접근할 때 자원을 사용할 수 있는 스레드 개수를 제한한다.
5. 대기 가능 타이머(waitable timer) : 정해진 시간이 되면 대기 중인 스레드를 깨운다.

### 2. 스레드 동기화 기본 개념

스레드 동기화가 필요한 경우는 크게 다음 두 경우다.

1. 둘 이상의 스레드가 공유 자원에 접근한다.
2. 한 스레드가 작업을 완료한 후, 기다리는 다른 스레드에 알려준다.

두 경우 모두 각 스레드가 독립적으로 실행하지 않고 다른 스레드와의 상호 작용을 토대로 자신의 작업을 진행한다는 특징이 있다. 스레드 동기화를 하려면 스레드가 상호 작용해야하므로 스레드간의 매개체가 필용하다. 두 스레드가 동시에 진행하면 안 되는 상황이 있을때, 두 스레드는 매개체를 통해 진행 가능 여부를 판단하고 이에 근거해 자신의 실행을 계속할지를 결정한다.

윈도우 운영체제에서 이러한 매개체 역할을 할 수 있는 것들을 통틀어 동기화 객체(synchronization object)라 한다. 동기화 객체의 특징을 요약하면 다음과 같다.

1. Create()함수를 호출하면 커널(kernel : 운영체제의 핵심 부분을 뜻함) 메모리 영역에 동기화 객체가 생성됭고, 이에 접근할 수 있는 핸들(HANDELE 타입)이 리턴된다.
2. 평소에는 비신호 상태(non-signaled state)로 있다가 특정 조건이 만족되면 신호 상태(signaled state)가 된다. 비신호 상태에서 신호 상태로 변화 여부는 Wait*()함수를 사용해야 감지할 수 있다.
3. 사용이 끝나면 CloseHandle()함수로 호출한다.

Wait*()함수는 동기화를 위한 필수 함수로, 자주 사용하는 WaitForSingleObject()와 WaitForMultipleObjects()함수는 1절에서 이미 학습했다. 동기화 객체를 학습할 때는 비신호 > 신호, 신호 > 비신호 상태 변화 조건을 잘 이해해야 하며, 상황에 맞게 Wait*()함수를 사용할 수 있도록 연습해야 한다.

### 3. 임계영역

임계영역(critical section)은 둘 이상의 스레드가 공유 자원에 접근할 때, 오직 한 스레드만 접근을 허용해야 하는 경우에 사용한다. 임계 영역은 대표적인 스레드 동기화 기법이지만, 생성과 사용법이 달라서 소개한 동기화 객체로 분류하지는 않는다. 대표적인 특징을 정리하면 다음과 같다.

- 임계영역은 일반 동기화 객체와 달리 개별 프로세스의 유저(user) 메모리 영역에 존재하는 단순한 구조체다. 따라서 다른 프로세스가 접근할 수 없으므로 한 프로세스에 속한 스레드간 동기화에만 사용한다.
- 일반 동기화 객체보다 빠르고 효율적이다.

임계영역 예제

~~~c++

#include <windows.h>

CRITICAL_SECTION cs; // 1

DWORD WINAPI MyThread1(LPVOID arg){
	EnterCriticalSection(&cs); // 3
	//공유자원 접근
	LeaveCriticalSection(&cs); // 4
}
DWORD WINAPI MyThread2(LPVOID arg){
	EnterCriticalSection(&cs); // 3
	//공유자원 접근
	LeaveCriticalSection(&cs); // 4
}
int main(int argc, char *argv[])
{
	InitializerCriticalSection(&cs); // 2
	// 스레드를 두 개 이상 생성해 작업을 진행한다.
	// 생성한 모든 스레드가 종료될 때 까지 기다린다.
	DeleteCriticalSection(&cs) // 5
}

~~~

1. CRITICAL_SECTION 구조체 변수를 전역변수로 선안한다. 일반 동기화 객체는  Create*()함수를 호출해 커널 메모리 영역에 생성하지만, 임계 영역은 유저 메모리 영역에(대게는 전역 변수 형태로) 생성한다.
2. 임계 영역을 사용하기 전에 InitializeCriticalSection()함수를 호출해 초기화한다.
3. 공유 자원에 접근하기 전에 EnterCriticalSection()함수를 호출한다. 공유 자원을 사용하고 있는 스레드가 없다면 EnterCriticalSection()함수는 곧바로 리턴한다. 하지만 공유 자원을 사용하고 있는 스레드가 있다면 EnterCriticalSection()함수는 리턴하지 못하고 스레드는 대기상태가 된다.
4. 공유 자원을 사용을 마치면 LeaveCriticalSection() 함수를 호출한다.이 때 EnterCriticalSection()함수에서 대기중인 스레드가 있다면 하나만 선택되어 깨어난다.
5. 임계 영역을 사용하는 모든 스레드가 종료하면 DeleteCriticalSection() 함수를 호출해 삭제한다.

임계 영역을 사용하지 않을경우 생길 수 있는 문제 예제

~~~c++

#include <windows.h>
#include <stdio.h>

#define MAXCNT 100000000
int g_count = 0;
CRITICAL_SECTION cs;

DWORD WINAPI MyThread1(LPVOID arg)
{
	for(int i=0; i<MAXCNT; i++){
		EnterCriticalSection(&cs);
		g_count+=2;
		LeaveCriticalSection(&cs);
	}
	return 0;
}

DWORD WINAPI MyThread2(LPVOID arg)
{
	for(int i=0; i<MAXCNT; i++){
		EnterCriticalSection(&cs);
		g_count-=2;
		LeaveCriticalSection(&cs);
	}
	return 0;
}

int main(int argc, char *argv[])
{
	// 임계 영역 초기화
	InitializeCriticalSection(&cs);
	// 두 개의 스레드 생성
	HANDLE hThread[2];
	hThread[0] = CreateThread(NULL, 0, MyThread1, NULL, 0, NULL);
	hThread[1] = CreateThread(NULL, 0, MyThread2, NULL, 0, NULL);
	// 두 개의 스레드 종료 대기
	WaitForMultipleObjects(2, hThread, TRUE, INFINITE);
	// 임계 영역 삭제
	DeleteCriticalSection(&cs);
	// 결과 출력
	printf("g_count = %d\n", g_count);	
	return 0;
}

~~~


### 4. 이벤트

이벤트(Event)는 사건 발생을 다른 스레드에 알리는 동기화 기법이다. 예를 들면 한 스레드가 작업을 완료한 후 기다리고 있는 다른 스레드에게 알릴 때 사용할 수 있다.

이벤트를 사용하는 절차는 다음과 같다.

1. 이벤트를 비신호 상태로 생성한다.
2. 한 스레드가 작업을 진행하고, 나머지 스레드는 이벤트에 대해 Wait*()함수를 호출해 이벤트가 신호 상태가 될 때까지 대기한다(sleep).
3. 스레드가 작업을 완료하면 이벤트를 신호 상태로 바꾼다.
4. 기다리고 있던 스레드 중 하나 혹은 전부가 깨어난다.(wakeup)

이벤트는 대표적인 동기화 객체로, 신호와 비신화 두 가지 상태를 가진다. 또한 상태를 변경할 수 있도록 다음과 같은 함수가 제공된다.

~~~c++

BOOL SetEvent(HANDLE nEvent); // 비신호 상태 > 신호상태
BOOL ResetEvent(HANDLE nEvent); // 신호 상태 > 비신호상태

~~~

이벤트는 특성에 따라 다음 두 종류가 있으므로, 용도에 맞게 선택할 수 있어야 한다.

- 자동리셋(auto-reset)이벤트 : 이벤트를 신호 상태로 바꾸면, 기다리는 스레드 중 하나만 깨운 후 자동으로 비신호 상태가 된다. 따라서 자동 리셋 이벤트에 대해서는 ResetEvent()함수를 사용할 필요가 있다.
- 수동리셋(manual-reset)이벤트 : 이벤트를 신호 상태로 바꾸면, 기다리는 스레드를 모두 깨운 후 계속 신호 상태를 유지한다. 자동 리셋 이벤트와 달리 비신호 상태로 바꾸려면 명시적으로 ResetEvent()함수를 호출해야 한다.

이벤트 생성 함수는 다음과 같다.

~~~c++

HANDLE CreateEvent(
	LPSECURITY_ATTRIBUTES lpEventAttributes, // 1
	BOOL bManual Reset, // 2
	BOOL bInitialState, //3
	LPCTSTR lpName //4
);

~~~

1. lpEventAttributes : 핸들 상속(handle inheritance)과 보안 디스크립터(security descpriptor)관련 구조체로, 대부분은 기본값인 NULL을 사용하면 된다.
2. bManual Reset : TRUE면 수동 리셋 FALSE면 자동 리셋 이벤트가 된다
3. bInitialState : TRUE면 신호 FALSE면 비신호 상태로 시작한다.
4. lpName : 이벤트에 부여할 이름이다. NULL을 사용하면 이름없는(anonymous) 이벤트가 생성되므로 같은 프로세스에 속한 스레드 간 동기화에만 사용할 수 있다. 서로 다른 프로세스에 속한 스레드 간 동기화를 하려면 같은 이름으로 생성해야 한다. 예를 들어, 프로세스 P1과 프로세스 P2에 각각 속한 두 스레드가 이벤트를 이용한 동기화를 하려면 다음과 같이 CreateEvent()함수를 호출하게 된다. 둘 중 먼저 CreateEvent()함수를 호출한 쪽은 이벤트를 생성(Create)하게 되고, 다른 쪽은 이벤트를 열게(open)된다.

이벤트 사용 예제

~~~c++
#include <windows.h>
#include <stdio.h>

#define BUFSIZE 10

HANDLE hReadEvent;
HANDLE hWriteEvent;
int buf[BUFSIZE];

DWORD WINAPI WriteThread(LPVOID arg)
{
	DWORD retval;

	for(int k=1; k<=500; k++){
		// 읽기 완료 대기
		retval = WaitForSingleObject(hReadEvent, INFINITE);
		if(retval != WAIT_OBJECT_0) break;

		// 공유 버퍼에 데이터 저장
		for(int i=0; i<BUFSIZE; i++)
			buf[i] = k;

		// 쓰기 완료 알림
		SetEvent(hWriteEvent);
	}

	return 0;
}

DWORD WINAPI ReadThread(LPVOID arg)
{
	DWORD retval;

	while(1){
		// 쓰기 완료 대기
		retval = WaitForSingleObject(hWriteEvent, INFINITE);
		if(retval != WAIT_OBJECT_0) break;

		// 읽은 데이터 출력
		printf("Thread %4d: ", GetCurrentThreadId());
		for(int i=0; i<BUFSIZE; i++)
			printf("%3d ", buf[i]);
		printf("\n");
		
		// 버퍼 초기화
		ZeroMemory(buf, sizeof(buf));

		// 읽기 완료 알림
		SetEvent(hReadEvent);
	}

	return 0;
}

int main(int argc, char *argv[])
{
	// 두 개의 자동 리셋 이벤트 생성(각각 비신호, 신호 상태)
	hWriteEvent = CreateEvent(NULL, FALSE, FALSE, NULL);
	if(hWriteEvent == NULL) return 1;
	hReadEvent = CreateEvent(NULL, FALSE, TRUE, NULL);
	if(hReadEvent == NULL) return 1;
	
	// 세 개의 스레드 생성
	HANDLE hThread[3];
	hThread[0] = CreateThread(NULL, 0, WriteThread, NULL, 0, NULL);
	hThread[1] = CreateThread(NULL, 0, ReadThread, NULL, 0, NULL);
	hThread[2] = CreateThread(NULL, 0, ReadThread, NULL, 0, NULL);
	
	// 세 개의 스레드 종료 대기
	WaitForMultipleObjects(3, hThread, TRUE, INFINITE);
	
	// 이벤트 제거
	CloseHandle(hWriteEvent);
	CloseHandle(hReadEvent);
	return 0;
}
~~~



















































































