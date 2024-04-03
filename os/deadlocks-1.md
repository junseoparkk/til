# Deadlocks

## 1️⃣ 교착상태 (Deadlock)

### 1) 정의
- 일련의 프로세스들이 서로가 가진 자원을 기다리며 block 된 상태
<br><br>

### 2) Resource (자원)
- 하드웨어, 소프트웨어 등을 포함하는 개념
- ex) I/O device, CPU cycle, memory space, semaphore 등
- 프로세스가 자원을 사용하는 절차
    1. Request(요청)
    2. Allocate(획득)
    3. Use(사용)
    4. Release(반납)
<br><br>

### 3) 4가지 Deadlock 발생 조건
1. Mutual exclusion (상호 배제)
    - 매 순간 하나의 프로세스만이 자원을 사용할 수 있음
2. No preemption (비선점)
    - 프로세스는 자원을 스스로 내어놓을 뿐 강제로 빼앗기지는 않음
3. Hold and wait (보유대기)
    - 자원을 가진 프로세스가 다른 자원을 기다릴 때 보유 자원을 놓지 않고 계속 갖고 있음
4. Circular wait (순환대기)
    - 자원을 기다리는 프로세스 간 사이클이 형성되어야 함
    - 프로세스 <P0, P1, ... , Pn> 에 대해
      - P0 은 P1 이 가진 자원을 기다림
      - P1 은 P2 가 가진 자원을 기다림
      - P(n-1) 은 Pn 이 가진 자원을 기다림
      - Pn 은 P0 가 가진 자원을 기다림
<br><br>

## 2️⃣ 자원할당 그래프 (Resource - Allocation Graph)

<img width="375" alt="image" src="https://github.com/junseoparkk/junseoparkk/assets/98972385/ced1313b-b24a-4406-840b-00bf7e7ac981">
<br><br>

- Vertex
  - Process P = {P1, P2, ... , Pn}
  - Resource R = {R1, R2, ... , Rn}
- Edge
  - Request edge Pi -> Rj
  - Assignment edge Rj -> Pi
<br><bR>

### ❗️ Deadlock 판단 방법

<img width="650" alt="image" src="https://github.com/junseoparkk/junseoparkk/assets/98972385/478ebee9-6e32-490c-8f6a-6306671dd48e">
<br><br>

- 그래프에 사이클이 없다면 deadlock 이 아님
- 그래프에 사이클이 있다면
  - 만약 자원마다 하나의 인스턴스만 있다면 deadlock
  - 만약 자원마다 여러 개의 인스턴스가 있다면 deadlock 가능성 존재
<br><br>
- 왼쪽 그래프 (Deadlock)
  - 사이클이 2개 존재
- 오른쪽 그래프 (Deadlock 아님)
  - 사이클이 있지만 여러 개의 인스턴스가 존재
  - P2, P4 에 의해 사이클이 없어질 수 있기 때문에 deadlock 이 아님
<br><br>

## 3️⃣ Deadlock 처리 방법

### 1) Deadlock Prevention
- 자원 할당 시 Deadlock 의 4가지 필요 조건 중 어느 하나가 만족되지 않도록 하는 것
- Deadlock 에 대한 원천적인 문제를 방지할 수 있지만, 몇몇 문제가 발생할 수 있음
  - Resource Utilization (자원 이용률) 저하
  - Throughput (성능) 감소
  - starvation 문제
<br>

### 🔎 4가지 필요 조건
#### 1. Mutual Exclusion
- 공유해서는 안되는 자원의 경우 반드시 성립해야 함

#### 2. Hold and Wait
- 프로세스가 자원을 요청할 때 다른 어떤 자원도 가지고 있지 않아야 함
  - `방법 1` : 프로세스 시작 시 모든 필요 자원을 할당받게 하는 방법
  - `방법 2` : 자원이 필요할 경우 보유 자원을 모두 놓고 다시 요청

#### 3. No Preemption
- 프로세스가 어떤 자원을 기다려야 하는 경우 이미 보유한 자원이 선점됨
- 모든 필요 자원을 얻을 수 있을 때 해당 프로세스는 다시 시작
- 상태를 쉽게 저장하고 복원할 수 있는 자원에서 주로 사용 (CPU, memory)

#### 4. Circular Wait
- 모든 자원 유형에 할당 순서를 정해 정해진 순서대로만 자원을 할당
- ex) 순서가 3인 자원 Ri를 보유한 프로세스가 순서가 1인 자원 Rj를 할당받기 위해선 먼저 Ri을 릴리즈해야 함
<br><br>

---
### 2) Deadlock Avoidance
- 자원 요청에 대한 부가적인 정보를 이용해 deadlock 의 가능성이 없는 경우에만 자원을 할당
- 프로세스가 시작될 때 프로세스의 최대 자원 사용량을 알고 있다고 가정하여 deadlock 을 방지
- 시스템 state 가 원래 state 로 돌아올 수 있는 경우에만 자원을 할당
- 따라서 항상 safe 한 상태를 유지

### 🔎 safe state
- 시스템 내의 프로세스들에 대한 `safe sequence` 가 존재하는 상태

### 🔎 safe sequence
- 프로세스의 sequence <P1, P2, ... , Pn> 이 safe 하려면 Pi (1<= i <=n) 의 자원 요청이 `가용 자원 + 모든 Pj (j < i)의 보유 자원` 에 의해 충족되어야 함
- 조건을 만족하면 다음 방법으로 모든 프로세스의 수행을 보장
  - Pi 의 자원 요청이 즉시 충족될 수 없으면 모든 Pj (j < i) 가 종료될 때까지 대기
  - Pi-1 이 종료되면 Pi의 자원요청을 만족시켜 수행

