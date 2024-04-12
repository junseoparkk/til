# Memory Management

## 1️⃣ Memory (메모리)

### 1) Logical vs. Physical Address

1. Logical Address (= virtual address)
    - 프로세스마다 독립적으로 가지는 주소 공간 (논리 주소)
    - 각 프로세스마다 0번지부터 시작
    - `CPU 가 보는 주소가 논리 주소`
2. Physical Address
    - 메모리에 실제 올라가는 위치 (물리 주소)
<br><br>

#### ❗️Address Binding (주소 바인딩)
- 특정 프로그램이 메모리에 올라갈 때 해당 메모리의 주소를 결정하는 것
- Symbolic Add. -> Logical Add. -> Physical Add.
  - 프로그래머 입장에선 Symbolic add. 사용. 컴파일되면 논리 주소로 바뀜
  - 논리 주소가 언제, 어떻게 바뀌는가?
<br><br>

---
### 2) Address Binding (주소 바인딩)

1. Compile time binding
    - 물리적 메모리 주소가 컴파일시 정해짐
    - 시작 위치 변경시 재컴파일 (주소를 바꾸려면 재컴파일 해야함)
    - 컴파일러는 절대 코드 (absolute code) 생성
2. Load time binding
    - 프로그램 실행시 주소가 결정됨
    - Loader 의 책임하에 물리적 메모리 주소 부여
    - 컴파일러가 재배치 가능 코드 (relocatable code) 를 생성한 경우 가능
      - relocatable code 는 위치가 정해져있지 않고 동적으로 위치 지정 가능
3. Execution time binding (= Run time binding)
    - 프로그램 실행시 주소가 결정됨
    - 수행이 시작된 이후에도 프로세스의 메모리 상 위치 이동이 가능
    - CPU 가 주소를 참조할 때마다 바인딩을 점검
    - 하드웨어적인 지원이 필요
      - e.g) MMU 를 통해 주소를 변환
    - 현재 많이 사용하는 방법
<br><br>

<img width="800" alt="image" src="https://github.com/junseoparkk/junseoparkk/assets/98972385/9e0b0719-675d-4467-9239-7c8b9bc1f5b4">
<br><br>

### 🔎 왜 CPU 는 논리 주소를 바라보는가?
- CPU 는 하드웨어이기에 물리 주소를 바라볼 것 같지만, 그렇지 않음
- 컴파일된 실행파일에 각각 인스트럭션들이 존재
- 인스트럭션들이 실행되어 메모리에 올라가도 인스트럭션 코드 내의 주소는 바뀌지 않음
- 인스트럭션이 올라가는 위치는 바뀔 수 있지만, 코드가 들어있는 위치까지는 논리 주소로 남아 있음
- 따라서 CPU 가 어떤 인스트럭션을 실행하기 위해선 결국 논리 주소를 바라볼 수 밖에 없음
- 실행을 위해 주소 변환을 통해 물리 주소를 찾고, 해당 내용을 찾아 CPU 에게 전달
<br><br>

---
### 3) Memory - Management Unit (MMU)
- 프로그램이 메모리에 올라갈 때 통째로 올라간다고 가정 (현대 OSs는 필요한 부분만 올라감)

1. MMU
    - 논리 주소를 물리 주소로 매핑해주는 하드웨어 장치
2. MMU Scheme
    - 사용자 프로세스가 CPU 에서 수행되어 생성해내는 모든 주소값에 대해 `base register (= relocation register)` 의 값을 더함
3. User Program
    - 논리 주소만을 다룸
    - 실제 물리 주소를 볼 수 없으며, 알 필요가 없음
<br><br>

---
### 4) Dynamic Relocation

<img width="800" alt="image" src="https://github.com/junseoparkk/junseoparkk/assets/98972385/aaff4917-e44d-4c51-bec9-231c4c2afe0a">
<br><br>

