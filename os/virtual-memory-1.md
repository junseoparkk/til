# Virtual Memory - 1

## 1️⃣ Demand Paging

### 1) Demand Paging
- 실제로 필요한 시점에 page를 메모리에 올리는 것 (요청이 있을 때 메모리에 올림)
- 효과
  - I/O 양의 감소
  - 물리적 메모리 사용량 감소
  - 멀티 프로그래밍 환경에서 빠른 응답 시간을 기대
  - 더 많은 사용자 수용
<br><br>

- Valid / Invalid bit 사용
  - paging 기법에서 각 page table entry 마다 사용되는 것
    - 당장 필요한 page는 demand paging에 의해 물리적 메모리에 올라감
    - 당장 필요 없는 page는 swap area에 내려감
  - Invalid 의미
    - 사용되지 않는 주소 영역인 경우
    - 물리적 메모리에 page가 없는 경우
  - 처음에는 모든 page entry가 'Invalid'로 초기화
  - 주소 변환시 invalid bit이 준비되어있으면 `page fault`
    - `page fault` : 요청한 페이지가 물리 메모리에 존재하지 않음 
<br><br>
---
### 2) 메모리에 없는 Page의 Page Table
<img width="600" alt="image" src="https://github.com/junseoparkk/kakao-cloud-school/assets/98972385/d0040a4f-ce50-4f50-a913-41a938a17026">
<br><br>

- page table entry는 주소 공간의 크기 만큼 만들어지고, invalid로 표시
- 실제로 프로그램을 구성하는 page는 1~5
- invalid 로 표시된 1, 3, 4 page는 물리 메모리가 아니라 swap area에 내려가있음
- 6,7은 사용되지 않는 페이지이지만 주소 공간만큼 지원받기 때문에 존재
<br><br>

### 🔎 예시)
1. CPU가 1번 page에 접근 시도
2. 주소 변환을 위해 page table 확인 -> 1번 page는 invalid -> 물리 메모리에 없음
3. 해당 page 를 디스크로부터 물리 메모리에 올려야 함 (I/O 작업) -> 사용자 프로세스가 못함
4. invalid page 에 대해 'page fault' 발생 -> CPU는 자동적으로 운영체제로 넘어감
5. 운영체제가 CPU를 통해 page fault난 page를 메모리에 올림
---

### Page Fault
<img width="600" alt="image" src="https://github.com/junseoparkk/kakao-cloud-school/assets/98972385/453428d7-e986-40c8-83bd-9f4eecad59f0">
<br><br>

- invalid page에 접근하면 MMU가 trap을 발생 시킴 -> 'page fault trap'
- 커널 모드로 들어가서 'page fault handler'가 invoke
- page fault 처리 순서
    1. invalid reference => abort process
    2. 빈 page frame을 가져옴 (만약 없다면 뺏어옴)
    3. 해당 page를 disk에서 메모리로 읽어옴
       1. disk I/O가 끝나기까지 해당 프로세스는 CPU를 preempt당함 (block)
       2. Disk 읽기 작업이 끝나면 page tables entry 기록, valid/invalid bit = 'valid'
       3. ready queue에 프로세스를 넣음 -> dispatch later
    4. 해당 프로세스가 CPU를 잡고 다시 실행
    5. 중단되었던 인스트럭션 재개
---

### Performance of Demand Paging
- Page fault가 났을 때 Disk에 접근하는 작업은 굉장히 오래 걸리는 작업
  - Page fault가 얼마나 나느냐에 따라 메모리 접근 시간이 좌우됨
<br><br>
- Page Fault Rate 0 <= p <= 1.0
  - `p=0`: page faults (X)
  - `p=1`: 모든 reference가 fault
- Effective Access Time
  - (1-p) x memory access + (OS & HW page fault overhead) + [필요시 swap page out] + swap page in + OS & HW restart overhead
---

### Free Frame이 없는 경우 (빈 fram이 없을 때)
- Page replacement
  - 어떤 frame을 빼앗아올지 결정해야 함 
  - 바로 사용하지 않을 page를 쫓아내는 것이 효율적 (어떤 frame을 Disk로 쫓아낼지)
  - 동일한 page가 메모리에서 여러 번 쫓겨났다가 다시 들어올 수 있음
