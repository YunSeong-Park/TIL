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