- 2 개의 register 를 통해 MMU 가 동작
- relocation register 은 물리 주소에서 시작 위치를 갖고 있음
- limit register 은 프로그램의 최대 크기를 갖고 있음
  - 만약 악의적인 프로그램이라면 임의의 주소를 얻을 수 있음. 하지만 제한을 걸어놓는다면 이를 방지할 수 있음
- CPU 가 논리 주소로 요청을 하면, base register + 논리 주소를 통해 물리 주소를 얻게 됨
<br><br>

---
### 5) Hardware Support for Address Translation

<img width="700" alt="image" src="https://github.com/junseoparkk/junseoparkk/assets/98972385/003f65b9-ba82-47b4-bae6-dafc69a80b07">
<br><br>

- Relocation Register (= base register) : 접근할 수 있는 물리적 메모리 주소의 최소값
- Limit Register : 논리적 주소의 범위를 가짐으로써 운영체제 및 사용자 프로세스 간의 메모리를 보호할 수 있음
<br><br>

## 2️⃣ 여러 기술들

### 1) Dynamic Loading
- 프로세스 전체를 메모리에 미리 다 올리는 것이 아니라, 해당 루틴이 불려질 때마다 메모리에 올리는 것 (필요할 때)
- memory utilization 의 향상
- 가끔 사용되는 많은 양의 코드인 경우 유용
  - ex) 오류 처리 루틴
- 운영체제의 특별한 지원 없이 프로그램 자체에서 구현할 수 있음
  - OS 는 라이브러리를 통해 지원 가능
<br><br>

### 2) Dynamic Linking
- Linking 을 실행 시간 (execution) 까지 미루는 기법
  - `Linking` : 여러 군데 존재하던 컴파일된 파일을 묶어 하나의 실행 파일로 만드는 것
- Staic Linking
  - 라이브러리가 프로그램의 실행 파일 코드에 포함됨
  - 실행 파일의 크기가 커짐
  - 동일한 라이브러리를 각각의 프로세스가 메모리에 올리기 때문에 메모리 낭비
    - ex) printf 함수 라이브러리 코드
- Dynamic Linking
  - 라이브러리가 실행시 연결됨
  - 라이브러리 호출 부분에 라이브러리 루틴 위치를 찾기 위한 'stub' 이라는 작은 코드를 둠
  - 라이브러리가 이미 메모리에 있다면 해당 루틴의 주소로 가고, 없다면 디스크에서 읽어옴
  - OS 도움 필요
<br><br>

### 3) Overlays
- 메모리에 프로세스의 부분 중 실제 필요한 정보만을 올림 (Dynamic Loading 과 비슷함)
- 프로세스의 크기가 메모리보다 클 때 유용
- 운영체제의 지원 없이 사용자에 의해 구현
- 작은 공간의 메모리를 사용하던 초창기 시스템에서 수작업으로 프로그래머가 구현
  - `Manual Overlay`
  - 프로그래밍이 매우 복잡
<br><br>

### 4) Swapping 
- 프로세스를 일시적으로 메모리에서 backing store 로 쫓아내는 것을 말함
  - `Backing store` : 많은 사용자의 프로세스 이미지를 담을 만큼 충분히 빠르고 큰 저장 공간
- Swap in / Swap out
  - 일반적으로 중기 스케줄러 (swapper) 에 의해 swap out 시킬 프로세스 선정
  - priority - based CPU scheduling algorithm
    - 중요도가 낮은 프로세스를 swapped out 시킴
    - 중요도가 높은 프로세스를 메모리에 올려 놓음
  - Compile time 혹은 load time binding 에서는 원래 메모리 위치로 swap in 해야 함
  - Execution time binding 에서는 나중에 빈 메모리 영역 아무 곳에나 올릴 수 있음
  - swap time 은 대부분 transfer time
    - 이는 swap 되는 양에 비례하는 시간
<br><br>

## 3️⃣ Memory Management (메모리 관리)

