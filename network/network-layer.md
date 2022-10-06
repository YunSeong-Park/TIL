# Network Layer

## forwarding & routing

- forwawrding: forwarding table를 참고하여 다음 라우터로 packet을 넘기는 것
- routing: forwarding table을 만들어 packet을 어느 링크로 보낼지 결정하는 것

## forwarding table

IP adress의 범위, forwarding link를 매핑하여 가지고 있는 테이블

다른 router들과 data를 공유하며 forwarding table을 생성

longest Prefix Match Forwarding으로 packet을 송신함.

## IP

### IP Datagram Format

packet의 overhead = 40 bytes = 20 bytes of TCP + 20 bytes of IP

- header (20byte)
  - ver
  - head len
  - time to live: router를 하나 지날 떄 마다 -1이 되고 0이 되면 drop
  - upper layer: TCP인지 UDP인지 구분
  - fragment offset
  - source address
  - destination address
  - options: any

## IP Address(IPv4)

IP Address는 host 가 아닌 network interface를 가르킨다. router는 여러 network interface를 가지고 있다.

forwarding table에 모든 IP Address가 하나하나 입력돼 있다면 forwarding table은 아주 커야할 것이다. 따라서 IP Address는 `계층적으로` 설계되었다.

12.34.158.0/24 에서 32bits 중 앞단 (12.34.158)24bits는 Network 주소를 뒤 (0)8bits는 interface 주소를 가르킨다.

/24를 가르키는게 subnet mast이다.
24 = 255.255.255.0

같은 network를 갖는 host는 같은 prefix를 갖기 때문에 forwarding table이 단순해진다.

## Subnets

라우터를 거치지 않고 갈 수 있는 network 단위

따라서 라우터의 각 interface는 각각 다른 subnet에 포함돼있다.

## NAT: network address translation

여러 장비가 하나의 IP를 공유하는 것

router에서 client IP Adress를 변환해서 IP Adress부족을 해결

1. 같은 IP Address를 공유하고자 하는 host들을 NAT에 안쪽에 연결
2. NAT 안쪽에 연결된 host들은 사설 IP 라 불리는 특별히 지정된 범위의 주소를 사용한다. (이 IP Address는 다른 NAT의 host와 중복이 가능함)

## DHCP(Dynamic Host Configureation Protocol)

host는 어떻게 IP Address를 얻는가?

IP Address는 network Address를 포함하기 때문에 network에서 IP Address를 할당 받아야한다.

### DHCP server

- subnet 안에서 각 interface들에게 IP Address를 할당해주는 server
- 하나의 subnet안에 최소 한개 이상의 DHCP server가 있어야한다.
- 보통 GateWay Router에 있다.

#### Host가 DHCP server로 부터 전달받는 데이터

- IP Address
- Gate Way Router address
- Local DNS address

#### broad cast IP Address

- subnet 내부 모든 host에게 packet을 보냄
- IP Address: 255.255.255.255
