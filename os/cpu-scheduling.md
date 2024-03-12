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

- 1, 4에서의 스케줄링 : nonpreemptive (= 강제로 빼앗지 않고 자진 반납)
- 나머지 스케줄링 : preemptive (= 강제로 빼앗음)
