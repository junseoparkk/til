# Process Synchronization - 1

## 1️⃣ 데이터 접근

![image](https://github.com/junseoparkk/crud-board/assets/98972385/1ba661f0-fe94-4e94-9dc0-ebd7b7b70b28)
<br>

### 예시)

|E-box|S-box|
|:---:|:---:|
|CPU|Memory|
|컴퓨터 내부|디스크|
|프로세스|프로세스 주소 공간|
<br>

### 1) Race Condition (경쟁 상태)

![image](https://github.com/junseoparkk/crud-board/assets/98972385/6cc27d99-efdf-4a7e-8f4b-b198a8684220)
<br>

- 병렬 컴퓨팅 환경에서 발생하는 SW 버그의 일종으로, 여러 스레드 또는 프로세스가 공유 자원에 동시 접근하고, 변경하는 경우 발생
- 만약 S-box를 공유하는 E-box가 여러 개 있는 경우 `Race Condition` 의 가능성이 있음
- Memory - CPU : Multiprocessor system (CPU가 여러 개인 시스템)
- Address Space - Process : 공유메모리를 사용하는 프로세스들, 커널 내부 데이터를 접근하는 루틴들 간
  - ex) 커널모드 수행 중 인터럽트로 커널모드의 다른 루틴 수행시
<br><br>

---
### 2) Race Condition 이 발생하려면?
1. kernel 수행 중 인터럽트가 발생한 경우
2. 프로세스가 시스템 콜을 하여 커널 모드로 수행 중인데 context switch가 일어나는 경우
3. Multiprocessor 에서 공유 메모리 내의 커널
<br><br>

<img width="650" alt="image" src="https://github.com/junseoparkk/crud-board/assets/98972385/304097c5-c0ce-4db2-9433-0c5448037811">
<br><br>

### Race Condition #1
- 'Count' 변수의 값을 1 증가시킬 때
- 여러 개의 인스트럭션으로 나눠 실행

1. load : 메모리에 존재하는 Count 변수 값을 CPU의 register로 불러들임
    - 만약 인터럽트가 발생하면 하던 일을 멈추고 인터럽트 처리 루틴으로 넘어감 (Interrupt Handler)
2. inc : 해당 값을 1 증가시킴
3. store : register의 값을 다시 메모리에 저장
4. 결국 1 감소한 것은 반영되지 않음
<br><br>

### Race Condition #2

<img width="700" alt="image" src="https://github.com/junseoparkk/crud-board/assets/98972385/a4788de1-8417-4a6d-890a-aa91d81083bb">
<br><br>

1. 유저 모드 상태에서 시스템 콜 요청 -> 커널 모드
2. ProcessA는 'Count' 변수를 읽어와 증가시키던 도중 CPU 할당 시간이 만료 (CPU preempt)
3. ProcessB에 CPU 할당, context switching -> 커널모드에서 Count 값 증가
4. ProcessB CPU 할당 시간 만료 (CPU preempt)
5. ProcessA 기존 작업 (count++) 진행
6. ProcessB에서 증가시키기 전에 ProcessA 에서 이미 컨텍스트를 다룸
<br><br>

### Race Condition #3

<img width="700" alt="image" src="https://github.com/junseoparkk/crud-board/assets/98972385/233f6def-a35b-405d-abb4-5126df7ba919">
<br><br>

- 어떤 CPU가 마지막으로 count를 저장했는가? =>  Race dondition
- Multiprocessor 의 경우 interrupt enable/disalbe 로 해결되지 않음
- 해결 방법
    1. 한번에 하나의 CPU만 커널에 접근할 수 있게 하는 방법
    2. 커널 내부에 있는 각 공유 데이터에 접근할 때마다 해당 데이터에 대해 Lock/Unlock
<br><br>

---
### 3) Process Synchronization Problem
- 공유 데이터의 동시 접근은 데이터의 불일치 문제를 야기할 수 있음
- 일관성 유지를 위해서는 협력 프로세스 간의 실행 순서를 정해주는 메커니즘이 필요
- `Race Condition`
  - 여러 프로세스들이 동시에 공유 데이터에 접근하는 상황
  - 데이터의 최종 연산 결과는 마지막에 해당 데이터를 다룬 프로세스에 따라 달라짐
- race condition을 막기 위해서 concurrent process는 동기화되어야 함
<br><br>

---
### 4) The Critical - Section Problem
<img width="700" alt="image" src="https://github.com/junseoparkk/crud-board/assets/98972385/78af904a-aee5-4ade-b73e-d775dea31fe4">
<br><br>

- n 개의 프로세스가 공유 데이터를 동시에 사용하기를 원하는 경우
- 각 프로세스의 'code segment' 에는 공유 데이터에 접근하는 코드인 `critical section` 존재
- 문제 상황
  - 하나의 프로세스가 critical section에 있을 떄 다른 모든 프로세서는 critical section에 들어갈 수 없어야 함
<br><br>

## 2️⃣ 데이터 접근 문제 해결

### 1) Initial Attempts to Solve Problem
- 두 개의 프로세스 P0, P1이 있다고 가정
- 프로세스들의 일반적인 구조
- 프로세스들은 수행의 동기화 (synchronize) 를 위해 몇몇 변수 공유 가능
  - `synchronization variable`
<br><br>

```java
do {
  entry section // Lock 설정
  critical section
  exit section  // Lcok 해제
  remainder section
} while (1);
```
<br>

---

### 2) 프로그램적 해결법의 충족 조건
1. Mutual Exclusion (상호 배제)
   - 프로세스 Pi가 'critical section' 부분을 수행 중이면 다른 모든 프로세스들은 그들의 critical section 에 들어가면 안 된다.
2. Progress (진행)
   - 'critical section' 에 아무도 들어가있지 않은 상태에서 critical section에 들어가고자 하는 프로세스가 있다면 들어가게 해주어야 한다.
3. Bounded Waiting (유한 대기)
   - 프로세스가 'critical section' 에 들어가려고 요청한 뒤부터 해당 요청이 허용될 떄까지 다른 프로세스들이 critical section에 들어가는 횟수엔 한계가 있어야 한다.
<br><br>
---

### 3) Algorithms

### Algorithm #1
- Synchronization variable : `int turn`
- `turn == i` : i 번째 프로세스가 critical section 에 접근할 수 있음을 의미 (차례)
  - ex) turn = 0, turn = 1
