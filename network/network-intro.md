# Network Intro

[한양대학교 컴퓨터네트워크 강의 정리](http://www.kocw.or.kr/home/cview.do?cid=0458b5381aa336dc)

## Network 구성

### Network Edge

말단의 applications, hosts

### Network Core

메세지를 전달하는 routers

### Communication Links

wifi, 도선등 신호를 전달해주는 Links

## TCP/ UDP

어플리케이션이 서버에 요청을 보낼 때 os 내부에 규현된 TCP/IP 혹은 UDP/IP를 통해 보낸다.
소켓: 어플리에키션과 TCP/IP를 연결하는 인터페이스?
패킷: 네트워크에서 전송되는 단위

### TCP

- 구현상 복잡도가 높다.
- 머신에 오버해드를 많이 준다.
- 메세지를 유실시키지 않는다.

### UDP

- 구현상 복잡도가 낮다.
- 머신에 오버해드를 적게 준다.
- 메세지가 유실될 수 있다.
- DNS, 보이스 송신

## Protocol

- 서로 다른 객체가 의사소통하기 위해 만들어지는 규칙

## Packet

하지만 인터넷 세상은 단발적인 리퀘스트를 여러번 보내는 형태이기 때문에 Circuit Switching이 적합하지 않다. 따라서 보내는 데이터 신호를 일정 단위로 전송한다.

이 단위를 Packet이라고 한다.

### Circuit Switching

resource와 target을 link bandwidth를 모두 점유해서 신호를 한번에 보내는 것

## Packet Switching의 라우터에서 발생하는 딜레이

- processing delay: 다음 라우터를 계산 하는 데 걸리는 시간 (processor의 성능에 반비례)
- queuing delay: 다음 라우터에 대기 하고 있는 신호들이 리졸브 되기 까지 기다리는 시간(사용자들이 몰릴 때 발생)
- transmission delay: 하나의 패킷이 전부 내보내는 시간 (밴드 위스에 반비례)
- propagation delay: 패킷의 마지막 비트가 다음 라우터에 도달하는 데 까지 걸리는 시간 (빛의 속도와 관련)

## Packet loss

queue가 가득 차서 Packet을 버리는 경우가 있다.

패킷이 유실되는 경우는 대부분 queue에서 유실되는 경우이다.

## Retransmission

유실된 패킷을 누가 재전송 할 것인가?

1. original source
2. 직전 라우터

현재 인터넷 디자인은 original source가 재전송한다.

1. router들은 IP 레이어 까지 만 담당하고
2. TCP이상은 엣지에서 담당한다.
