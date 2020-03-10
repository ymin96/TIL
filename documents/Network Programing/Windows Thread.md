# Windows Thread

> Windows 에서의 쓰레드 사용방법에 대해 알아본다.

<br>

### 커널 오브젝트(Kernel Objects)

윈도우 기반에서 쓰레드를 이해하기 위해서는 `커널 오브젝트`를 먼저 이해해야 한다. 

<br>

#### 커널 오브젝트란?

프로세스, 쓰레드, 파일, 그리고 앞으로 이야기할 세마포어, 뮤텍스 등, 운영체제가 만드는 리소스(Resource)의 종류는 매우 다양하다. 그리고 이들 대부분은 프로그래머의 요청에 의해서 생성되며, 요청의 방식(요청에 사용되는 함수)도 제 각각이다. 그런데 이러한 차이점에도 불구하고 이들은 다음의 공통점을 지닌다.

**"윈도우 운영체제가 생성해서 관리하는 리소스들이다."**

운영체제에 의해서 생성되는 리소스들은 관리를 목적으로 정보를 기록하기 위해 내부적으로 데이터 블록을 생성한다. 물론 이 데이터 블록의 형태는 리소스마다 차이가 있다. 그리고 이 데이터 블록을 가리켜 **`커널 오브젝트`**라 한다. 

<br>

#### 커널 오브젝트의 소유자

쓰레드, 파일 등의 생성요청이 프로세스 내에서 이뤄지기 때문에, 이 때 생성되는 커널 오브젝트의 소유자는 커널(운영체제)이다. 그리고 소유자가 커널이라는 것은 다음의 의미를 갖는다.

**"커널 오브젝트의 생성, 관리 그리고 소멸시점을 결정하는 것까지 모두 운영체제의 몪이다."**

이렇듯 커널 오브젝트는 생성의 주체도 소유의 주체도 운영체제인 데이터 블록이다.

<br>

<br>

### 윈도우 기반의 쓰레드 생성

위도우에서 쓰레드의 생성에 사용되는 함수를 소개하겠다. 이 함수가 호출되면, 쓰레드가 생성되고, 운영체제는 이의 관리를 위해서 커널 오브젝트도 함께 생성한다. 그리고 마지막으로 이 커널 오브젝트의 구분자 역할을 하는, 정수로 표현되는 **`핸들(Handle)`**을 반환한다. 참고로 핸들은 리눅스의 파일 디스크립터에 비유된다.

```c
#include <windows.h>

HANDLE CreateThread(
	LPSECURITY_ATTRIBUTES lpThreadAttributes,
	SIZE_T dwStackSize,
	LPTHREAD_START_ROUTINE lpStartAddress,
	LPVOID lpParameter,
	DWORD dwCreationFlags,
	LPDWORD lpThreadId
);
> 성공 시 쓰레드 핸들, 실패 시 NULL 반환
```

* **lpThreadAttributes -** 쓰레드의 보안관련 정보전달, 디폴트 보안설정을 위해서 NULL 전달.
* **dwStackSize -** 쓰레드에게 할당할 스택의 크기를 전달, 0을 전달하면 디폴트 크기의 스택 생성
* **lpStartAddress -** 쓰레드의 main 함수정보 전달
* **lpParameter -** 쓰레드의 main 함수호출 시 전달할 인자정보 전달.
* **dwCreationFlags -** 쓰레드 생성 이후의 행동을 결정, 0을 전달하면 생성과 동시에 실행 가능한 상태가 된다.
* **lpThreadId -** 쓰레드의 ID의 저장을 위한 변수의 주소 값 전달.

복잡해 보이지만 실제로 신경 쓸 것은 lpStartAddress와 lpParameter 두 가지 정도이며, 나머지는 0 또는 NULL을 전달하면 된다.

<br>

<br>

### 쓰레드에 안전한 C 표준함수의 호출을 위한 쓰레드 생성

앞서 CreateThread 함수를 소개했는데, 생성된 쓰레드를 통해서 C/C++ 표준함수를 호출하려면 다음함수를 이용해서 쓰레드를 생성해야 한다. 왜냐하면 CreateThread 함수를 통해서 생성되는 쓰레드는 C/C++ 표준함수에 안정적으로 동작하지 않기 때문이다.

```c
#include <process.h>

uintptr_t _beginthreades(
	void *security,
	unsigned stack_size,
	unsigned (*start_address)(void *),
	void *arglist,
	unsigned initflag,
	unsigned *thrdaddr
);
> 성공 시 쓰레드 핸들, 실패 시 0 반환
```

