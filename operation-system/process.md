# 프로세스

## 프로세스의 context

프로세스의 현재 상태를 나타내는 것

- CPU의 수행 상태를 나타내는 하드웨어 context
  - Program Counter
  - 각종 register
- 프로세스의 주소 공간
  - code
  - data
  - stack
- 프로세스 관련 커널 자료 구조
  - PCB (Process Control Block)
  - Kernel stack

## 프로세스의 상태

- Running
  - CPU를 잡고 instruction을 수행중인 상태
- Ready
  - CPU를 기다리는 상태(메모리 등 다른 조건을 모두 만족)
- Blocked(wait, sleep)
  - CPU가 주어지더라도 instruction을 수행할 수 없는 상태
  - Process 자신이 요청한 event(I/O)가 만족되지 않아 기다리는 상태
- Suspended(stopped)
  - 외부적인 이유로 프로세스의 수행이 정지된 상태
  - 프로세스는 통째로 디스크에 swap out된다.

// 번외

- New
  - 프로세스가 생성 중인 상태
- Terminated
  - 수행(execution)이 끝나고 process를 정리하는 상태

## PCB(Process Control Block)

- 운영체제가 각 프로세스를 관리하기 위해 프로세스당 유지하는 정보

### PCB 구성요소

- OS가 관리상 사용하는 정보
  - process 상태, ID
  - scheduling information, priority
- CPU 수행 관련 하드웨어 값
  - Program Counter, registers
- 메모리 관련
  - code, data, stack의 위치 정보
- 파일 관련
  - Open file descriptors

## Context Switch

- CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정
- 절차

  - CPU를 내어주는 프로세스의 상태를 그 프로세스의 PCB에 저장
  - CPU를 새롭게 얻는 프로세스의 PCB에서 로드

- System call이나 interrupt 발생 시 반드시 context switch가 일어나는 것은 아님

## 프로세스를 스케줄링하기 위한 큐

각 큐의 item으로는 PCB의 주소 값이 들어간다.

- Job queue
  - 현재 시스템 내에 있는 모든 프로세스의 집합
- Ready queue
  - 현재 메모리 내에 있으면서 CPU를 잡아서 실행되기를 기다리는 프로세스의 집합
- Device queue
  - I/O device의 처리를 기다리는 프로세스의 집합

## 스케줄러(Scheduler)

- Short-term scheduler(CPU scheduler)

  - 어떤 프로세스를 다음번에 running시킬지 결정
  - 프로세스에 CPU를 주는 문제

- Medium-term scheduler(Swapper)

  - 여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫓아냄
  - 프로세스에서 memory를 뺏는 문제

- Long-term scheduler(job scheduler)
  - 시작 프로세스 중 어떤 것들을 ready queue로 보낼지 결정
  - 프로세스에 memory를 주는 문제
  - time sharing system에는 보통 장기 스케줄러가 없음
