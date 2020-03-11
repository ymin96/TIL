# Windows Thread Synchronize

> 윈도우에서의 쓰레드 동기화 기법에 대해 알아본다.

<br>

### 동기화 기법의 분류와 CRITICAL_SECTION 동기화

<br>

#### 유저모드(User mode)와 커널모드(Kernel mode)

윈도우 운영체제의 연산방식(프로그램 실행방식)을 가리켜 `이중모드 연산(Dual-mode Operation)`방식이라 한다. 이는 연산을 하는데 있어서 윈도우에 두 가지 모드가 존재함을 뜻한다. 그리고 그 두 가지는 다음과 같다.

* **유저모드 -** 응용 프로그램이 실행되는 기본모드로, 물리적인 영역으로의 접근이 허용되지 않으며, 접근할 수 있는 메모리의 영역에도 제한이 따른다.
* **커널모드 -** 운영체제가 실행될 때의 모드로, 메모리뿐만 아니라, 하드웨어의 접근에도 제한이 따르지 않는다. 

쓰레드와 같이 커널 오브젝트의 생성을 동반하는 리소스의 생성을 위해서는 다음 모드 변환의 과정을 기본적으로 거쳐야 한다.

유저모드 > 커널모드 > 유저모드

유저모드에서 커널모드로의 전환은 리소스의 생성을 위한 것이고, 커널모드에서 유저모드로의 재 전환은 응용 프로그램의 나머지 부분을 이어서 실행하기 위한 것이다. 이렇듯 리소스의 생성뿐만 아니라, 커널 오브젝트와 관련된 모든 일은 커널모드에서 진행이 되는데, 모드의 변환은 시스템에 부담이 되는 일이기 때문에, 빈번한 모드의 변환은 성능에 영향을 줄 수 있다.

<br>

#### 유저모드 동기화

유저모드 동기화는 유저모드상에서 진행되는 동기화를 말한다. 즉, 운영체제의 도움 없이 응용 프로그램상에서 진행되는 동기화가 바로 유저모드 동기화이다. 유저모드 동기화의 가장 큰 장점은 다음과 같다.

**"속도가 빠르다"**

하지만 운영체제의 도움을 빌리지 않는 동기화 기법이기 때문에 기능은 제한적이다.

<br>

#### 커널모드 동기화

커널모드의 장점은 다음과 같다.

* 유저모드 동기화에 비해 제공되는 기능이 더 많다.
* Dead-lock에 걸리지 않도록 타임아웃의 지정이 가능하다.

그리고 커널모드 동기화는 커널 오브젝트를 기반으로 하기 때문에 서로 다른 프로세스 사이에서의 동기화도 가능하다. 왜냐하면 커널 오브젝트는 하나의 프로세스에 의해서 소유되는 것이 아니라, 운영체제에 의해서 소유 및 관리되기 때문이다.

<br>

#### CRITICAL_SECTION 기반의 동기화

CRITICAL_SECTION 기반의 동기화에서는 `CRITICAL_SECTION 오브젝트`라는 것을 생성해서 이를 동기화에 활용한다. 참고로 이는 커널 오브젝트가 아니며, 대부분의 다른 동기화 오브젝트와 마찬가지로 이는 임계영역의 진입에 필요한 일종의 Key로 이해할 수 있다. 그럼 이어서 CS오브젝트의 초기화 및 소멸과 관련된 함수를 소개하겠다.

```c
#include <windows.h>

void InitializeCriticalSection(LPCRITICAL_SECTION lpCriticalSection);
void DeleteCriticalSection(LPCRITICAL_SECTION lpCriticalSection);
```

* **lpCriticalSection -** Init... 함수에서는 초기화 할 CRITICAL_SECTION 오브젝트의 주소 값 전달, 반면 Del... 함수에서는 해제할 CRITICAL_SECTION 오브젝트의 주소 값 전달.

참고로 위 함수의 매개변수형인 LPCRITICAL_SECTION은 CRITICAL_SECTION의 포인터 형이다. 그리고 DeleteCriticalSection 함수는 CRITICAL_SECTION 오브젝트를 소멸하는 함수가 아니다. 이 함수는 CRITICAL_SECTION 오브젝트가 사용하던 리소스를 소멸시키는 함수이다. 그럼 이어서 CS 오브젝트의 획득 및 반납에 관련된 함수를 소개하겠다.

```c
#include <windows.h>

void EnterCriticalSection(LPCRITICAL_SECTION lpCriticalSection);
void LeaveCriticalSection(LPCRITICAL_SECTION lpCriticalSection);
```

