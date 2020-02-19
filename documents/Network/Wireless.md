# Wireless

> 무선 통신에 대하여 알아본다.

<br>

### AP(Access Point)

AP는 유선과 무선을 서로 연결해주는 역할을 하는 장비이다. 한쪽은 유선 네트워크로 스위치에 붙어있고, 나머지 한쪽은 무선 랜 카드가 장착된 기기와 무선으로 통신을 한다. 

<br>

### CSMA/CA

무선 랜에서의 통신 방식인 IEEE802.11 즉, CSMA/CA는 Carrier Sense Multiple Access / Collision Avoidance 의 약자이다. 이더넷 방식과 비슷하지만 이더넷의 경우는 CD(Collision Delection)로 부딪히면 다시 보내는 방식인데 반해 무선은 CA(Collision Avoidance)로 충돌을 피하기 위한 대비책을 많이 가지고 있다.

CSMA/CA 통신에서 데이터를 보내는 방식은 다음과 같다.

1. **Listen Air Space(radio wave)**: 즉, 현재 통신이 일어나고 있는지 들어본다
2. **Set Random ware timer before sending frame**: 그리고 바로 데이터를 보내는 것이 아니라 다시 랜덤한 시간 동안 기다리게 된다.
3. **After timer has passed, listen again and send**: 랜덤한 시간이 흐르고 난 후 다시 한 번 더 통신이 일어나고 있는지를 들어 본 후 프레임을 전송한다.
4. **Wait for an Ack**: 하지만 무선 통신의 경우 보낸 데이터가 잘 도착했는지를 알 수 있는 방법이 없으니 보내고 나서는 항상 잘 받았다는 신호(ACK)를 기다린다.
5. **If no Ack, resend the frame(다시 1번으로)**: 만약 정해진 시간 동안에 ACK를 받지 못했다면 전송이 실패한 것으로 생각하고 1번으로 돌아가 다시 전송을 시도하게 된다.

<br>

<br>

 