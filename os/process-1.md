# Process - 1

## 1️⃣ 프로세스(Process)

### 1) 프로세스의 개념
- Process = 'program in execution'
- 프로세스 문맥(context) : 특정 시점에서 프로세스가 어디까지 수행했는지, 프로세스 메모리에 어떤 내용을 담고 있는지 등을 나타냄 -> 프로세스의 현재 상태를 나타내는 데에 필요한 모든 요소를 의미
  1. CPU 수행 상태를 나타내는 하드웨어 문맥
     - Program Counter -> 어떤 메모리 주소를 가리키고 있었는가?
     - Registers -> 주로 레지스터가 어떤 값을 가지고 있었는가?
  2. 프로세스의 주소 공간
     - code, data, stack
  3. 프로세스 관련 커널 자료 구조
     - PCB (Process Control Block)
     - Kernel stack : 프로세스마다 별도로 존재
<br><br>

### 2) 프로세스 문맥의 필요성
현대의 운영체제는 대부분 시분할 방식 -> CPU가 한 번에 여러 개의 프로세스를 관리한다. 하나의 프로세스가 CPU 제어권을 갖고 레지스터에 어떠한 값을 넣고 실행할 때 백업해놓지 않는다면 (프로세스 문맥을 모른다면) 다음에 CPU 제어권을 가졌을 때 처음부터 다시 실행될 수도 있다. 따라서 프로세스 문맥을 아는 것이 중요하다.
<br><br>

### 3) 프로세스의 상태 (Process State)

![image](https://github.com/junseoparkk/til/assets/98972385/e3150fc0-bf9a-456d-b24c-29599de26fde)

프로세스는 상태가 변경되며 수행된다.

- Running
  - CPU를 잡고 인스트럭션을 수행중인 상태
- Ready
  - CPU를 기다리는 상태 (메모리 등 다른 조건은 모두 만족)
- Blocked (wait, sleep)
  - CPU를 주어도 당장 인스트럭션을 수행할 수 없는 상태
  - 프로세스 자신이 요청한 이벤트가 즉시 만족되지 않아 이를 기다리는 상태
    - ex) 디스크에서 file을 읽어와야 하는 경우 등 I/O 작업
- New
  - 프로세스가 생성중인 상태
- Terminated
  - 수행이 끝난 상태
<br><br>