- Replacement Algorithm
  - page-fault rate을 최소화하는 것이 목표
  - 알고리즘 평가 척도
    - 주어진 page reference string에 대해 page fault를 얼마나 내는지를 조사
    - ex) `1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5`
<br><br>

### 🔎 만약 디스크에서 메모리에 올라온 이후 내용이 변경된다면?
- 변경 사항 존재
  - Disk로 쫓아낼 때 변경 사항까지 함께 저장하여 보냄
- 변경 사항 없음
  - 물리 메모리에서 지워버리면 됨
<br><br>

## 2️⃣ Replacement Algorithm

### 1) Optimal Algorithm
<img width="600" alt="image" src="https://github.com/junseoparkk/kakao-cloud-school/assets/98972385/be537012-9059-4000-a937-40c7761598e4">
<br><br>

- page fault를 가장 적게 나게 하는 알고리즘 (최소값)
- 미래에 참조되는 page를 미리 안다고 가정
  - 'Offline Algorithm'이라고도 불림
  - 실제 시스템에서 사용하는 것은 불가하지만, 다른 알고리즘의 upper bound를 제공 (참고로 사용)
- MIN (OPT) : `가장 먼 미래에 참조되는 page를 replace`
<br><br>
---

### 2) FIFO (Fist In First Out) Algorithm
<img width="600" alt="image" src="https://github.com/junseoparkk/kakao-cloud-school/assets/98972385/fbea786f-e369-4975-8e1d-ca1703e0f453">
<br><br>

- FIFO : `먼저 들어온 것을 먼저 지움`
- 메모리 크기를 늘리면 보통 성능이 좋아야하지만, 해당 알고리즘은 page fault가 더 많이 발생
- FIFO Anomaly
  - more frames => more page faults
<br><br>
---

### 3) LRU (Least Recently Used) Algorithm
<img width="600" alt="image" src="https://github.com/junseoparkk/kakao-cloud-school/assets/98972385/8f60c9df-acce-4f3e-8aed-11e6e6db19b5">
<br><br>

- LRU : `가장 오래 전에 참조된 것을 지움`
- "미래를 모르면 과거를 알라" - ㄷ명언...
---

### 4) LFU (Least Frequently Used) Algorithm
- LFU : `참조 횟수(reference count)가 가장 적은 페이지를 지움`
- 최저 참조 횟수 page가 여러 개인 경우
  - LFU 알고리즘 자체에서는 여러 page 중 임의로 선정
  - 성능 향상을 위해 가장 오래 전에 참조된 page를 지우게 구현 가능
- 장단점
  - LRU 처럼 직전 참조 시점만 보지 않고 장기적인 시간 규모를 보기 때문에 page 인기도를 더 정확히 반영 가능
  - 참조 시점의 최근성 반영 못함
  - LRU 보다 구현이 복잡합
<br><br>

### 예시)
<img width="600" alt="image" src="https://github.com/junseoparkk/kakao-cloud-school/assets/98972385/2a4d0421-6fae-45bf-ac47-def1fbe071d4">
---

### 5) LRU, LFU 알고리즘 구현
- LRU : Linked List로 구현 가능
  - 참조된 시간 순서대로 나열
  - 맨 위 : 가장 오래된 page, 맨 아래 : 가장 최근에 참조된 page
  - 가장 최근에 참조된 page는 맨 아래로 보내면 됨 (가장 가치가 높다)
  - 가장 마지막에 참조된 page를 쫓아내면 됨 (맨 위)
  - 맨 위, 아래에서 연산 -> 비교할 필요가 없음
    - O(1)
- LFU : 
  - 맨 위 : 가장 참조 횟수가 적은 page, 맨 아래 : 가장 참조 횟수가 많은 page
  - 사실상 한 줄로 나열하는 것이 불가능
  - 하나하나 참조 횟수를 비교를 해서 어디까지 내려갈 수 있는지 확인해야 함
    - O(n)
    - Heap을 통해 구현하면 O(log n)으로 구현 가능

---
references<br>
KOCW 반효경-Introduction to Operating Systems