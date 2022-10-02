# Application Layer

## Processes communicating

### Process

host에 실행 중인 프로그램

### inter-process-communication

같은 host에 있는 두 process의 커뮤니케이션은 OS에 정의된 inter-process-communication을 이용한다.

### soket

inter-process-communication의 일종, 다른 host에 있는 process와 커뮤니케이션 하고자 한다면 OS에 정의된 soket을 사용해야 한다.

### IP Adress

- 인터넷 상에 있는 머신을 지칭하는 주소

### Port Number

- 머신의 process를 식별하는 번호

## Transport service needs

아래 요소 중 네트워크 단에서 지원되는 것은 Data Integrity 뿐이다 이외의 요소들은 application 단에서 구현해야한다.

### Data Integrity

- 데이터 유실 없이 완전히 전송
- TCP service로 network 단에서 지원

### Timing

- 적은 딜레이로 전달 되는 것

### Security

- 보내는 신호를 다른 사람들이 볼 수 없도록 함

### throughput

- 다른 터미널로 전달되는 단위 시간당 데이터 전송 처리 량

## TCP가 제공하는 기능

### reliable transport

데이터가 유실되지 않고 전달되는 것

### flow control

receiver 의 상태에 따라 보내는 속도를 조절

### congestion constrol

네트워크 상황에 따라 보내느 속도를 조절

## Web & HTTP

- web page는 여러 object로 이루어져 있다.
- object: HTML file, jpeg image ...
- web page는 objects의 referenc를 가지고 있는 기본 HTML-file로 구성된다.
- 각 object는 URL 로 접근할 수 있다.

## HTTP: hypertext transfer protocol

### stateless

request => response 과정이 종료되면 끝
서버는 과거 client 정보를 기억하지 않는다.

### HTTP connections

#### persistent HTTP

하나의 TCP 연결로 다양한 object를 전송하는 것

#### non-persistent HTTP

하나의 object 전송에 하나의 TCP 연결을 사용하는 것

### HTTP request message

- request line
  메소드 토큰, URI 정보를 담고 있음.
- header lines
  주로 client에 대한 정보를 담고 있음.
  HTTP의 header field는 그렇게 중요하지 않다.

### HTTP response message

- status line
- header lines
- data

### User-server state: cookies

http 는 stateless이기 때문에 서버는 이전 연결을 기억하지 않는다.

이를 보완하기 위힌 cookies가 존재한다.

1. client가 server에 처음 request한다.
2. server는 식별자 cookie를 생성해 response에 첨부하여 client에 제공한다.
3. client는 cookie를 가지고 있고 다음 request에 cookie를 첨부한다.
4. servers는 client을 식별하여 제공하는 objects을 변경할 수 있다.

## SMTP(simple mail transfer protocol)

http는 client가 server로부터 데이터를 fetch해 오는 개념 이지만,
SMTP는 sender - sender server - receiver server 로 데이터를 push하는 개념이다.

receiver server - receiver는 receiver가 데이터를 요청해야하기 때문에 다른 프로토콜이 사용된다.(ex POP, IMAP)

## Socket

### 정의

네트워킹 프로그램이란 다른 컴퓨터의 process와 message를 주고 받는 프로그램이다.

소켓은 application이 다른 컴퓨터의 process와 message를 주고 받을 수 있도록 OS에서 제공해주는 interface이다.

### 종류

- TCP socket
- UDP socket

### API

- Creation & setup
- Establishing a Connectio(TCP)
- sending and Receiving Data
- Tearing Down a Connection(TCP)

### TCP Socket example

os에서 제공하는 system call을 호출하여 구성

Server

1. TCP Server가 `socket()` 을 호출해여 socket을 생성
2. `bind()` 호출하여 socket을 특정 port에 바인드
3. `listen()` 호출하여 해당 소켓이 request을 기다린다는 것을 명시(?)
4. `accept()` 을 호출하여 request가 오기 까지 blocking 하여 대기

Client

5.  `soket()` 생성
6.  `connect()` 로 Server의 blocking을 풀고 가상적인 서킷을 생성함(Server 와 three-way handshaking)
7.  `write()` 소켓에 request 정보를 입력하여 request를 보냅

- 왜 Client는 `bind()`로 포트를 바인딩 하지 않는가? Client의 request, Server의 response로 이루어지기 때문

## DNS (Domain Name Service)

Domain Name(`www.naver.com`)을 통해 IP address을 얻어야 서버에 접속이 가능하다.

DNS는 Domain Name과 IP address를 mapping 해준다.

### DNS 구조 필요성

하나의 서버에서 모든 Domain Name과 Ip address를 관리한다면 여러가지 문제가 발생한다.

1. 속도 문제: 전세계의 모든 Domain Name을 저장하면 검색 시간이 걸린다.
2. 안정성 문제: 하나의 서버가 다운 되면 전세계 DNS가 마비된다.

### DNS 구조: 분산화(distributed), 계층화(hierarchy) database

- Root Domain Server: DNS 구조의 root 하위 top-level Domain Server의 IP address를 라우팅해준다.
- top-level Domain Server: `.com`, `.kr`, `.co.kr` 과 같이 본인에게 소속된 도메인 IP address를 라우팅해준다.
- authoritative Domain Server: 네트워크를 가지고 있는 모든 기관은 Authoritative Domain Server를 보유하고 있다. 이 서버에서 본인 내부에 hostName, IP address를 관리한다.

#### Domain과 host

- hanyang.ac.kr: 한양 대학교의 네트워크 이름을 의미함(Domain)
- www.hanyang.ac.kr: 한양 대학교 네트워크에 속한 host의 이름을 의미함

#### DNS records

```ts
type A = {
  name: host name;
  value: IP address;
  type: a;
  TTL: time
}

type NS = {
  name: domain name;
  value: host name; // 해당 도메인의 authoritative domain server의 host name
  type: NS;
  TTL: time
}
```

#### Local Domain Server

내트워크 내에 각각 Local Domain Server를 두어 존재하는 domain name/ host name 을 캐싱해두어 사용함.

ex) 한양대학교 네트워크 내의 컴퓨터에서 www.naver.com에 접속한다면

1. 한양대학교 local Domain Server에 www.naver.com의 ip를 요청함
2. local Domain Server에 www.naver.com IP가 있다면 바로 반환하고 그렇지 않다면 관리하고 있는 도메인 네임(ex naver.com, .com) 중 있는 IP 주소로 요청을 해 재귀적으로 IP 주소를 얻어냄

TTL: Local Doamin Server는 일종의 캐시 서버로 볼 수 있는데 outdated 정보를 가지고 있을 수 있다. 이런 문제를 해결하기위해 DNS records에 TTL(Time to Leave)를 두어 기한이 될 떄마다 records를 삭제한다.

### DNS와 UDP

DNS는 UDP를 사용한다. 이는 직관적 이상하게 느껴질 수 있다.
왜냐하면 server host의 ip주소는 손실 없이 안정성을 보장해야하는 것 처럼 느껴지기 때문이다.

그럼에도 DNS가 UDP 통신을 사용하는 이유는 다음과 같다.

- 하나의 DNS records는 40byte이다. 패킷이 손실되더라도 40byte의 정보만 손실되고 요청을 다시 보내면된다. (50kb 요청 중 패킷 하나가 손실되면 50kb을 전부 다시 패치해야한다.)