![image](https://github.com/junseoparkk/til/assets/98972385/79664b25-f2ba-4eb0-a0ad-bd0a295162ff)

- CPU에는 하나의 프로세스만 실행한다. 만약 Timer Interrupt가 들어오면 해당 프로세스는 CPU 제어권을 뺏기고 Ready queue의 뒤로 들어가게 된다.
- 여기서 queue는 운영체제 커널 내의 Data영역에 자료구조를 만들고, 프로세스 상태를 바꿔가며 CPU 제어권을 넘기는 방식으로 운영한다.
<br><br>

### 🔎 만약 프로세스 실행 도중 디스크에서 데이터를 읽어와야 한다면?
- CPU 내의 프로세스는 Running -> Blocked 로 상태가 바뀌면서 Disk I/O queue의 뒤로 들어간다.
- 디스크 컨트롤러에 의해 디스크는 들어온 요청을 순차적으로 처리한다. 만약 작업이 끝나면 컨트롤러는 CPU에게 인터럽트를 건다.
- 인터럽트가 들어오면 CPU는 하던 작업을 잠시 멈추고 CPU 제어권은 운영체제의 커널로 넘어간다.
- 앞서 끝난 I/O 작업에서 해당되는 메모리 영역의 데이터를 넘겨주거나, 프로세스를 Blocked -> Ready 로 바꿔서 CPU를 얻을 수 있는 자격을 부여한다.
- queue지만 무조건 앞선 프로세스 순서대로 수행하지 않을 때도 있다. 우선순위를 부여해 먼저 처리하기도 한다.

### 🔎 놀이동산에 비유한다면?
- CPU : 롤러코스터
  - CPU의 처리 속도는 굉장히 빠르다. 따라서 Ready queue에 대기하는 시간에 비해 실행 시간이 굉장히 짧다.
- I/O 작업 : 3D 체험
  - I/O 작업 속도는 CPU에 비해 수십 ~ 수백만배 느리다. 따라서 queue에 대기하는 시간과 수행 시간 또한 길다.
- 따라서 각 장치들과 프로세스가 놀지 않고 효율적으로 실행되도록 하는 방법이 필요하다.
<br><br>

## 2️⃣ PCB (Process Control Block)
- 운영체제가 각 프로세스를 관리하기 위해 프로세스당 유지하는 정보를 의미

### 1) 구성 요소 (구조체로 유지)

![image](https://github.com/junseoparkk/til/assets/98972385/c4eb4170-e26e-4b8e-b1ac-d1e52033ddd1)

1. OS가 관리상 사용하는 부분
   - Process state, Process ID
   - Scheduling information, priority
2. CPU 수행 관련 하드웨어 값
   - Program counter, registers 
3. 메모리 관련
   - Code, Data, Stack의 위치 정보
4. 파일 관련
   - Open file descriptors 등
<br><br>

### 2) 문맥 교환 (Context Switch)

![image](https://github.com/junseoparkk/til/assets/98972385/ec4ea35c-91b6-4c5f-8d44-3b1e7d0efb75)

- CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정
- CPU가 다른 프로세스에게 넘어갈 때 운영체제는 다음 과정을 수행
  - CPU를 내어주는 프로세스의 상태를 해당 프로세스의 PCB에 저장
  - CPU를 얻는 프로세스의 상태를 PCB에서 읽어옴
<br><br>

### 🔎 문맥 교환은 항상 일어나는 것인가?
System call 또는 Interrupt 발생시 반드시 Context Switch가 일어나는 것은 아니다. Context Switch는 사용자 프로세스 하나로부터 또 다른 프로세스로 CPU가 넘어가는 것.

- 단순히 아래처럼 CPU 제어권이 사용자 프로세스 -> 운영체제 커널로 넘어가는 경우는 컨텍스트 스위치가 아님
  1. System call : 프로세스가 본인이 필요해서 운영체제에 무언가를 요청할 때
  2. Interrupt : 컨트롤러와 같은 장치가 CPU에게 정보를 전달할 때
<br><br>

![image](https://github.com/junseoparkk/til/assets/98972385/39a65bf6-655d-43db-b4fa-60f26c1d177a)


#### 예시 1
1. 사용자 프로세스 A (User mode)
<br> -> interrupt or system call
2. ISR 또는 시스템 콜 함수 (Kernel mode)
<br> -> context switch 없이 user mode 복귀
3. 사용자 프로세스 A (User mode)

#### 예시 2
1. 사용자 프로세스 A (User mode)
<br> -> timer interrupt or I/O 요청 system call
2. Kernel mode
<br> -> context switch
3. 사용자 프로세스 B (User mode)
<br><br>

- ❗️ 문맥 교환이 일어나지 않을 때 CPU 수행 정보 등 context의 일부를 PCB에 저장해야 하지만, 문맥 교환을 하는 경우 그 부담이 훨씬 크다. (eg. cache memory flush)
<br><br>

### 3) 프로세스 스케줄링을 위한 큐(Queue)
1. Job Queue
    - 현재 시스템 내에 있는 모든 프로세스의 집합
2. Ready Queue
    - 현재 메모리 내에 있으면서 CPU를 잡아서 실행되기를 기다리는 프로세스 집합
3. Device Queue
    - I/O Device의 처리를 기다리는 프로세스 집합

❗️프로세스들은 각 큐를 오가면서 수행된다.
<br><br>

## 3️⃣ 스케줄러 (Scheduler)

### 1) 스케줄러 종류

#### 1. Long-term Scheduler (장기 스케줄러 or job scheduler)
- 시작 프로세스 중 어떤 것들을 ready queue로 보낼지 결정
- 프로세스에 메모리 및 각종 자원을 주는 문제
- degree of Multiprogramming 제어 -> 메모리에 몇 개의 프로세스를 올릴 것인가?
- Time Sharing System에는 보통 장기 스케줄러가 없음 -> 무조건 ready

#### 2. Short-term Scheduler (단기 스케줄러 or CPU scheduler)
- 어떤 프로세스를 다음 번에 Running 시킬지 결정
- 프로세스에 CPU를 주는 문제
- 충분히 빨라야 함 (millisecond 단위)

#### 3. Medium-term Scheduler (중기 스케줄러 or Swapper)
- 여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 보냄
- 프로세스에게서 메모리를 뺏는 문제
- degree of Multiprogramming 제어 -> 메모리에서 몇 개의 프로세스를 내릴 것인가?
<br><br>

### 2) 프로세스 상태

![image](https://github.com/junseoparkk/til/assets/98972385/fea812a9-4647-41fb-a6ca-d734ca398966)

❗️중기 스케줄러가 들어있기 때문에 현대의 운영체제에서는 프로세스의 상태(3가지)에서 'Suspended'가 추가 된 것 

- Running
  - CPU를 잡고 인스트럭션을 수행중인 상태
- Ready
  - CPU를 기다리는 상태 (메모리 등 다른 조건은 모두 만족)
- Blocked (sleep, wait)
  - CPU를 주어도 당장 인스트럭션을 수행할 수 없는 상태
  - 프로세스 자신이 요청한 이벤트가 즉시 만족되지 않아 이를 기다리는 상태
    - ex) 디스크에서 file을 읽어와야 하는 경우 등 I/O 작업
- Suspended (stopped) : inactive
  - 외부적인 이유로 프로세스의 수행이 정지된 상태
  - 프로세스는 통째로 디스크에 swap out 된다.
    - ex) 사용자가 프로그램을 일시 정지시킨 경우 (break key), 시스템이 여러 이유로 프로세스를 잠시 중단시킨 경우 (메모리에 너무 많은 프로세스가 올라와 있을 때)
<br><br>

❗️Blocked : 자신이 요청한 event가 만족되면 Ready<br>
❗️Suspended : 외부에서 resume해주어야 Active
<br><br><br>
---
references<br>
KOCW 반효경-Introduction to Operating Systems