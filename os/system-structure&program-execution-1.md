# System Structure & Program Execution 1

## 1️⃣ 컴퓨터 시스템 구조

![image](https://github.com/junseoparkk/til/assets/98972385/93ed0b5e-6df3-4361-9277-ca4355a34a08)

1. CPU : 매 클럭 사이클마다 메모리에서 인스트럭션(기계어)을 읽어 하나씩 실행 => I/O Device에 직접 접근하는 것이 아니라, 메모리에 올라온 작업을 수행
   - Interrupt line : CPU는 항상 메모리의 인스트럭션을 실행. 인터럽트가 들어오면 CPU 작업이 자동으로 운영체제로 넘어감
   - mode bit : CPU에서 실행되는 것이 운영체제인지, 사용자 프로그램인지 구분
   - registers : 메모리보다 더 빠르면서 공간은 작음
2. 메모리 : CPU의 작업 공간
3. I/O 디바이스 : 키보드, 마우스 등 (Input Device), 모니터, 프린터 등 (Output Device), 디스크 (I/O Deviece)
4. Device Controller : 각각의 I/O 디바이스들을 전담하는 컨트롤러 (디바이스 내부를 통제), CPU에 비해 Device Controller는 속도가 느림
5. Local Buffer : Deviece Controller의 작업 공간
6. Timer : 특정 프로그램이 CPU를 독점하는 것을 막기 위한 하드웨어 (사용자 프로그램에 특정 값을 세팅하여 CPU를 넘겨줌. 인스트럭션을 실행하다가 해당 시간이 되면 CPU에 인터럽트를 건다. 인터럽트 라인을 체크하여 없다면 다음 작업을 실행)
<br><br>

※ 만약 CPU가 키보드에서 데이터를 읽어오는 인스트럭션을 만난다면?<br>
CPU는 키보드 컨트롤러에게 만약 사용자 Input이 있다면 알려달라고 요청. (입력이 들어오는지 안들어오는지는 개의치 않음) -> 메모리에 올라온 작업 중 CPU가 수행할 수 있는 인스트럭션을 실행

※ 만약 특정 프로그램이 I/O 작업을 요청한다면?<br>
프로그램은 CPU를 운영체제로 넘긴다. -> 운영체제는 해당 I/O 컨트롤러에게 작업을 시킴 -> 요청 작업이 끝나면 I/O 컨트롤러는 CPU에 인터럽트를 건다. -> 인터럽트가 들어오면 CPU 제어권은 자동으로 운영체제로 넘어간다. -> I/O된 값(로컬 버퍼에 있는 값)을 메모리 공간에 복사 -> 프로그램에 다시 CPU를 넘김

---
### 1) Mode bit

#### 정의
사용자 프로그램의 잘못된 수행으로 다른 프로그램 및 운영체제에 피해가 가지 않도록 하기 위한 보호 장치. Mode bit을 통해 하드웨어적으로 두 가지 모드의 operation을 지원

- 1 (사용자 모드) : 사용자 프로그램 수행
- 0 (모니터 모드 = 커널 모드 = 시스템 모드) : OS 코드 수행

#### 동작 원리
- 보안을 해칠 수 있는 중요한 명령어는 커널 모드에서만 수행 가능한 '특권 명령'으로 규정
- Interrupt나 Exception 발생시 하드웨어가 mode bit을 0으로 바꿈
- 사용자 프로그램에게 CPU를 넘기기 전에 mode bit을 1로 세팅
<br><br>

### 2) Timer
- 정해진 시간이 흐른 뒤 운영체제에게 제어권이 넘어가도록 인터럽트를 발생시킴
- 타이머는 매 클럭 틱 때마다 1씩 감소
- 타이머 값이 0이 되면 타이머 인터럽트 발생
- CPU를 특정 프로그램이 독점하는 것으로부터 보호
- 타이머는 Time Sharing을 구현하기 위해 이용됨
- 타이머는 현재 시간을 계산하기 위해서도 사용
<br><br>

### 3) DMA(Direct Memory Access) Controller
만약 I/O 작업이 빈번하여 CPU에 인터럽트가 많이 발생하면 효율이 떨어짐. 따라서 I/O작업의 결과들을 DMA 컨트롤러가 모아서 CPU에게 인터럽트를 전달. 이 때 CPU는 자신의 할 일을 계속 진행하므로 효율적인 작업이 가능함
<br><br>

### 4) Device Controller
- I/O device controller
   - 해당 I/O 장치 유형을 관리하는 일종의 작은 CPU
   - 제어 정보를 위해 control register, status register를 가짐
   - local buffer를 가짐 (일종의 data register)
- I/O는 실제 device와 local buffer 사이에서 일어남
- Device controller는 I/O가 끝났을 경우 interrupt로 CPU에게 알림
<br><br>

※ device driver vs device controller
- device driver (장치구동기) : OS 코드 중 각 장치별 처리 루틴 -> software
- device controller (장치제어기) : 각 장치를 통제하는 일종의 작은 CPU -> hardware
<br><br>

## 2️⃣ 입출력(I/O)의 수행
- 모든 입출력 명령은 '특권 명령' -> mode bit = 0
- 사용자 프로그램은 어떻게 I/O를 하는가?
  - 시스템 콜 (system call) : 사용자 프로그램이 운영체제의 커널 함수를 호출하는 것 -> 운영체제에 I/O 요청
  - trap을 사용하여 인터럽트 벡터의 특정 위치로 이동
  - 제어권이 인터럽트 벡터가 가리키는 인터럽트 서비스 루틴으로 이동
  - 올바른 I/O 요청인지 확인 후 I/O 수행
  - I/O 완료시 제어권을 시스템콜 다음 명령으로 옮김
<br><bR>

## 3️⃣ 인터럽트 (Interrupt)
- 인터럽트
  - 인터럽트 당한 시점의 레지스터와 program counter를 save한 뒤 CPU의 제어를 인터럽트 처리 루틴에 넘긴다
- interrupt (넓은 의미)
  - Interrupt (하드웨어 인터럽트) : 하드웨어가 발생시킨 인터럽트 ex) I/O 디바이스, Timer 등이 인터럽트를 걺
  - Trap (소프트웨어 인터럽트)
    - Exception : 프로그램이 오류를 범한 경우
    - System call : 프로그램이 커널 함수를 호출한 경우 ex) 사용자 프로그램이 I/O가 필요할 때
- 인터럽트 관련 용어
  - 인터럽트 벡터 : 해당 인터럽트의 처리 루틴 주소를 가짐
  - 인터럽트 처리 루틴 (Interrupt Service Routine, 인터럽트 핸들러) : 해당 인터럽트를 처리하는 커널 함수
<br><br>

※ I/O를 하기 위해선 두 가지 종류의 인터럽트가 걸린다.
1. 사용자 프로그램이 I/O 요청을 위해 운영체제에 시스템 콜을 하게 됨 (소프트웨어 인터럽트)
2. I/O 작업이 끝나면 디바이스 컨트롤러는 CPU에게 작업 종료를 알리기 위해 인터럽트를 걸게 됨 (하드웨어 인터럽트)
- 현대의 운영체제는 인터럽트에 의해 구동됨
<br><br>

## 4️⃣ 시스템 콜 (System Call)
사용자 프로그램이 운영체제의 서비스를 받기 위해 커널 함수를 호출하는 것