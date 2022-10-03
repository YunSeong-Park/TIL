# 전송 계층(Transport Layer)

전송 계층은 OS 내부에 구현돼있다.

application layer(process)로 부터 message를 전달 받아 다른 host의 process로 전달하는 시스템

## Segment

전송 계층의 전송 단위이다.
부가 정보를 담고 있는 Header와 application layer로 부터 전달받은 data를 포한한다.

packet은 segment를 data로 담고 있다.

## Multiplexing/ Demultiplexing

- multiplexing: sender 측) 여러 socket에서 들어온 데이터에 transport header를 더해 Segment를 만드는 것

- demultiplexing: receiver 측) header 정보를 통해 전달받은 segment들은 data를 socket에 배분하는 것

## UDP

UDP 는 Multiplexing/Demultiplexing과 error detection을 수행한다.

header는 기본적으로 어플리케이션에 필요없는 overhead이다. 따라서 이를 최소화하는 방향으로 설계 한다.

- header
  - source port
  - dest port
  - length
  - checksum
- payload(data)

### checksum(error detection)

payload의 내부 데이터가 전송 중 에러가 발생했는지 아닌지 판별할 수 있는 데이터

UDP는 신뢰성을 보장하지 않지만 최소한 에러가 발생한 message를 전달하진 않는다.

## RDT(Reliable Data Transfer) 설계

unreliable data transfer 상황

1. bit error 발생
2. packet 유실

### RDT1.0

- bit error 발생
- receiver 응답(ACK: 정상/ NAK: 비정상)

1. sender가 checksum을 첨부하여 receiver에 데이터 전송
2. receiver가 checksum을 확인하여 bit error가 발생했는지 확인하고 error 발생 시 sender에 이를 알림(ACK/ NAK)
3. sender는 ACK/ NAK 를 확인하여 NAK인 경우 다시 전송

문제: receiver응답에 에러가 발생한 경우 정상/비정상을 어떻게 판단할까?

무조건 data를 다시 송신한다.
receiver는 packet은 sequence number를 가지고 중속 packet인지 판별한다.

### RDT2.0

- bit error 발생
- receiver 응답(ACK: 정상/ NAK: 비정상)

receiver는 ACK인 경우에만 응답하고 NAK인 경우 응답하지 않는다.
sender는 sequence number로 어느 packet에 오류가 발생했는지 알 수 있다.

### RDT3.0

- bit error 발생
- packet 유실 발생
- receiver 응답(ACK: 정상/ NAK: 비정상)

sender에서 ACK 응답을 일정 시간 기다리고 시간이 넘어가면 유실된 것으로 판단 다시 송신한다.

ACK 응답이 유실될 경우 receiver는 중복된 packet을 받는데 이를 버린다.

- 위 예제는 패킷 하나, 하나 보내기 때문에 비효율적이다. 실제 TCP는 pipelining으로 패킷을 한번에 보내고 ACK응답도 한번에 보낸다.

## TCP

### 특징

- reliable, in-order byte stream
- point-to-point: 2개의 host가 통신함.
- pipelined: TCP congestion and flow control set window size
- full duplex data: 양방향 통신, MMS: maximum segment size

### TCP segment structure

TCP header를 줄이면 한번에 담을 수 있는 데이터의 량이 커지기 때문에 header를 최소화하는 것이 중요하다.

- header
  - source port
  - dest port
  - sequence number
  - acknowledgement number
  - checksum
  - window size: receiver가 한번에 받을 수 있는 maximum segment의 크기(flow control)

### reliable transfet

#### send burffer/ receive burffer

각 TCP 통신은 sender/ receiver를 가리지 않기 때문에 TCP는 2개의 burffer를 가진다.

send burffer: sender측에서 송신한 segment들을 가지고 있고 응답 받은 acknowledgement number, window size 등을 참고 송신해야할 segment를 관리 => 안정성 보장

receive burffer: receiver 측에서 받는 데이터를 sequence number에 맞게 정렬하여 들고 있음. => 순서 보장

#### RTT

```
timeoutInterval = RTT + safety margin
```

timeoutInterval: sender 측에서 패킷이 유실됐는지 판단하는 기준 시간
RTT: 데이터가 왕복하는 시간(queueing delay 떄문에 일정하지 않음)

#### Duplicate ACK

중복된 ACK를 3번 받으면 timer가 지나기 전이라도 packet이 유실됐다고 판단하고 다시 데이터를 보낸다.

### flow control

receive buffer의 남은 공간을 sender에게 보내, receiver가 감당할 수 있는 량의 data만 보내도록 하는 것

#### 시나리오

1. sender가 receiver에게 segment를 송신한다.
2. receiver가 segemnt를 받고 이를 receive buffer에 적재한다.
3. receive buffer의 남은 공간을 sender에게 보낸다.

#### corner case

receive buffer가 가득 찼다면?
sender은 최소 단위의 데이터만 담아 주기적으로 송신한다.

### Nagle's algorithm \*\*\*

- 하나의 segment는 500byte 정도의 데이터를 담을 수 있다.
- application layer에서 데이터가 지연되며 넘어올 수 있다.
- 이런 segment에 항상 데이터를 채우기까지 기다린 후 송신해야하나?

아래 두 상황일 때 송신한다.

1. segment에 데이터를 모두 채웠을 떄
2. 이전 segment의 ACK응답을 받았을 떄

결과적으로 app program이 빠를 수록 segment size가 커진다. network 속도가 빠를 수록 segment size가 작아 진다.

### Clark's solution

receive buffer의 남은 공간이 maximum segment size보다 작은 경우 남은 공간을 0으로 보낸다.

### Delay ACK

다음 세그먼트를 일정 시간 기다린 후 ACK을 보낸다.

### Connection Management

데이터를 교환하기 이전에 각 host는 다음과 같은 작업을 마쳐야 한다.

1.  receive queue, sender queue를 생성하여 데이터를 전송/수신할 준비
2.  상대편의 초기 squence number 얻기

#### 3way-handshake

connection을 하기 위해선 총 3회 segment를 교환한다.

각각 상대편의 초기 squence number를 얻고 상대방이 준비됐는지 확인해야한는데 최소 3번의 교환이 필요하다.

1. client가 server에 squence number를 송신
2. server가 client의 squnce number를 확인하고 connection 준비를 마치고 server의 squence number를 송신
3. client가 server의 squence number를 확인 후 connection 준비를 마치고 준비가 됐다는 사실을 server에 알림
