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

### 1) Allocation of Physical Memory
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


---
references<br>
KOCW 반효경-Introduction to Operating Systems