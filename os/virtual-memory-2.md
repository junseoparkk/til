# Virtual Memory - 2

## 1️⃣ Caching (캐싱)

### 1) 다양한 캐싱 환경
- 캐싱 기법
  - 한정된 빠른 공간(캐시)에 요청된 데이터를 저장해 두었다가 요청시 캐시로부터 직접 서비스하는 방식
  - paging system 외에도 'cache memory', 'buffer caching', 'web caching' 등 
- 캐시 운영의 시간 제약
  - 교체 알고리즘에서 삭제할 항목을 결정하는 일에 지나치게 많은 시간이 걸릴 경우 실제 시스템에서 사용 불가
  - Buffer caching이나 Web caching의 경우
    - O(1) ~ O(log n) 정도까지 허용
  - Paging system인 경우
    - page fault인 경우에만 OS가 관여
    - 페이지가 이미 메모리에 존재할 경우 참조시각 등 정보를 OS가 알 수 없음
      - page fault나 날 때만 알 수 있음
    - O(1)인 LRU의 리스트 조작조차 불가능
---

### 2) Paging System에서 LRU, LFU 가능 여부

<img width="600" alt="image" src="https://github.com/junseoparkk/kakao-cloud-school/assets/98972385/66699267-344d-42f9-9418-0131ccd0dfe3">
<br><br>

- 결론적으로 CPU는 가장 오래 전에 참조된 page, 참조 횟수가 가장 적은 page를 알 수 없음
  - 이미 물리 메모리에 올라가있는 경우 CPU 개입이 없기 때문에 HW에서 주소 변환을 하기 때문에 OS는 접근 시간을 알 수 없음
  - page fault가 나면 CPU 제어권이 OS로 넘어가고, 디스크의 page가 메모리에 올라온 시간을 알 수 있음
- LRU, LFU는 리스트를 조작해야 함
  - OS가 자료구조를 유지하면서 CPU를 갖고 인스트럭션을 통해 page의 위치를 옮기는 작업을 해야 함
  - 따라서 Virtual Memory에서는 LRU, LFU 사용할 수 없음
  - buffer caching, web caching에서는 사용할 수 있음
- Paging System에서는 Clock Algorithm을 사용
---

### 3) Clock Algorithm
- Clock Algorithm
  - LRU의 근사 알고리즘으로, 여러 명칭으로 불림
    - Second chance algorithm
    - NUR (Not Used Recently) 또는 NRU (Not Recently Used)
  - Reference bit
    - 주소 변환을 해주는 HW가 page에 접근했을 때 valid여서 CPU로 읽어갈 때 1로 세팅하는 값 -> page가 참조되었다는 것을 표시
  - Reference bit이 0인 것을 찾을 때까지 포인터를 하나씩 앞으로 이동
  - 포인터를 이동하는 중에 reference bit이 1이라면 모두 0으로 바꿈
  - 한 바퀴 돌아와서도 0이라면 replace 당함
  - 자주 사용되는 page라면 second chance가 올 때 1
- Clock Algorithm의 개선
  - reference bit과 modified bit (dirty bit)을 함께 사용
  - reference bit = 1 : 최근에 참조된 페이지
  - modified bit = 1 : 최근에 변경된 페이지 (I/O를 동반하는 페이지)
    - 'write'가 발생하면 1로 변경
    - 0이라면 메모리에 올라온 뒤로 'write'연산이 발생하지 않았던 것
    - 가능하면 0인 것을 우선으로 쫓아낸다면 디스크에 해당 page를 작성하지 않아도 되기에 조금 더 빠를 수 있음
<br><br>

<img width="600" alt="image" src="https://github.com/junseoparkk/kakao-cloud-school/assets/98972385/9a22a19b-1dce-4984-b583-a69a813fbf14">
<br><br>

- Paging System에서 일반적으로 사용되는 알고리즘
- 각각의 사각형이 물리 메모리에 들어있는 page
- 만약 page fault가 나서 OS가 특정 page를 쫓아내야 한다면, HW가 세팅해놓은 reference bit을 참조함
  - 1인 경우 : 최근에 참조되었다는 것을 의미 (쫓아내지 않고 bit를 0으로 바꾼 뒤 다음 page를 쫒아냄)
    - 시계 바늘이 한 바퀴 돌아오는 동안 참조가 있었다는 것을 의미
  - 0인 경우 : 시계 바늘이 한 바퀴 돌아오는 동안 참조가 없었다는 것을 의미
- LRU와 완전히 똑같진 않지만, 한 바퀴를 돌면서 참조되지 않은 page를 찾기 때문에 오랫동안 참조되지 않은 page를 어느 정도 알 수 있음
<br><br>

## 2️⃣ Allocation

### 1) Page Frame의 Allocation
- Allocation Problem : 각 프로세스에 얼마만큼의 page frame을 할당할 것인가?
- Allocation의 필요성
  - 메모리 참조 명령어 수행시 명령어와 데이터 등 여러 page를 동시에 참조
    - 명령어 수행을 위해 최소한으로 할당되어야 하는 frame 수가 있음
  - Loop를 구성하는 page들은 한꺼번에 allocation 되는 것이 유리함
    - 최소한의 allocation이 없으면 매 loop 마다 page fault
- Allocation Schema
  - Equal allocation : 모든 프로세스에 똑같은 갯수 할당
  - Proportional allocation : 프로세스 크기에 비례하여 할당
  - Priority allocation : 프로세스의 우선순위에 따라 다르게 할당
---

### 2) Global vs. Local Replacement
- Global replacement
  - 교체시 다른 프로세스에 할당된 frame을 빼앗아 올 수 있다
  - 프로세스별 할당량을 조절하는 또 다른 방법
  - FIFO, LRU, LFU 등의 알고리즘을 global replacement로 사용시에 해당
  - Working set, PFF 알고리즘 사용
- Local replacement
  - 자신에게 할당된 frame 내에서만 교체
  - FIFO, LRU, LFU 등의 알고리즘을 프로세스별로 운영시
<br><br>

## 3️⃣ Thrashing

<img width="600" alt="image" src="https://github.com/junseoparkk/kakao-cloud-school/assets/98972385/4d31606a-5ed7-4958-ad9f-23d5a5ec685d">
<br>

### 1) Thrashing
- 프로세스의 원활한 수행에 필요한 최소한의 page frame 수를 할당받지 못한 경우 발생
- page fault rate이 매우 높아짐
- CPU utilization이 낮아짐
- OS는 MPD (Multiprogramming degree)를 높여야 한다고 판단
- 또다른 프로세스가 시스템에 추가됨
- 프로세스당 할당된 frame의 수가 더욱 감소
- 프로세스는 page의 swap in/swap out으로 매우 바쁨
- 대부분의 시간에 CPU는 한가함
---

### 2) Working-Set Model
- Locality of reference
  - 프로세스는 특정 시간 동안 일정 장소만을 집중적으로 참조하는 특징
  - 집중적으로 참조되는 해당 page들의 집합을 'locality Set'이라고 함
- Working-set Model
  - Locality에 기반하여 프로세스가 일정 시간 동안 원활하게 수행되기 위해 한꺼번에 메모리에 올라와 있어야 하는 page들의 집합을 'Working Set'이라고 함
  - Working Set 모델에서는 프로세스의 working set 전체가 메모리에 올라와있어야 수행되고, 그렇지 않은 경우 모든 frame을 반납한 후 swap out (suspend)
  - Thrashing을 방지함
  - Multiprogramming degree를 결정함