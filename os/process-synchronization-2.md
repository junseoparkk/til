# Process Synchronization - 2

## 1️⃣ Classical Problems of Synchronization

### 1) Bounded-Buffer Problem (Producer-Consumer Problem)
- Buffer 란 임시로 데이터를 저장하는 공간
- Buffer 의 크기가 유한한 환경에서의 문제
- 두 가지 프로세스가 존재
  - Producer (생산자) : 공유 버퍼에 데이터를 만들어 집어 넣는 역할 (포인터 사용)
  - Consumer (소비자) : 공유 버퍼의 데이터를 꺼내 가는 역할
<br><br>

<img width="800" alt="image" src="https://github.com/junseoparkk/til/assets/98972385/4d413f0d-bd35-42f6-996b-d04b51ab138f">
<br><br>

### 🔎 문제 상황
1. 생산자와 소비자 간 동시에 공유 버퍼에 접근하는 문제
    - 접근을 위해 버퍼 전체에 락을 걸고 작업을 마치면 락을 해제
2. 공유 버퍼의 크기가 유한하기에 발생하는 문제
    - 공유 버퍼의 크기가 꽉 찰 경우 생산자 입장에서 데이터를 넣을 수 없음
      - 소비자가 데이터를 꺼내 가야 버퍼 공간이 생김
    - 공유 버퍼에 데이터가 하나도 없을 경우 소비자 입장에서 꺼내갈 데이터가 없음
      - 가용 자원의 개수를 세서 접근 가능한 데이터가 있는지 확인해야 함
<br><br>

### 🔎 참고
- Shared Data
  - 버퍼 자체 및 버퍼 조작 변수
- Synchronization variables (세마포어 변수)
  - mutual exclusion
    - 공유 데이터의 상호 배제를 위해 binary semaphore 필요
    - Lock 을 걸고 해제하는 용도
  - resource count
    - 자원의 개수를 세기 위한 용도
      - 생산자 입장 : 비어있는 버퍼의 개수
      - 소비자 입장 : 내용이 들어있는 버퍼의 개수
<br><br>


<img width="700" alt="image" src="https://github.com/junseoparkk/til/assets/98972385/89667d32-02a7-45d6-8c05-c0a13b37d75d">
<br><br>

### 3 가지 세마포어 변수
  1. `semaphore full = 0` : 내용이 들어 있는 버퍼 개수를 세기 위한 용도
  2. `empty = n` : 비어있는 버퍼 개수를 세기 위한 용도 (처음엔 n -> 공유 버퍼 개수랑 동일)
  3. `mutex = 1` : Lock 을 걸기 위한 용도
<br><br>

### Producer
1. P(empty) : 빈 버퍼의 개수를 셈. 0이라면 대기, 빈 버퍼가 있다면 하나 획득
2. P(mutex) : 버퍼에 데이터를 집어 넣기 위해 Lock 을 걺
3. 데이터를 집어 넣음
4. V(mutex) : Lock 해제
5. V(full) : 내용이 들어있는 버퍼의 개수를 1 증가 (소비자 입장에서 자원을 하나 증가)
<br><br>

### Consumer
1. P(full) : 내용이 들어있는 버퍼가 없다면 대기(생산자가 데이터를 넣기 전까지). 있다면 버퍼 획득
2. P(mutex) : 버퍼에 Lock 을 걺
3. 데이터를 꺼내 감
4. V(mutex) : Lock 해제
5. V(empty) : 비어있는 버퍼의 개수를 1 증가 (생산자 입장에서 자원을 하나 증가)
---

### 2) Readers - Writers Problem
- 두 가지 프로세스가 존재 : 쓰기(write) / 읽기(read)
- 한 프로세스가 DB 에 write 중일 때 다른 프로세스가 접근하면 안됨
- read 는 동시에 여럿이 해도 됨
- 해결책
  - Writer 가 DB 에 접근 허가를 아직 얻지 못한 상태에선 대기중인 모든 Reader 에 대한 DB 접근 허용
  - Writer 는 대기 중인 Reader 가 하나도 없을 때 DB 접근 허용
  - 일단 Writer 가 DB 에 접근 중이면 Reader 들은 접근 금지
  - Writer 가 DB 에서 빠져나가야만 Reader 의 접근을 허용
<br><br>

### 🔎 참고
- Shared data
  - DB 자체
  - readcount (현재 DB에 접근 중인 Reader 수)
- Synchronization variables
  - mutex : 공유 변수 readcount 를 접근하는 코드 (critical section) 의 mutual extension 보장을 위해 사용
  - db : Reader / Writer 가 공유 DB 자체를 올바르게 접근하게 하는 역할
