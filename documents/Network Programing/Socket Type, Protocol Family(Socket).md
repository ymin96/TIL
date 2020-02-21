 # Socket Type, Protocol Family

> 소켓의 프로토콜과 그에 따른 데이터 전송 특성을 알아본다.

<br>

### 소켓의 생성

```c
#include <sys/socket.h>

int socket(int domain, int type, int protocol);
> 성공 시 파일 디스크립터, 실패 시 -1 반환
```

* **domain -** 소켓이 사용할 프로토콜 체계(Protocol Family) 정보 전달.
* **type -** 소켓의 데이터 전송방식에 대한 정보 전달.
* **protocol -** 두 컴퓨터간 통신에 사용되는 프로토콜 정보 전달.

소켓의 생성에 사용되는 socket 함수는 위와같은 형식으로 사용 가능하다.

<br>

<br>

### 프로토콜 체계(Protocol Family)

소켓이 통신에 사용하는 프로토콜은 여러 부류로 나뉜다. 그리고 이 socket 함수의 첫 인자로, 생성되는 소켓이 사용할 프로토콜의 부류정보를 전달해야 한다. 이러한 부류정보를 가리켜 `프로토콜 체계`라 하며, 프로토콜 체계의 종류는 다음과 같다.

| 이름     | 프로토콜 체계(Protocol Family)      |
| -------- | ----------------------------------- |
| PF_INET  | IPv4 인터넷 프로토콜 체계           |
| PF_INET6 | IPv6 인터넷 프로토콜 체계           |
| PF_LOCAL | 로컬 통신을 위한 UNIX 프로토콜 체계 |
| p        | Low Level 소켓을 위한 프로토콜 체계 |
| PF_IPX   | IPX 노벨 프로토콜 체계              |

<br>

<br>

### 소켓의 타입(Type)

소켓의 타입이란 소켓의 데이터 전송방식을 의미하는데, 바로 이 정보를 socket 함수의 두 번째 인자로 전달해야 한다. 그래야 생성되는 소켓의 데이터 전송방식을 결정할 수 있기 때문이다. 

대표적인 전송방식 두 가지를 소개하겠다. 

#### 연결지향형 소켓(SOCK_STREAM)

socket 함수의 두 번째 인자로 `SOCK_STREAM`을 전달하면 `연결지향형 소켓`이 생성된다. 연결지향형 소켓의 특징은 다음과 같다. 

* 중간에 데이터가 소멸되지 않고 목적지로 전송된다.
* 전송 순서대로 데이터가 수신된다.
* 전송되는 데이터의 경계(Boundary)가 존재하지 않는다.

여기서 세 번째 특징에 대해 부연설명을 하면, 데이터를 송수신하는 소켓은 내부적으로 버퍼(Buffer), 쉽게 말해서 바이트 배열을 지니고 있다. 그리고 소켓을 통해 전송되는 데이터는 일단 이 배열에 저장된다. 때문에 데이터가 수신되었다고 해서 바로 read함수를 호출해야 하는 것은 아니다. 이 배열에 용량을 초과하지 않는 한, 데이터가 채워진 후에 한번의 read 함수호출을 통해서 데이터 전부를 읽어 들일수도 있고, 반대로 한번의 write 함수호출로 전송된 데이터 전부를 여러 번의 read함수호출을 통해서 읽어 들일수도 있다. 때문에 연결지향형 소켓은 데이터의 경계가 존재하지 않는다고 말하는 것이다.

마지막으로 **연결지향형 소켓은 반드시 1대 1이어야 한다.** 

#### 비 연결지향형 소켓(SOCK_DGRAM)

socket 함수의 두 번째 인자로 `SOCK_DGRAM`을 전달하면 `비 연결지향형 소켓`이 생성된다. 비 연결지향형 소켓의 특징은 다음과 같다.

* 전송된 순서에 상관없이 가장 빠른 전송을 지향한다.
* 전송된 데이터는 손실의 우려가 있고, 파손의 우려가 있다.
* 전송되는 데이터는 경계(Boundary)가 존재한다.
* 한번에 전송할 수 있는 데이터의 크기가 제한된다.

비 연결지향형 소켓은 연결지향형 소켓에 비해 데이터의 전송속도는 빠르나, 데이터의 손실 및 훼손이 발생하지 않음을 보장하지 않는다. 그리고 한번에 전송할 수 있는 데이터의 크기가 제한되며, 데이터의 경계가 존재한다. 이말은 데이터를 전송할 때 두 번의 함수 호출이 수반되었다면, 데이터를 수신할때도 두 번의 함수 호출이 수반되어야 함을 의미한다. 

<br>

<br>

### 프로토콜 최종선택

이제 socket 함수의 세 번째 인자에 대해 살펴보자. 이는 최종적으로 소켓이 사용하게 될 프로토콜 정보를 전달하는 목적으로 존재한다. 근데 좀 이상한 점이 있지 않은가? socket 함수의 첫 번째, 그리고 두 번째 전달인자를 통해서 프로토콜 체계와 소켓의 데이터 전송방식에 대한 정보까지 전달을 하는데 세 번째 인자를 통해서 프로토콜 정보를 추가로 전달하는 이유는 무엇일까?

socket 함수의 첫 번째, 그리고 두 번째 전달인자를 통해서도 충분히 원하는 유형의 소켓을 생성할 수 있다. 따라서 대부분의 경우, 세 번째 인자로 그냥 0을 넘겨줘도 우리가 원하는 소켓을 생성할 수 있다. 그러나 다음과 같은 상황 때문에 세 번째 인자는 필요하다.

**"하나의 프로토콜 체계 안에 데이터의 전송방식이 동일한 프로토콜이 둘 이상 존재하는 경우"**

즉, 소켓의 데이터 전송방식은 같지만, 그 안에서도 프로토콜이 다시 나뉘는 상황이 존재할 수 있다. 그리고 이러한 경우네는 세 번째 인자를 통해서 원하는 프로토콜 정보를 더 구체화해야한다. 

<br>

<br>

### 윈도우의 socket 함수

함수의 이름과 인자로 전달되는 상수의 이름까지도 리눅스와 동일하다. 다만 반환형에서 조금 차이가 나는데, 이에 대한 설명을 하겠다.

```c
#include <winsock2.h>

SOCKET socket(int af, int type, int protocol);
> 성공 시 소켓 핸들, 실패 시 INVALID_SOCKET 반환
```

보다시피 반환형이 SOCKET인데, 이는 정수로 표현되는 소켓의 핸들 값을 저장하기 위해 정의된 자료형의 이름이다. 즉, 위 함수가 반환하는 값은 SOCKET형 변수를 하나 선언해서 저장해야 한다. 사실 변환되는 값이 정수이다 보니, int형 변수를 하나 선언해서 값을 저장해도 된다. 그러나 앞으로의 확장성을 고려해서 SOCKET이라는 이름의 자료형이 정의되었으니 이를 그냥 소켓의 핸들 값 저장에 필요한 자료형으로 인식하는게 좋다.

마찬가지로 오류발생시 반환하는 INVALID_SOCKET도 실제 값은 -1 이지만 그냥 오류발생을 알리는 하나의 상수라고만 기억을 하자.