### Allocation of Physical Memory
- 메모리는 일반적으로 두 영역으로 나뉘어 사용
  - OS 상주 영역 : interrupt vector 와 함께 낮은 주소 영역 사용
  - 사용자 프로세스 영역 : 높은 주소 영역 사용
<br><br>

- 사용자 프로세스 영역 할당 기법
    1. Contiguous allocation (연속 할당)
       - 프로그램이 한 군데 통쨰로 올라감
       - 각각의 프로세스가 메모리의 연속적인 공간에 적재되도록 하는 것
       - Fixed partition allocation
       - Variable partition allocation
    2. Noncontiguous (불연속 할당)
       - 하나의 프로세스가 메모리의 여러 영역에 분산되어 올라갈 수 있음
       - Paging
         - 같은 크기의 페이지 단위로 나눠 분산
       - Segmentation
       - Paged Segmentation
<br><br>
---

### Contiguous Allocation (연속 할당)
1. 고정 분할(Fixed Partition) 방식
   - 물리적 메모리를 몇 개의 영구적 분할(partition)로 나눔
   - 분할의 크기가 모두 동일한 방식 vs 서로 다른 방식
   - 분할당 하나의 프로그램을 적재
   - 융통성이 없음
     - 동시에 메모리에 load 되는 프로그램 수가 고정
     - 최대 수행 가능한 프로그램 크기 제한
   - Internal Fragmentation 발생 (External fragmentation도 발생)
2. 가변 분할(Variable Partition) 방식
   - 프로그램의 크기를 고려해서 할당
   - 분할의 크기, 개수가 동적으로 변환
   - 기술적 관리 기법 필요
   - Externer fragmentation 발생
<br><br>
---

## 4️⃣ Paging
<img width="600" alt="image" src="https://github.com/junseoparkk/algorithm-study/assets/98972385/27905c8d-b7f5-4fdc-b513-68933c532912">
<br><br>

### 1) Paging
   - 프로세스의 가상 메모리를 동일한 사이즈의 페이지 단위로 나눔
   - 가상 메모리의 내용이 페이지 단위로 noncontiguous(불연속)하게 저장됨
   - 일부는 backing storage에, 일부는 physical memory에 저장
<br>

1. Basic Method
   - Physical memory를 동일한 크기의 frame으로 나눔
   - Logical memory를 동일한 크기의 page로 나눔 (= frame 크기)
   - 모든 가용 frame들을 관리
   - `page table` 을 사용하여 논리 주소를 물리 주소로 변환

2. Page Table
   - 페이지의 개수 만큼 엔트리가 존재 (엔트리의 크기가 정해짐)
   - 인덱스를 통해 논리 주소의 페이지들을 물리 주소의 페이지를 찾음
   - 주소 변환이 이루어질 땐 상대적 위치가 변하지 않음
<br><br>
---

### 2) Implementation of Page Table
- 'Page Table'은 main memory에 상주
- `Page-table base register (PTBR)` 가 page table을 가리킴
- `Page-table length register (PTLR)` 가 테이블 크기를 보관
- 모든 메모리 접근 연산에는 2번의 memory access 필요
  - page table 접근 : 1회
  - 실제 data/instruction 접근 : 1회
- 속도 향상을 위해 `associative register` 혹은 `translation look-aside buffer (TLB)` 라고 불리는 고속의 하드웨어 캐시를 사용
<br><br>


### 🔎 `TLB`
<img width="500" alt="image" src="https://github.com/junseoparkk/algorithm-study/assets/98972385/29756d8b-bb17-4244-8399-fc9998251778">
<br>

- 메모리 주소 변환을 위한 별도의 캐시 메모리
- page table에서 빈번히 참조되는 페이지를 캐싱
- 특정 항목이 아니라 전체를 검색
  - 시간이 오래 걸리기 때문에 병렬 검색 가능
- CPU가 논리 주소를 주었을 때
  1. 메모리상의 page table을 검색하기 전에 TLB를 먼저 검색
  2. 주소 변환 정보 중 TLB에 저장된 정보를 통해 변환이 가능한지를 확인
     - 가능하다면 TLB를 통해 변환 -> 메모리를 1번만 사용
     - 불가능하다면 메모리에 접근하여 변환 -> 메모리를 2번 사용