CreateThread 함수와 비교해보면, 매개변수의 수도 같고, 각각의 매개변수가 지니는 의미와 순서까지도 동일하다. 다만 이름이 틀리고 선언된 매개변수의 자료형에서 조금 차이가 있을 뿐이다. 따라서 CreateThread 함수를 대신해서 위 함수를 호출할 때에는 적절히 형변환만 해 주면 된다.

<br>

<br>

### 커널 오브젝트의 두 가지 상태

커널 오브젝트에는 프로그램의 구현에 있어서 특히 더 관심을 둬야 하는 정보에 대해 **`상태(state)`**라는 것을 부여한다. 예를들어 쓰레드의 커널 오브젝트에 있어서의 큰 관심사는 종료여부이다. 때문에 종료된 상태를 가리켜 `signaled 상태` 라 하고, 종료되지 않은 상태를 가리켜 `non-signaled 상태`라 한다.

<br>

#### 커널 오브젝트의 상태, 그리고 확인

운영체제는 프로세스나 쓰레드의 상태를 커널 오브젝트에 기록해 둔다. 그리고 다음과 같이 약속한다.

**"프로세스나 쓰레드가 종료되면 해당 커널 오브젝트를 signaled 상태로 변경해 놓겠다!"**

이 말은 프로세스와 쓰레드의 커널 오브젝트 상태가 초기에는 non-signaled 상태라는 뜻도 된다. 그렇다면 이 상태를 어떻게 표현될까? 간단하게 boolean 형 변수 하나로 표현이 된다. 즉, 커널 오브젝트는 boolean 형 변수 하나를 지니고 있으면서 이벤트가 발생했을때 이 값을 TRUE로 두고, 이 상태를 가리켜 signaled 상태라 하는 것이다. 그리고 이 상태를 얻기 위해서 다음 두 가지 함수가 정의되어 있다.

<br>

#### WaitForSingleObject &#38; WaitForMultipleObjects

먼저 WaitForSingleObject 함수를 소개하겠다. 이는 하나의 커널 오브젝트에 대해서 signaled 상태인지를 확인하기 위해서 호출하는 함수이다.

```c
#include <windows.h>

DWORD WaitForSingleObject(HANDLE hHandle, DWORD dwMilliseconds);
> 성공 시 이벤트 정보, 실패 시 WAIT_FAILED 반환
```

* **hHandle -** 상태확인의 대상이 되는 커널 오브젝트의 핸들을 전달.
* **dwMilliseconds -** 1/1000초 단위로 타임아웃을 지정, 인자로 INFINITE 전달 시, 커널 오브젝트가 signaled 상태가 되기 전에는 반환하지 않는다.
* **반환 값 -** signaled 상태로 인한 반환 시, WAIT_OBJECT_0 반환, 타임아웃으로 인한 반환 시 WAIT_TIMEOUT 반환.

위 함수는 이벤트 발생에 의해서 반환되면, 해당 커널 오브젝트를 다시 non-signaled 상태로 되돌리기도 한다. 그리고 이렇게 다시 non-signaled 상태가 되는 커널 오브젝트를 가리켜 `auto-reset 모드`커널 오브젝트라 하고, 반대의 커널 오브젝트를 가리켜 `manual-reset 모드`커널 오브젝트라 한다. 이어서 소개하는 다음 함수는 위의 함수와 달리 둘 이상의 커널 오브젝트를 대상으로 상태를 확인하는 경우에 호출하는 함수이다.

```c
#include <windows.h>

DWORD WaitForMultipleObjects(
	DWORD nCount, const HANDLE *lpHandles, BOOL bWaitAll, DWORD dwMilliseconds);
> 성공 시 이벤트 정보, 실패 시 WAIT_FAILED 반환
```

* **nCount -** 검사할 커널 오브젝트의 수 전달.
* **lpHandles -** 핸들정보를 담고 있는 배열의 주소 값 전달.
* **bWaitAll -** TRUE 전달 시, 모든 검사대상이 signaled 상태가 되어야 반환, FALSE 전달 시, 검사대상 중 하나라도 signaled 상태가 되면 반환.
* **dwMilliseconds -** 1/1000초 단위로 타임아웃 지정, 인자로 INFINITE 전달 시, 커널 오브젝트가 signaled 상태가 되기 전에는 반환하지 않는다.