<br><br>

<img width="700" alt="image" src="https://github.com/junseoparkk/til/assets/98972385/d91f06f5-a2b9-42a4-97b6-702a58bb955c">
<br><br>

### 2 가지 세마포어 변수
1. `semaphore mutex = 1` : 공유 변수(readcount)에 Lock 을 걸기 위한 binary semaphore
2. `db = 1`
    - P(db) : DB 에 대한 Lock 을 거는 과정
    - V(db) : DB 에 대한 Lock 을 해제하는 과정
<br><br>

### ❗️ 문제점
- 위 코드에선 Starvation 발생 가능
- Writer 가 쓰기 전 Reader 가 읽는 작업이 계속해서 진행할 경우 읽기 작업이 후순위로 밀리게 됨
  - 늦게 도착한 Reader 의 작업은 쓰기 이후에 진행하도록 하는 등 방법이 있음

---

### 3) Dining - Philosophers Problem

<img width="700" alt="image" src="https://github.com/junseoparkk/til/assets/98972385/b6726bb3-6831-46da-a35a-9103801031d1">
<br><br>

### ❗️ 문제점
- Deadlock 발생 가능성이 있음
- 모든 철학자가 동시에 배가 고파져 왼쪽 젓가락을 집어버린 경우
<br><br>

### 🔎 해결 방안
- 4 명의 철학자만이 테이블에 동시에 앉을 수 있도록 한다.
- 젓가락을 두 개 모두 집을 수 있을 때만 젓가락을 집을 수 있게 한다.
- 비대칭
  - 짝수 (홀수) 철학자는 왼쪽 (오른쪽) 젓가락부터 집도록 한다.
<br><br>

<img width="700" alt="image" src="https://github.com/junseoparkk/til/assets/98972385/85c8be2b-1289-48eb-b68d-7d4d00aba29c">
<br><br>

- `enum state[i]` : 철학자의 상태
- `semaphore self[i] = 0` :  i 번째 철학자가 젓가락 두 개를 모두 잡을 수 있는 권한
- `semaphore mutex = 1` : 공유 변수에 대한 동시 접근을 막기 위한 변수

## 2️⃣ Monitor

### 1) Semaphore 의 문제점
- 코딩하기 힘듦
- 정확성의 입증이 어려움
- 자발적 협력이 필요
- 한 번의 실수가 모든 시스템에 치명적 영향을 미침
- ex)
```c
// Mutual exclusion 깨짐
V(mutex)
Critical Section
P(mutex)
```

```c
// Deadlock 발생
P(mutex)
Critical Section
P(mutex)
```
<br>

### 2) Monitor
- 동시 수행중인 프로세스 사이에서 'abstract data type' 의 안전한 공유를 보장하기 위한 'high-level synchronization construct'
  - 공유 데이터를 밖에서 아무나 접근할 수 없도록한 뒤 모니터 내부에 공유 데이터와 이를 접근하는 프로시저를 정의
  - 만약 공유 데이터에 접근하려면 해당되는 프로시저만을 통해 접근할 수 있도록 통제
- 모니터 내에서는 한번에 하나의 프로세스만이 활동 가능
- 프로그래머가 동기화 제약 조건을 명시적으로 코딩할 필요 없음 (Lock 을 걸 필요가 없음)
- 프로세스가 모니터 안에서 대기할 수 있도록 'condition variable' 사용
  - 'condition variable' 은 'wait', 'signal' 연산에 의해서만 접근 가능
  - x.wait() 을 invoke 한 프로세스는 다른 프로세스가 x.signal() 을 invoke 하기 전까지 suspend
  - x.signal() 은 정확하게 하나의 suspend 된 프로세스를 resume, 만약 없다면 아무 일도 일어나지 않음
<br><br>

```c
monitor bounded_buffer {
    int buffer[N];
    
    /* condition 은 값을 가지진 않고 자신의 큐에 프로세스를 추가해 sleep or wakeup 하는 역할만 함 */
    condition full, emtpy;

    void produce(int x) {
        if (there is no empty buffer) {
            empty.wait();
        }
        add x to an empty buffer
        full.signal();
    }

    void consume(int *x) {
        if (there is no full buffer) {
            full.wait();
        }
        remove an item from buffer and store it to *x
        empty.signal();
    }

}
```

---
<br><br><br>

#### references
- ABRAHAM SILBERSCHATZ ET AL., OPERATING SYSTEM CONCEPTS, NINTH EDITION, WILEY, 2013
- 반효경, 운영체제와 정보기술의 원리, 이화여자대학교 출판부, 2008