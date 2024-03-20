# Process Synchronization

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