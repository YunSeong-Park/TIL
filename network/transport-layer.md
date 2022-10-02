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