- 'Mutual Exclusion' 은 만족하지만, 'Progress' 는 만족하지 않음
  - 특정 프로세스가 critical section 에 들어갔다가 나와야 다음 프로세스로 진행 가능 (turn을 바꿔줘야 함)
<br>

```java
do {
  while (turn != 0) ;
  critical section
  turn = 1;
  remainder section
} while (1);
```
- 처음 turn을 0번째 프로세스로 세팅
- 0번 프로세스가 프로세스가 아니라면 while문 실행 동안 대기
- `turn = 0` 이 되면 critical section 으로 진입
- critical section 에서 나오면 `t`urn = 1` 로 변경
<br><br>

---
### Algorithm #2
- Synchronization variable : `boolean flag[2]`
- `flag[i] == true` : i 번째 프로세스가 critical section 에 접근할 준비가 됐음을 의미
- 역시 'Mutual Exclusion' 은 만족하지만, 'Progress' 는 만족하지 않음
  - 특정 프로세스가 critical section 에 들어갔다가 나와야 flag 를 바꿀 수 있음
  - 둘 다 while 문까지 수행 후 끊임 없이 양보하는 상황 발생 가능
<br>

```java
do {
  flag[i] = true;
  while (flag[i]) ;
  critical section
  flag[i] = false;
  remainder section
} while (1);
```
<br>

---
### Algorithm #3 (Peterson's Algorithm)
- turn, flag 변수를 모두 사용하는 알고리즘
- Pi 가 critical section 에 들어가고자 할 때 
  - `flag[i] = true` : critical section 에 들어가겠다는 것을 명시
  - `turn = j` : 상대방 턴으로 바꿔 놓음
  - 들어가기 전 Pj 가 critical section 에 들어갈 것인지, Pj 차례인지 확인
    - 두 조건을 모두 만족한다면 대기. 즉, Pj가 하나라도 만족하지 않는다면 Pi 가 들어감
    - 빠져나올 땐 `flag[i] = false` 로 변경
- 'Mutual Exclustion', 'Progress', 'Bounded Waiting' 모두 만족
 
<br>

```java
do {
  flag[i] = true;
  turn = j;
  while (flag[j] && turn == j) ;  // waiting
  critical section
  flag[i] = false;
  remainder section
} while (1);
```
- 두 프로세스가 critical section 에 들어가고자 할 때 : turn 으로 구분
- 아무도 critical section 에 들어가있지 않을 때 : turn 에 관계 없이 들어감
<br><br>

#### ❗️ Busy Waiting (= Spin Lock) 문제
- 계속해서 CPU 와 memory 를 사용하면서 wait
- 특정한 작업이 완료될 때까지 계속 CPU를 사용하기 때문에 리소스 낭비가 발생할 수 있음
<br><br>

---

### 2) Synchronization Hardware

![image](https://github.com/junseoparkk/til/assets/98972385/3f0822ca-e231-4bd5-9f9e-bf78634c9aab)
<br>

- Synchronization variable : `boolean lock`
- 하드웨어적으로 `Test & modify` 를 atomic 하게 하나의 인스트럭션으로 수행할 수 있도록 지원
- `Test_and_Set` : 하나의 인스트럭션으로 데이터 읽기, 쓰기를 동시에 지원
  - 초기 Lock = 0 으로 세팅
  - critical section 에 들어가기 전 Lock = 1 로 변경
  - Lock 걸려있는지 확인 & Lock 걸려있지 않다면 Lock 을 걸고 진입 -> 동시에 진행
<br><br>

```java
do {
  while (Test_and_Set(lock)) ;
  critical section
  lock = false;
  remainder section
} while(1);
```
<br>

## 3️⃣ Semaphores

### 1) 세마포어란?
- 추상 자료형, Semaphore 변수 S
  - integer variable (자원의 개수)
  - P 연산, V 연산 두 가지 atomic 연산에 의해서만 접근 가능
- 왜 사용하는가?
  - Lock 을 걸고 해제하는 기능을 간단하게 제공할 수 있음
  - 공유 자원을 획득하고 반납하는 것을 처리
<br><br>

#### 1) P 연산 : P(S)
- 세마포어 변수 값을 획득하는 과정
- 공유 데이터를 획득하는 과정
- Lock 을 거는 과정
```java
while (S <= 0) do no-op;
S--;
````
<br>

