# Memory Management - 2

### Multilevel Paging and Performance
- Address space가 더 커지면 다단계 page table이 필요 (2단계로 부족)
- 각 단계의 page table이 메모리에 존재하므로 logical address의 physical address 변환에 더 많은 메모리 접근 필요
  - TLB를 통해 접근 시간을 줄일 수 있음
- 4단계 page table을 사용하는 경우
  - 메모리 접근 시간이 100ns, TLB 접근 시간이 20ns, TLB hit ratio가 98%인 경우 (98% 비율 만큼은 20ns 만에 주소 변환이 된다.)
    - effective momory access time = 0.96 x 120 x 0.02 x 520 = 128ns
    - 결과적으로 주소 변환을 위해 28ns만 소요
<br><br>
---

### Valid (v) / Invalid (i) Bit in Page Table
<img width="600" alt="image" src="https://github.com/junseoparkk/algorithm-study/assets/98972385/e49f33f0-ca42-4182-a180-eee6554243be">
<br><br>

- 6, 7번 page는 프로그램에서 사용하지 않지만, 엔트리에는 존재
  - 이유 : 프로그램의 주소 공간이 가질 수 있는 최대 크기 만큼 page table entry가 있어야 함
- valid : 실제로 물리적 메모리에 올라가 있음 (주소 변환 정보가 맞음)
- invalid : 물리적 메모리에 올라가있지 않음
<br><br>
---

### Memory Protection
- Page table의 각 entry 마다 추가로 bit를 둔다.
- `Protection bit`
  - page에 대한 접근 권한(연산에 대한 권한) : (read/write/read-only)
    - stack, data 영역은 read/write 권한 모두 
    - code 영역은 read-only만
- `Valid-invalid bit`
  - valid : 해당 주소의 frame에 프로세스를 구성하는 유효 내용이 있음을 뜻함 (접근 허용)
  - invalid : 해당 주소의 frame에 유효한 내용이 없음을 뜻함 (접근 불허)
    1. 프로세스가 해당 주소 부분을 사용하지 않는 경우
    2. 해당 페이지가 메모리에 올라와 있지 않고, swap area에 있는 경우
---

### Inverted Page Table
<img width="700" alt="image" src="https://github.com/junseoparkk/algorithm-study/assets/98972385/d06ca916-a37a-428e-9488-5590eb32dd91">
<br><br>
- page table이 매우 큰 이유
  - 모든 프로세스별로 해당 logical address에 대응하는 모든 page에 대해 page table entry가 존재
  - 대응하는 page가 메모리에 있는지 상관 없이 page table에는 entry로 존재함
- Inverted Page Table
  - Page frame 하나당 page table에 하나의 entry를 둔 것
  - 각 page table entry는 각각의 physical memory의 page frame이 담고 있는 내용 표시 (process id, process의 logical address)
  - 단점
    - 페이지 전체를 탐색해야 함
    - page 번호가 주어지면 entry를 전부 검색해야 주소 변환을 할 수 있음
  - 조치
    - associative register 사용 (비용이 높음)
---

### Shared Page
<img width="600" alt="image" src="https://github.com/junseoparkk/algorithm-study/assets/98972385/7138fd9e-438b-41bb-86b0-db246585ea67">
<br><br>

- Shared code
  - 공유할 수 코드는 별도로 올리지 않고 같은 frame으로 매핑하여 메모리에 한 칸만 올릴 수 있음
  - Re-entrant Code (= Pure code) : 재진입 가능한 코드
  - read-only로 반드시 세팅하여 프로세스 간 하나의 code만 메모리에 올림
    - ex) text editors, compilers, window systems
  - Shared code는 모든 프로세스의 logical address space 에서 동일한 위치에 있어야 함
- Private code
  - 프로세스마다 별도로 가져야 하는 코드는 독자적으로 메모리에 올림
  - private data는 logical address space의 아무 곳에 오도 무방
- 제약 조건
  - read-only 로 설정해야 함
  - 동일한 logical address에 위치해야 함 (physical address는 당연히 같음)
---

## 5️⃣ Segmentation

### Segmentation
- 프로그램은 의미 단위인 여러 개의 segment로 구성
  - 작게는 프로그램을 구성하는 함수 하난하나를 세그먼트로 정의
  - 크게는 프로그램 전체를 하나의 세그먼트로 정의
  - 일반적으로 code, data, stack 부분이 하나씩 세그먼트로 정의
- ex) main(), function, global variables, stack, arrays
<br><br>

### Segmentation Architecture
- Logical address 는 <segment-number, offset> 두 가지로 구성
- Segment table
  - base : 세그먼트의 물리 주소 시작점
  - limit : 세그먼트의 길이
- STBR (Segment-table base register)
  - 물리적 메모리에서의 segment table 위치
- STLR (Segment-table length register)
  - 프로그램이 사용하는 segmet 수
<br><br>

### Segmentation Hardware
<img width="700" alt="image" src="https://github.com/junseoparkk/algorithm-study/assets/98972385/8d866a7c-5df8-40a0-92ce-bb23d40110d3">
<br><br>

---

### Segmentation with Paging
- pure segmentation과의 차이점
    - segment-table entry가 segment의 base address를 가지고 있는 것이 아니라, segment를 구성하는 page table의 base address를 가지고 있음