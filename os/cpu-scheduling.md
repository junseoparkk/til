# CPU Scheduling

## 1️⃣ CPU & I/O Burst

![image](https://github.com/junseoparkk/til/assets/98972385/28e46df3-8c70-44a7-9c19-8f9a97c2ea6a)

1. CPU 에서 인스트럭션을 실행
    - load store
    - add store
    - read from file (I/O 작업)
2. I/O 작업
    - I/O 작업이 진행되는 동안 CPU를 놓음
3. I/O 작업 종료
    - ready 상태가 되어 CPU 사용을 대기
    - store increment
    - index
    - write to file (I/O 작업)
4. 이후 반복
<br><br>

### 1) CPU Burst
- 프로세스가 CPU를 사용하여 실제 작업을 수행하는 시간
- CPU를 사용하여 계산을 수행하거나 연산을 처리하는 등의 작업을 의미
- ex) 정렬 알고리즘 실행, 숫자 계산 등
<br><br>

### 2) I/O Burst
- 프로세스가 I/O 장치와 상호작용하여 데이터를 읽거나 쓰는 시간
- 디스크나 네트워크와 같은 외부 장치와의 상호작용에 의해 발생
- ex) 파일 읽기 쓰기, 네트워크로 데이터 전송 등
<br><br>

### 3) CPU-burst Time

![image](https://github.com/junseoparkk/til/assets/98972385/f14d97df-f021-4c80-8927-1a55cd88ef91)

- 여러 종류의 job(=process)이 섞여 있기 때문에 CPU 스케줄링이 필요
- Interactive job 에게 적절한 response 제공 요망
- CPU 와 I/O 장치 등 시스템 자원을 골고루 효율적으로 사용
- 가능하면 사람과 상호작용하는 job에게 CPU를 우선적으로 주는 것이 필요
- 공평한 것보단 효율적인 것, Interactive job 의 대기시간을 줄여주는 것
<br><br>

### 4) 프로세스 특성 분류

1. I/O-bound process
    - CPU를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한 job
    - many short CPU bursts
2. CPU-bound process
    - 계산 위주의 job
    - few very long CPU bursts
<br><br> 

### 5) CPU Scheduler & Dispatcher
- CPU Scheduler
    - Ready 상태의 프로세스 중에서 CPU를 줄 프로세스를 고른다
    - 운영체제 안에서 CPU 스케줄링을 하는 코드를 말함 (하드웨어나 소프트웨어 x)
- Dispatcher
    - CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘긴다
    - 해당 과정을 'context switch(문맥 교환)' 이라고 부름
<br><br>
### 🔎 CPU 스케줄링이 필요한 경우
1. Running -> Blocked    
   - ex) I/O 요청하는 시스템 콜
2. Running -> Ready
   - ex) 할당시간 만료로 Timer interrupt
3. Blocked -> Ready
   - ex) I/O 완료 후 interrupt
4. Terminate
<br>

### ❗️비선점형 vs 선점형
- 1, 4에서의 스케줄링 : nonpreemptive (= 강제로 빼앗지 않고 자진 반납, 비선점형)
- 나머지 스케줄링 : preemptive (= 강제로 빼앗음, 선점형)
<br><br>

## 2️⃣ Scheduling Criteria
- Performance Index (= Performance Measure, 성능 척도)
- 스케줄링 알고리즘을 평가할 수 있는 성능 척도
<br><br>

### 1) 시스템 입장에서의 성능 척도
- CPU 하나로 얼마나 처리할 수 있는가?
  - CPU utilization (이용률) : 주어진 시간 내에 작업을 얼마나 많이 했는지
  - Throughput (처리량) : 주어진 시간 내에 작업을 얼마나 완료했는지
### 2)프로세스 입장에서의 성능 척도
- 얼마나 빨리 처리할 수 있는가?
  - Turnaround Time (소요 시간, 반환 시간) : CPU를 사용하고 반환할 때까지 걸린 시간
  - Waiting Time (대기 시간) : Ready queue에서 기다린 시간
  - Response Time (응답 시간) : Ready queue에 들어와 처음 CPU를 얻을 때까지 걸린 시간, 시분할 환경에서는 처음 CPU를 얻는 시간이 사용자 응답과 연관이 있기 때문에 개념을 분리
<br>

### 🔎 Waiting Time vs Response Time
- 선점형 스케줄링의 경우 CPU를 얻어도 끝까지 사용하는 것이 아님
- 한 번의 CPU Burst에도 CPU를 얻고 뻇기는 과정이 여러 번 반복됨 
  - Waiting Time : Ready queue에 줄서서 대기한 시간을 모두 합한 것
  - Response Time : 최초의 CPU를 얻기까지 걸린 시간

### 🔎 음식점 비유
1. 가게 입장에서의 성능 척도 (= 시스템)
    - CPU utilization : 주인 입장에서 주방장이 쉬지 않고 일한 시간의 비율이 높으면 좋음
    - Throughput : 단위 시간당 방문하여 음식을 먹고 나간 손님의 수가 많으면 좋음