#### 2) V 연산 : V(S)
- 공유 데이터 사용 후 반납하는 과정
- Lock 을 해제하는 과정
```java
S++;
```
<br>

---

### 2) Critical Section of n Processes
- Synchronization variable : `mutex`
  - initially 1 : 1개가 critical section 에 들어갈 수 있음
<br>

```java
do {
  P(mutex);
  critical section
  V(mutex);
  remainder section
} while (1);
```
<br>

- 그럼에도 busy-wait 는 효율적이지 못함 (= spin lock)
- Block & Wakeup 방식의 구현 (= sleep lock)
<br><br>

--- 

### 3) Block & Wakeup Implementation (구현)
```c
typedef struct {
  int value;  // semaphore
  struct process *L;  // process wait queue
} semaphore;
```

- Semaphore 를 위처럼 정의
- block & wakeup 을 다음과 같이 가정
- `block`
  - 커널은 block 을 호출한 프로세스를 suspend 시킴
  - 해당 프로세스의 PCB 를 semaphore 에 대한 wait queue 에 넣음
- `wakeup(P)`
  - block 된 프로세스 P 를 wakeup 시킴
  - 해당 프로세스의 PCB 를 ready queue 로 옮김
<br><br>

<img width="600" alt="image" src="https://github.com/junseoparkk/til/assets/98972385/b0c9dc28-4164-45ad-9395-6e8e3ac5e15c">

