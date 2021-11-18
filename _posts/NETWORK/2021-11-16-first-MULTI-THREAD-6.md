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













































































