2. 손님 입장에서의 성능 척도 (= 프로세스)
    - Turnaroun Time : 음식점에 들어와 주문하고 식사가 끝난 뒤 나갈때까지 걸린 시간 (대기 시간 + 식사 시간)
    - Waiting Time : 손님이 대기한 시간
    - Response Time : 손님이 첫 음식이 나올때까지 대기한 시간
<br><br>

## 3️⃣ Scheduling Algorithms

### 1) FCFS (First-Come First-Served)
- 비선점형 스케줄링 방식
- 프로세스 도착 순서대로 처리하는 방법
- Burst Time이 긴 프로세스가 있다면 비효율적일 수 있음
- `Convoy effect` : queue 에서 오랜 시간 기다려야하는 경우
<br><br>

### ❗️ 예시

<img width="350" alt="image" src="https://github.com/junseoparkk/crud-board/assets/98972385/a35dc0a4-99a3-47f7-b44c-9581af0b17b7">


|Process|Arrival Time|Burst Time|
|:---:|:---:|:---:|
|P1|0.0|24|
|P2|0.0|3|
|P3|0.0|3|

프로세스 도착 순서 : P1 -> P2 -> P3
- Waiting Time : P1 = 0, P2 = 24, P3 = 27
- Average Waiting Time : (0 + 24 + 27) / 3 = 17
<br><br>

---
### 2) SJF (Shortest - Job - First)
- 각 프로세스의 다음번 CPU burst time을 가지고 스케줄링에 활용
- CPU burst time이 가장 짧은 프로세스를 가장 먼저 스케줄링하는 방법
- 주어진 프로세스들에 대해 최소 평균 대기 시간 (minimum average waiting time)을 보장 => preemptive 방식
<br>

1. Nonpreemptive
    - 일단 CPU를 잡으면 이전 CPU burst가 완료될 때까지 CPU를 선점당하지 않음
    - 즉, 더 짧은 burst time을 가진 프로세스가 도착해도 현재 사용하는 프로세스의 CPU 사용을 보장 
2. Preemptive
    - 현재 수행중인 프로세스의 남은 burst time 보다 더 짧은 CPU burst time을 가지는 프로세스가 도착하면 CPU를 빼앗김
    - 해당 방법을 `SRTF, Shortest-Remaining-Time-First` 라고도 부름
<br><br>

### ❗️ 예시

|Process|Arrival Time|Burst Time|
|:---:|:---:|:---:|
|P1|0.0|7|
|P2|2.0|4|
|P3|4.0|1|
|P4|5.0|4|

### 1. Non-Preemptive
<img width="350" alt="image" src="https://github.com/junseoparkk/crud-board/assets/98972385/d97a5659-32fc-4ddb-977c-36b22fea8f71">
<br><br>

- 0.0초에 도착한 프로세스는 P1밖에 없으므로 P1이 CPU를 얻음
- 7초 뒤 P2 ~ P4까지 모두 도착
- 다음으로 burst time이 가장 짧은 P3가 CPU를 얻음
- 즉, P1 -> P3 -> P2 -> P4 순서로 CPU를 얻음
- Average Waiting Time = (0 + 3 + 6 + 7) / 4 = 4
<br><br>

### 2. Preemptive
<img width="350" alt="image" src="https://github.com/junseoparkk/crud-board/assets/98972385/59b08e30-39cf-47df-9bd4-9579e11832e5">
<br><br>

- 0.0초에 도착한 프로세스는 P1밖에 없으므로 P1이 CPU를 받음
- 2.0초에 P2가 도착, P1보다 burst time이 짧은 P2에게 CPU를 넘김 `(P1 : 5 > P2 : 4)`
- 4.0초에 P3가 도착, P2보다 burst time이 짧은 P3에게 CPU를 넘김
- 즉, P1 -> P2 -> P3 -> P2 -> P4 -> P1 순서로 CPU를 얻음
- Average Waiting Time = (9 + 1 + 0 + 2) / 4 = 3
<br><br>

### 🔎 문제점
1. Starvation (기아 현상)
    - burst time 이 길어 우선 순위가 낮은 프로세스는 실행하지 못할 수도 있음
2. 다음 CPU burst time
    - 다음 CPU burst time 을 정확하게 알 수 없음
    - 과거의 CPU burst time 을 이용하여 추정만 가능 => `Exponential Averaging` 수식 활용
      - 현재와 가까울 경우 높은 가중치, 과거와 가까울 경우 낮은 가중치
<br><br>

---
### 3) Priority Scheduling
- 우선순위가 가장 높은 프로세스에게 CPU를 할당하는 방법
  - 우선순위는 정수로 주어짐 (정수가 낮을수록 우선순위가 높음)
    - Preemptive
    - Non-Preemptive
- SJF는 일종의 priority scheduling
- 문제점
  - Starvation (기아현상) : 우선순위가 낮은 프로세스는 실행하지 못할 수도 있음
- 해결책
  - Aging (노화) : 우선순위가 낮더라도 오래 기다린다면 우선순위를 높이는 기법
<br><br>