<br><br>
---

### 3) Associatice Register
- Assosiatice registers (TLB) : parallel search 가능
  - TLB 에는 page table 중 일부만 존재
- Address translation
  - page table 중 일부가 associative register에 보관되어 있음
  - 만약 해당 page가 associativce register에 있을 경우 곧바로 frame을 얻음
  - 그렇지 않은 경우 main memory에 있는 page table로부터 frame을 얻음
  - TLB는 context switch 때 flush
    - 프로세스마다 주소 변환정보가 다르기 때문임
---

### 4) Effective Access Time
- Associativce register lookup time (TLB에 접근하는 시간) = 𝜖
- memory cycle time (메인 메모리 접근하는 시간) = 1
- Hit ratio (associative register에서 찾아지는 비율) = 𝛼 
- Effectivce Access Time (메모리에 접근하는 시간)
  - EAT = (1 + 𝜖)𝛼 + (2 + 𝜖)(1 - a) = 2 + 𝜖 - a
  - 결국 해당 값이 page table만 있을 때 걸리는 시간인 2보다는 작다 -> 빠름
---

### 5) Two-Level Page Table
<img width="500" alt="image" src="https://github.com/junseoparkk/algorithm-study/assets/98972385/fafa0ea1-4d8b-4e60-b769-68736c7565cb">
<br><br>

- 현대의 컴퓨터는 주소 공간이 매우 큰 프로그램을 지원
- 32 bit address 사용시 : 2^32B (4G) 의 주소 공간 (최근엔 64bit)
  - page size가 4K시 1M개의 page table entry 필요
  - 각 page entry가 4B 라면 프로세스당 4M의 page table 필요
  - 하지만 대부분의 프로그램은 4G의 주소 공간 중 아주 작은 일부분만 사용하므로 page table의 낭비가 심함

- B(바이트), K(킬로 바이트), M(메가 바이트), G(기가 바이트)

❗️해결
- page table 자체를 page로 구성
- 사용되지 않는 주소 공간에 대한 outer page table의 엔트리 값은 NULL (대응하는 inner page table 없음)
- 안쪽 page table과 바깥쪽 page table이 존재
  - CPU가 논리 주소를 전달하면 page table 두 단계를 거쳐 주소 변환 -> 메모리 접근
<br><br>

🔎 왜 사용하는가?
- 속도는 줄어들지 않지만 page table 공간이 줄어듦
---

### 6) Two-Level Paging Example
- logical address (32-bit, 4K page size)의 구성
  - 20 bit : page number
  - 12 bit : page offset (page와 얼마나 떨어져 있는가를 나타낸 것)
- page table 자체가 page로 구성되기 떄문에 page number가 다음과 같이 나뉨
  - 각 page table entry : 4B
  - 10-bit : page number
  - 10-bit : page offset
- logical address
<br>
<img width="380" alt="image" src="https://github.com/junseoparkk/algorithm-study/assets/98972385/2eaa28a2-938e-4fdd-8d2a-a2cefa94cd85">
<br>
- p1 : outer page table의 index
- p2 : outer page table의 page에서의 변위(displacement)
<br><br>
---

### Address-Translation Scheme
<img width="700" alt="image" src="https://github.com/junseoparkk/algorithm-study/assets/98972385/9866bb97-243f-441c-a9be-450d0d51f622">
<br><br>

- logical address 에서 outer-page table의 index를 찾을 page 번호를 통해 주소 변환 정보를 얻음
- page of page table(안쪽 테이블)의 페이지 번호를 통해 물리적인 page frame 번호를 얻음
- 해당 번호에서 d만큼 떨어진 위치에서 원하는 정보를 찾음

<br><br>
---
references<br>
KOCW 반효경-Introduction to Operating Systems