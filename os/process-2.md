
# Process - 2

## 1️⃣ 동기식 입출력 & 비동기식 입출력

### 1) 동기식 입출력 (synchronous I/O)

![image](https://github.com/junseoparkk/til/assets/98972385/0ffcf5f4-7b4c-4200-9153-5d7ea084f145)


- I/O 요청 후 입출력 작업이 완료된 후에 사용자 프로그램에게 제어가 넘어감
1. 구현방법 1
    - I/O가 끝날 때까지 CPU를 낭비시킴
    - 매시점 하나의 I/O만 일어남
2. 구현방법 2
    - I/O가 끝날 떄까지 해당 프로그램에게서 CPU를 빼앗음
    - I/O 처리를 기다리는 대기 줄에 해당 프로그램을 세움
    -  다른 프로그램에게 CPU를 넘김
<br><br>

### 2) 비동기식 입출력 (asynchronous I/O)

![image](https://github.com/junseoparkk/til/assets/98972385/7078d7d2-e531-4c6e-9624-d416f522f996)

- I/O가 시작된 후 입출력 작업이 끝나기를 기다리지 않고 제어가 사용자 프로그램에 즉시 넘어감
<br><br>

🔎 두 경우 모두 I/O의 완료를 인터럽트를 통해 알려줌
<br><br>

## 2️⃣ Thread (스레드)

### 1) 스레드 개념

스레드는 CPU를 수행하는 가장 기본 단위, 'lightweight process'라고도 불림

![image](https://github.com/junseoparkk/til/assets/98972385/32c12797-6879-486f-9809-20a474b0e18f)

- Thread의 구성
  - CPU 수행과 관련된 정보
  - program counter
  - register set
  - stack space
- Thread가 동료 Thread와 공유하는 부분 (=task)
  - code section
  - data section
  - OS resources
<br><br>

🔎 프로세스가 하나 주어질 때
- 프로세스마다 code, data, stak으로 구성된 주소 공간이 만들어짐
- 프로세스 하나를 관리하기 위해 운영체제 내부에 PCB가 존재 (프로세스 하나당 PCB 하나)
- program counter를 통해 현재 메모리에서 실행되고 있는 부분을 나타냄
<br><br>

🔎 동일한 작업을 하는 프로세스가 여러 개 주어질 때
- 각각 별도의 프로세스를 생성하면 메모리 공간이 여러 개 생겨 메모리 낭비가 발생
- 해당 프로세스의 메모리 공간을 하나만 생성
- 프로세스마다 다른 부분의 코드를 실행할 수 있도록 해주는 것이 '스레드'
- program counter 만 여러 개 두면 됨 
<br><br>

### 2) 장점
- 멀티 스레드로 구성된 테스크 구조에서는 하나의 서버 스레드가 blocked(waiting) 상태인 동안에도 동일한 테스크 내의 다른 스레드가 실행되어 빠른 처리가 가능
- 동일한 일을 수행하는 멀티 스레드가 협력하여 높은 처리율과 성능 향상을 얻을 수 있음
- 스레드를 사용하면 병렬성을 높일 수 있음
<br><br>
1. 응답성 (Responsiveness)
    - ex) multi-threaded web
      - network 스레드가 block
      - 다른 스레드는 display
2. 자원 공유 (Resource Sharing)
    - 같은 프로세스를 여러 개 생성하기 보단 하나만 생성, CPU 수행 단위만 여러 개를 둠
    - code, data, 각종 자원들은 스레드간 공유 가능 -> 효율적인 자원 사용
3. 경제성 (Economy)
    - 프로세스 생성, CPU 스위칭보다 스레드 생성, 스위칭의 오버헤드가 훨씬 적음
    - 가능하면 같은 작업을 하는 프로세스는 줄여야 함
4. 병렬성
    - 멀티 프로세서 환경 -> 스레드를 각 프로세서에서 효율적으로 실행 가능
<br><br>

### 3) 스레드 구현 방법
- Kernel Threads
  - 운영체제 커널의 도움을 받음
  - 운영체제 커널은 스레드가 여러 개 있다는 사실을 알고 있음
  - 하나의 스레드에서 다른 스레드로 CPU가 넘어갈 때 CPU 스케줄링처럼 커널이 넘김
  - ex) Windows 95/98/NT, Solaris, Digital UNIX, Mach
- User Threads
  - 라이브러리의 도움을 받음 
  - 운영체제 커널은 스레드가 여러 개 있다는 사실을 모름
  - 사용자 프로그램이 스스로 여러 스레드를 관리
  - ex) POSIX Pthreads, Mach C-threads, Solaris threads
---
<br><br><br>

#### references
- ABRAHAM SILBERSCHATZ ET AL., OPERATING SYSTEM CONCEPTS, NINTH EDITION, WILEY, 2013
- 반효경, 운영체제와 정보기술의 원리, 이화여자대학교 출판부, 2008