---
### 4) Round Robin (RR)
- 각 프로세스는 동일한 크기의 할당 시간(time quantum)을 가짐 (일반적으로 10-100 m/s)
- 할당 시간이 지나면 프로세스는 선점당하고 ready queue의 맨 뒤로 가서 다시 대기
- n개의 프로세스가 ready queue에 있고, 할당 시간이 q time unit인 경우 각 프로세스는 최대 q time unit 단위로 CPU 시간의 1 / n을 얻음
  - 어떤 프로세스도 (n - 1) / q time unit 이상 기다리지 않음
  - 적어도 해당 시간 이후엔 차례가 돌아옴
- 응답 시간이 매우 빨라진다는 것이 장점
- 성능
  - q large = FCFS
  - q small = context switch 오버헤드가 커짐
  - 적당한 time quantum을 설정해야함
  - `일반적으로 SJF 보다 average turnaround time이 길지만, response time은 더 짧음`
<br><br>

---
### 5) Multilevel Queue

<img width="600" alt="image" src="https://github.com/junseoparkk/crud-board/assets/98972385/0ca3c315-59b0-44bd-b744-570ccf124c3b">
<br><br>

- Ready queue 를 여러 개로 분할
  - foreground (interactive) : 상호작용
  - background (batch) : CPU만 오랫동안 사용
- 각 큐는 독립적인 스케줄링 알고리즘을 가짐
  - foreground : RR
  - background : FCFS
- 큐에 대한 스케줄링이 필요
  - Fixed priority scheduling
  - Time slice
<br><br>

---
### 6) Multilevel Feedback Queue

<img width="600" alt="image" src="https://github.com/junseoparkk/crud-board/assets/98972385/e348b375-1663-4007-8c67-970b43187775">
<br><br>

- 프로세스가 다른 큐로 이동 가능
- 'Aging'을 이와 같은 방식으로 구현할 수 있음
- `Multilevel-feedback-queue scheduler` 를 정의하는 파라미터
  - Queue 의 수
  - 각 큐의 스케줄링 알고리즘
  - 프로세스를 상위 큐로 보내는 기준
  - 프로세스를 하위 큐로 내쫓는 기준
  - 프로세스가 CPU 서비스를 받으려 할 때 들어갈 큐를 결정하는 기준

### ❗️ 예시
- Three queues
    - Q0 : time quantum 8 m/s
    - Q1 : time quantum 16 m/s
    - Q2 : FCFS
- Scheduling
  - new job 이 Q0 큐로 들어감
  - CPU를 잡아서 할당 시간인 8m/s 동안 수행
  - 8m/s 동안 다 끝내지 못했다면 Q1 큐로 내려감
  - Q1에 줄서서 기다렸다가 CPU를 잡아 16m/s 동안 수행
  - 16m/s 동안 다 끝내지 못했다면 Q2 큐로 내려감 (가장 낮은 우선순위)
<br><br>

## 4️⃣ Special Case Scheduling

### 1) Multiple - Processor Scheduling
- CPU가 여러 개인 경우 스케줄링은 더욱 복잡해짐
- Homogeneous processor인 경우
  - Queue에 한줄로 세워서 각 프로세서가 알아서 꺼내가게 할 수 있음
  - 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우 문제가 복잡해짐
- Load sharing
  - 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 메커니즘이 필요
  - 별개의 큐를 두는 방법 vs 공동 큐를 사용하는 방법
- Symmetric Multiprocessing (SMP)
  - 각 프로세서가 각자 알아서 스케줄링 방법을 결정
- Asymmetric Multiprocessing
  - 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고, 나머지 프로세서는 이를 따름
<br><br>

### 2) Real - Time Scheduling
- Hard real - time systems
  - Hard real - time task는 정해진 시간 안에 반드시 끝내도록 스케줄링해야 함
- Soft real - time systems
  - Soft real - time task는 일반 프로세스에 비해 높은 우선순위를 갖도록 해야 함
<br><br>

### 3) Thread Scheduling
- Local Scheduling
  - User level thread 의 경우 사용자 수준의 thread library에 의해 어떤 thread를 스케줄할지 결정
- Global Scheduling
  - Kernel level thread 의 경우 일반 프로세스와 마찬가지로 커널의 단기 스케줄러가 어떤 thread를 스케줄할지 결정
<br><br>

## 5️⃣ Algorithm Evaluation

### 1) 어떤 알고리즘이 좋은지 평가하는 방법
1. Queueing models
    - 확률 분포로 주어지는 `arrival rate(도착율)` 와(과) `service rate(처리율)` 등을 통해 각종 performance index 값을 계산
2. Implementation (구현) & Measurement (성능 측정)
    - 실제 시스템에 알고리즘을 구현하여 실제 작업에 대해 성능을 측정 비교
3. Simulation (모의 실험)
    - 알고리즘을 모의 프로그램으로 작성 후 trace를 입력으로 하여 결과 비교

---
#### references
- ABRAHAM SILBERSCHATZ ET AL., OPERATING SYSTEM CONCEPTS, NINTH EDITION, WILEY, 2013
- 반효경, 운영체제와 정보기술의 원리, 이화여자대학교 출판부, 2008