* **lpCriticalSection -** 획득(소유) 및 반납할 CRITICAL_SECTION 오브젝트의 주소 값 전달.

리눅스에서 나온 뮤텍스처럼 사용하면 된다.

<br>

<br>

### 커널모드 동기화 기법

대표적인 커널모드 동기화 기법에는 Event, Semaphore, Mutex 라는 커널 오브젝트 기반의 동기화가 있다.

<br>

#### Mutex(Mutual Exclusion) 오브젝트 기반 동기화

Mutex 오브젝트 기반의 동기화도 CS 오브젝트 기반의 동기화와 유사하다. 따라서 Mutex 오브젝트 역시 열쇠에 비유해서 이해할 수 있다. 그럼 먼저 Mutex 오브젝트의 생성에 관련된 함수를 소개하겠다.

```c
#include <windows.h>

HANDLE CreateMutex(
	LPSECURITY_ATTRIBUTES lpMutexAttributes, BOOL bInitialOwner, LPCTSTR lpName);
> 성공 시 생성된 Mutex 오브젝트의 핸들, 실패 시 NULL 반환
```

* **lpMutexAttributes -** 보안관련 특성 정보의 전달, 디폴트 보안설정을 위해서 NULL 전달
* **bInitialOwner -** TRUE 전달 시, 생성되는 Mutex 오브젝트는 이 함수를 호출한 쓰레드의 소유자가 되면서 non-signaled 상태가 된다. 반면 FALSE 전달 시, 생성되는 Mutex 오브젝트는 소유자가 존재하지 않으며, signaled 상태로 생성된다.
* **lpName -** Mutex 오브젝트에 이름을 부여할 때 사용된다. NULL을 전달하면 이름없음 Mutex 오브젝트가 생성된다.

Mutex 오브젝트는 소유자가 없는 경우에 signaled 상태가 된다. 따라서 이러한 특성을 이용해서 동기화를 진행한다. 그리고 Mutex는 커널 오브젝트이기 때문에 다음 함수를 통해서 소멸이 이뤄진다.

```c
#include <windows.h>

BOOL CloseHande(HANDLE hObject);
> 성공 시 TRUE, 실패 시 FLASE 반환
```

* **hObject -** 소멸하고자 하는 커널 오브젝트의 핸들 전달.

위 함수는 커널 오브젝트를 소멸하는 함수이기 때문에, 이어서 소개하는 Semaphore 와 Event의 소멸에도 사용된다. 그럼 이어서 Mutex의 획득과 반납에 관한 함수를 소개하겠다. 그런데 여기선 반납에 관련된 함수만 소개하면 될 것 같다. 획득은 이미 소개한 WaitForSingleObject의 함수호출을 통해서 이뤄지기 때문이다.

```c
#include <windows.h>

BOOD ReleaseMutex(HANDLE hMutex);
> 성공 시 TRUE, 실패 시 FALSE 반환
```

* **hMutex -** 반납할, 다시 말해서 소유를 해제할 Mutex 오브젝트의 핸들 전달.

Mutex는 소유되었을 때 non-signaled 상태가 되고, 반납되었을 때(소유되지 않았을 때) signaled 상태가 되는 커널 오브젝트이다. 따라서 Mutex의 소유여부를 확인할 때에는 WaitForSingleObject 함수를 이용할 수 있다. 그런데 Mutex는 WaitForSingleObject 함수가 반환될 때, 자동으로 non-signaled 상태가 되는 'auto-reset 모드' 커널 오브젝트이다. 따라서 WaitForSingleObject 함수가 결과적으로 Mutex를 소유할 때 호출하는 함수가 된다. 그러므로 Mutex 기반의 임계영역 보호을 위한 코드는 다음과 같이 구성된다.

```c
WaitForSingleObejct(hMutex, INFINITY);
// 임계영역의 시작
// .....
// 임계영역의 끝
ReleaseMutex(hMutex);
```

<br>

#### Semaphore 오브젝트 기반 동기화

윈도우의 `Semaphore 오브젝트 기반 동기화` 역시, 리눅스의 세마포어와 유사하다. 둘다 `세마포어 값(Semaphore Value)`이라 불리는 정수를 기반으로 동기화가 이뤄지고, 이 값이 0보다 작아질 수 없다는 특징도 모두 동일하다. 

그럼 바로 Semaphore 오브젝트의 생성에 사용되는 함수를 소개하겠다. 물론 Semaphore 오브젝트의 소멸 역시 앞에서 설명한 `CloseHandle` 함수를 이용해서 진행한다.