### ❗️ 자원당 인스턴스의 개수에 따른 방법
- 단일 인스턴스 : 자원 할당 그래프 사용
- 복수 인스턴스 : Banker's 알고리즘 사용 
<br><br>

#### i) 자원 할당 그래프
<img width="650" alt="image" src="https://github.com/junseoparkk/junseoparkk/assets/98972385/5ccd7383-44d9-47eb-8a95-963ed3eaf365">
<br><br>

- Claim edge Pi -> Rj
  - 프로세스 Pi 가 자원 Rj 를 미래에 요청할 수 있음을 뜻함 (점선으로 표시)
  - 프로세스가 해당 자원 요청 시 request edge 로 바뀜 (점선 -> 실선)
  - Rj 가 release 되면 assignment edge 는 다시 claim edge 로 바뀜
- request edge -> assignment edge 변경시 점선을 포함해 cycle 이 생기지 않는 경우에만 요청 자원을 할당
- Cycle 생성 여부 조사시 프로세스 수가 n 일 때 O(n^2) 시간이 걸림
<br><br>

#### ii) Banker's 알고리즘
<img width="700" alt="image" src="https://github.com/junseoparkk/junseoparkk/assets/98972385/3f815eb0-b29d-4f8e-8919-9be1a500f9b2">
<br><br>

- 요청 가능한 최대 자원의 양을 미리 계산
- 최대 요청 자원이 현재 가용 자원만으로도 충족이 되는지 확인
  - 충족이 된다면 : 요청을 받아들임
  - 충족이 안된다면 : 요청을 받아들이지 않음
- 모든 프로세스가 한꺼번에 최대 요청을 하더라도 deadlock 이 발생하지 않는 요청만 받아들임
- 예시
  - P0 의 경우 무조건 받아들이지 않음
  - P1 의 경우 가용 자원만으로도 충족되기에 받아들임

---

### 3) Deadlock Detection and recovery
- Deadlock 발생은 허용, 그에 대한 탐지 루틴을 두어 deadlock 발견시 recover
<br><br>

<img width="700" alt="image" src="https://github.com/junseoparkk/junseoparkk/assets/98972385/d7d5b1c0-e8b9-47a2-8af0-949e390e4d6e">
<br><br>

### 자원당 인스턴스가 하나일 때

#### i) Deadlock Detection
- Resource type 당 single instance 인 경우
  - 자원할당 그래프에서의 cycle 이 곧 deadlock 을 의미
- Resource type 당 multiple instance 인 경우
  - Banker's 알고리즘과 유사한 방법 활용
  - Table 을 그려서 판단
<br><br>

#### ii) Wait-for graph 알고리즘
- Resource type 당 single instance 인 경우
- Wait-for graph
  - 자원할당 그래프의 변형
  - 프로세스만으로 노드 구성
  - Pj 가 가지고 있는 자원을 Pk 가 기다리는 경우 Pk -> Pj 로 표시
- 알고리즘
  - Wait-for graph 에 사이클이 존재하는지를 주기적으로 조사
  - O(n^2)
    - 사이클을 찾는 오버헤드 정도
    - 프로세스 n 개가 있을 때 점선은 최대 n x (n - 1) 개
<br><br>

### 자원당 인스턴스가 여러 개일 때

<img width="700" alt="image" src="https://github.com/junseoparkk/junseoparkk/assets/98972385/35f92a68-cd6c-48a8-99dc-25ae88278db7">
<br><br>

- 프로세스가 요청을 하면 일단 받지만, 가용 자원이 없다면 요청을 받지 않는다.
- Deadlock 인지 아닌지 판단하기 위해서 요청한 자원이 없는 프로세스 유무를 파악
  - 만약 요청한 자원이 없다면 이후 프로세스는 자원을 반납할 것이라고 가정
  - ex) P0, P2 는 자원을 반납할 것이라고 가정
  - 가용 자원으로 처리 가능한 요청을 확인 -> 요청 자원이 없는 프로세스의 자원을 확보 -> 처리 가능한 요청이 있다면 반납한 자원을 채우면서 문제가 생기는지 확인
<br><br>

### Deadlock Detection and Recovery
- Deadlock 이 발견되면 recovery 를 해야 함
- 크게 2가지 방법

1. Process termination
   - Deadlock 과 관련된 모든 프로세스를 한꺼번에 죽임
   - Deadlock 이 없어질 때까지 Deadlock 과 관련된 모든 프로세스를 하나씩 죽임
2. Resource Preemption
   - Deadlock 과 관련된 프로세스로부터 자원을 빼앗음
   - 비용을 최소화할 희생 프로세스를 선정
   - safe state 로 롤백하여 프로세스를 재시작하면 deadlock 이 없어질 수 있음
   - Starvation 문제
     - 동일한 프로세스가 계속해서 희생 프로세스로 선정되는 경우
     - cost factor 에 롤백 횟수도 같이 고려해야 함

---

### 4) Deadlock Ignorance
- Deadlock 을 시스템이 책임지지 않음
- Deadlock 이 매우 드물게 발생하므로 조치 자체가 더 큰 오버헤드가 될 수 있음
- 만약 시스템에 deadlock 이 발생한 경우 시스템이 비정상적으로 작동하는 것을 사람이 느낀 후 직접 프로세스를 죽이는 등의 방법으로 대처
- UNIX 를 포함한 대부분의 OS 가 채택
---
references<br>
KOCW 반효경-Introduction to Operating Systems