- semaphore 변수를 누군가 획득했다면, 획득하지 못한 프로세스의 PCB 를 semaphore 의 ready queue 에 연결
<br><br>

#### 1) P(S)
```java
S.value--;
if (S.value < 0) {  // 자원의 여분이 없다면
  add this process to S.L;  // blocked 상태로 있다가 여분이 생기면 wakeup
  block();
}
```

#### 2) V(S)
```java
S.value++;
if (S.value <= 0) { // 자원을 내놓았음에도 잠들어있는 상태라면
  remove a process P from S.L;
  wakeup(P);
}
```
<br>

#### ❗️ S.value 의미
- 음수일 때 : 누군가 자원을 기다리는 상황
- 양수일 때 : 자원에 여분이 있기 때문에 기다리지 않고 사용하는 상황
- 따라서 누군가 깨워야할지를 판단하기 위해 사용
<br>
---

### 4) Busy-wait vs Block/wakeup
- 보통은 block/wakeup 을 사용하는 것이 효율적. 그럼에도 block/wakeup 에도 overhead 가 있음
- Block/wakeup overhead vs Critical section 길이
  - Critical section 의 길이가 긴 경우 : Block/wakeup 이 적당
  - Critical section 의 길이가 매우 짧은 경우 : Blcok/wakeup 오버헤드가 busy-wait 오버헤드보다 더 커질 수 있음
  - 일반적으로는 Block/wakeup 방식이 더 좋음
<br>

---

### 5) Types of Semaphore
1. Counting semaphore
   - 도메인이 0 이상인 임의의 정수 값
   - 주로 resource counting 에 사용 (여분의 자원 개수를 판단)
   - 여분이 있다면 가져다 쓸 수 있음
2. Binary semaphore (= mutex)
   - 0 또는 1 값만 가질 수 있는 세마포어
   - 주로 mutual exclusion (상호배제, lock/unlock) 용도로 사용
<br><br>


## 4️⃣ Deadlock & Starvation

### 1) Deadlock
- 둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 event를 무한히 기다리는 현상
- S 와 Q 가 1로 초기화된 semaphore, 특정 작업을 위해 S, Q 모두 획득해야 한다고 가정
<br><br>

<img width="600" alt="image" src="https://github.com/junseoparkk/til/assets/98972385/e5a4b475-70b1-4445-b604-aedef2b03188">
<br><br>

- P0, P1 모두 S, Q 를 얻고 반납함
- P0 가 CPU를 먼저 얻어서 S를 획득한 뒤 CPU를 뺏김
- P1 은 CPU를 얻어 Q를 획득. 이후 S를 획득하려고 하지만 P0 가 S를 가짐.
- P0 는 S 작업이 끝난 뒤 내놓기 때문에 P1 은 이를 무한히 대기 (조건 충족 X)


#### ❗️ 이를 해결하려면?
- P1 의 순서를 P(S) - P(Q) 의 순서로 바꿔준다.
<br><br>
 
### 2) Starvation
- indefinite blocking
- 프로세스가 suspend 된 이유에 해당하는 세마포어 큐에서 빠져나갈 수 없는 현상

---
<br><br><br>

#### references
- ABRAHAM SILBERSCHATZ ET AL., OPERATING SYSTEM CONCEPTS, NINTH EDITION, WILEY, 2013
- 반효경, 운영체제와 정보기술의 원리, 이화여자대학교 출판부, 2008




