# UDP basics

> UDP의 기초적인 이론을 알아본다.

<br>

### UDP 소켓의 특성

* 신뢰할 수 없는 전송 방법을 제공한다.

UDP는 TCP보다 훨씬 간결한 구조로 설계되어있다. ACK와 같은 응답 메시지를 보내는 일도 없으며, SEQ와 같이 패킷에 번호를 부여하는 일도 없다. 즉, TCP에서 하던 `흐름제어(Flow Control)`가 UDP에는 없다. 때문에 상황에 따라서 TCP보다 훨씬 좋은 성능을 발휘하고, 신뢰성보다 성능이 중요시되는 상황에서는 UDP가 더 좋은 선택이 될 수 있다.

<br>

<br>

### UDP의 내부 동작원리

UDP는 TCP와 달리 흐름제어를 하지 않는다고 이야기 했었다. 그럼 이번에는 UDP의 역할이 어디까지인지 구체적으로 알아보자.

출발지 호스트를 떠난 UDP 패킷이 목적지 호스트 A에게 전달되도록 하는 것은 IP의 역할이다. 이렇게 전달된 UDP 패킷을 목적지 호스트 내에 존재하는 UDP 소켓 중 하나에게 최종 전달하는 것이 바로 UDP의 역할이다. 즉, UDP의 역할 중 가장 중요한 것은 호스트로 수신된 패킷을 PORT 정보를 참조하여 최종 목적지인 UDP 소켓에 전달하는 것이다.

<br>

<br>

### UDP의 효율적 사용

어떤 상황에서 UDP를 쓰는것이 효율적인지 알아보자. 1만개의 패킷을 보냈는데 그 중 1개만 손실 되도 문제가 발생하는 압축파일의 경우에는 반드시 TCP를 기반으로 송수신이 이뤄져야 한다. 그러나 인터넷 기반으로 실시간 영상 및 음성을 전송하는 경우에는 상황이 좀 다르다. 멀티미디어 데이터는 그 특성상 일부가 손실되어도 크게 문제되지 않는다. 하지만 실시산으로 서비스를 해야 하므로 속도가 상당히 중요한 요소가 된다.  이러한 경우가 UDP 기반의 구현을 고려할만한 상황이다.  그러나 UDP가 TCP에 비해 언제나 빠른 속도를 내는 것은 아니다. TCP가 UDP에 비해 느린 이유 두 가지만 들라고 하면 다음 두 가지를 들 수 있다.

* 데이터 송수신 이전, 이후에 거치는 연결설정 및 해체과정
* 데이터 송수신 과정에서 거치는 신뢰정보를 위한 흐름제어

따라서 송수신하는 데이터의 양은 적으면서 잦은 연결이 필요한 경우에는 UDP가 TCP보다 훨씬 효율적이고 빠르게 동작한다.  반대로 송수신하는 데이터의 양이 크고 한번의 연결만 필요로 한다면 TCP는 UDP 못지않은 전송속도를 낼 수 있다.