```c
#include <windows.h>

HANDLE CreateSemaphore(
	LPSECURITY_ATTRIBUTES lpSemaphoreAttributes, LONG lInitialCount, LONG lMaximumCount,
	LPCTSTR lpName);
> 성공 시 생성된 Semaphore 오브젝트의 핸들, 실패 시 NULL 반환
```

* **lpSemaphoreAttributes -** 보안관련 정보의 전달, 디폴트 보안설정을 위해서 NULL전달.
* **lInitialCount -** 세마포어의 초기 값 지정, 매개변수 lMaximumCount에 전달된 값보다 크면 안되고, 0 이상이어야 한다.
* **lMaximumCount -** 최대 세마포어 값을 지정한다. 1을 전달하면 세마포어 값이 0, 또는 1이 되는 바이너리 세마포어가 구성된다.
* **lpName -** Semaphore 오브젝트에 이름을 부여할 때 사용한다. NULL을 전달하면 이름없는 Semaphore 오브젝트가 생성된다.

세마포어 값이 0인 경우 non-signaled 상태가 되고, 0보다 큰 경우에 signaled 상태가 되는 특성을 이용해서 동기화가 진행된다. 그리고 매개변수 lInitialCount에 0이 전달되면, non-signaled 상태의 Semaphore 오브젝트가 생성된다. 또한 매개변수 lMaximumCount 에 3을 전달하면 세마포어의 최대 값은 3이기 때문에, 세 개의 쓰레드가 동시에 임계영역에 진입하는 유형의 동기화도 가능하다. 그럼 이어서 Semaphore 오브젝트의 반납에 사용되는 함수를 소개하겠다.

```c
#include <windows.h>

BOOL ReleaseSemaphore(HANDLE hSemaphore, LONG lReleaseCount, LPLONG lpPreviousCount);
> 성공 시 TRUE, 실패 시 FALSE 반환
```

* **hSemaphore -** 반납할 Semaphore 오브젝트의 핸들 전달.
* **lReleaseCount -** 반납은 세마포어 값의 증가를 의미하는데, 이 매개변수를 통해서 증가되는 값의 크기를 지정할 수 있다. 그리고 이로 인해서 세마포어의 최대 값을 넘어서게 되면, 값은 증가하지 않고 FLASE가 반환된다.
* **lpPreviousCount -** 변경 이전의 세마포어 값 저장을 위한 변수의 주소 값 전달, 불필요하다면 NULL전달.

Semaphore 오브젝트는 다음의 형태로 임계영역의 보호가 가능하다.

```c
WaitForSingleObject(hSemaphore, INFINITY);
// 임계영역의 시작
// .....
// 임계영역의 끝
ReleaseSemaphore(hSemaphore, 1, NULL);
```

<br>

#### Event 오브젝트 기반 동기화

Event 동기화 오브젝트는 앞서 소개한 동기화 오브젝트과 두드러지는 차이점이 있다. 이는 오브젝트의 생성과정에서, 자동으로 non-signaled 상태로 돌아가는 auto-reset 모드와 그렇지 않은 manual-reset 모드 중 하나를 선택할 수 있다는 점이다.

```c
#include <windows.h>

HANDLE CreateEvent(
	LPSECURITY_ATTRIBUTES lpEventAttributes, BOOL bManualReset, BOOL bInitialState,
	LPCTSTR lpName);
> 성공 시 생성된 Event 오브젝트의 핸들, 실패 시 NULL 반환
```

* **lpEventAttributes -** 보안관련 정보의 전달, 디폴트 보안설정을 위해서 NULL  전달
* **bManualReset -** TRUE 전달 시 manual-reset 모드 Event, FALSE 전달 시 auto-reset 모드 Event 오브젝트 생성
* **bInitialState -** TRUE 전달 시 signaled 상태의 Event 오브젝트 생성, FALSE 전달 시 non-signaled 상태의 Event 오브젝트 생성
* **lpName -** Event 오브젝트에 이름을 부여할 때 사용된다. NULL을 전달하면 이름없는 Event오브젝트가 생성된다.

두 번때 매개변수에 주목하자. 이 곳에 TRUE가 전달되면, manual-reset 모드의 Event 오브젝트가 생성되고, 이렇게 되면 WaitForSingleObject 함수가 반환을 한다고 해서 non-signaled 상태로 되돌려지지 않는다. 따라서 이러한 경우에는 다음 두 함수를 이용해서 명시적으로 오브젝트의 상태를 변경해야 한다.

```c
#include <windows.h>

BOOL ResetEvent(HANDLE hEvent);		// to the non-signaled
BOOL SetEvent(HANDLE hEvent);		// to the signaled
> 성공 시 TRUE, 실패 시 FALSE 반환
```

