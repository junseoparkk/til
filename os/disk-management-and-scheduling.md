# Disk Management & Scheduling

## 1️⃣ Disk Scheduling

### 1) Disk scheduling
<img width="400" alt="image" src="https://github.com/junseoparkk/today-my-school-front/assets/98972385/7067f3a1-4bf8-4f43-84e9-43197ef66c82">
<br><br>

- Access time 구성
  - `Seek time` : 헤드를 해당 실린더로 움직이는데 걸리는 시간
  - `Rotational latency` : 헤드가 원하는 섹터에 도달하기까지 걸리는 시간
  - `Transfer time` : 실제 데이터 전송 시간
- Disk bandwidth
  - 단위 시간 당 전송된 바이트 수
  - 디스크의 성능을 나타내는 지표 중 하나
- Disk scheduling
  - Seek time을 최소화하는 것이 목표
  - Seek time = Seek distance
  - 요청이 들어온 순서대로 처리하면 비효율적이므로, 안쪽의 요청이 큐에 쌓여있다면 먼저 처리함
---

### 2) Disk Structure
- Logical Block
  - 디스크의 외부에서 보는 디스크의 단위 정보 저장 공간들
  - 주소를 가진 1차원 배열처럼 취급
  - 정보 전송의 최소 단위
- Sector
  - Logical block이 디스크에 매핑된 위치
  - `Sector 0`은 최외곽 실린더의 첫 트랙에 있는 첫 번째 섹터 -> 부팅과 관련된 정보 저장
---

### 3) Disk Management
- Physical Formatting (Low-level formatting)
  - 디스크를 컨트롤러가 읽고 쓸 수 있도록 섹터들로 나누는 과정
  - 각 섹터는 `header`+ `실제 data`(보통 512bytes) + `trailer`로 구성
  - header와 trailer는 sector number, ECC(Error-Correcting Code) 등의 정보가 저장되며 controller가 직접 접근 및 운영
- Partitioning
  - 디스크를 하나 이상의 실린더 그룹으로 나누는 과정
  - OS는 이것을 독립적인 디스크로 취급 (Logical Disk)
- Logical Formatting
  - 파일시스템을 만드는 것
  - FAT, inode, free space 등의 구조를 포함
- Booting
  - ROM에 있는 `small bootstrap loader`의 실행
  - sector 0 (boot block)을 load하여 실행
  - sector 0은 `full bootstrap program`
  - OS를 디스크에서 load하여 실행
<br><br>

## 2️⃣ Disk Scheduling Algorithm

### 🔎 예시
- 큐에 다음과 같은 실린더 위치의 요청이 존재하는 경우 디스크 헤드 53번에서 시작한 각 알고리즘의 수행 결과는? (실린더 위치는 0-199)
- `98, 183, 37, 122, 14, 124, 65, 67`
---
<br><br>

### 1) FCFS (First Come First Service)
<img width="500" alt="image" src="https://github.com/junseoparkk/mdn-web-docs/assets/98972385/5b2cfbd8-4c85-495c-84de-9614b838ed6e">
<br>

- 들어온 순서대로 처리하는 방법
- 총 head의 이동 : 640 cylinders
- 비효율적인 경우가 발생
---

### 2) SSTF (Shortest Seek Time First)
<img width="500" alt="image" src="https://github.com/junseoparkk/mdn-web-docs/assets/98972385/7d868e6d-b3bd-49ed-97ce-373fd104b7b1">
<br>

- 현재 헤드 위치에서 제일 가까운 요청을 먼저 처리하는 방법
- 총 head의 이동 : 236 cylinders
- starvation 문제
---

### 3) SCAN
<img width="300" alt="image" src="https://github.com/junseoparkk/mdn-web-docs/assets/98972385/2ab30186-85cf-4435-b973-7f3c9db06656">
<br>

<img width="500" alt="image" src="https://github.com/junseoparkk/mdn-web-docs/assets/98972385/6e9b46fd-6a25-4b4a-b722-80ce0e683284">
<br>

- '엘리베이터 스케줄링'이라고도 불림
- disk arm이 디스크의 한쪽 긑에서 다른쪽 끝으로 이동하며 길목에 있는 모든 요청을 처리
- 다른 한쪽 끝에 도달하면 역방향으로 이동하며 오는 길목에 있는 모든 요청을 처리하며 반대쪽 끝으로 이동
- 문제점 : 실린더 위치에 따라 대기 시간이 달라짐
---

### 4) C-SCAN
<img width="300" alt="image" src="https://github.com/junseoparkk/mdn-web-docs/assets/98972385/a84b79f0-b360-46fa-952d-174c177444bf">
<br>
<img width="500" alt="image" src="https://github.com/junseoparkk/mdn-web-docs/assets/98972385/578e5f42-ba9a-4f0f-84b5-97c1335ae127">
<br>

- 헤드가 한쪽 끝에서 다른쪽 끝으로 이동하며 가는 길목에 있는 모든 요청을 처리
- 다른쪽 끝에 도달했으면 요청을 처리하지 않고 곧바로 출발점으로 다시 이동
- SCAN보다 균일한 대기 시간을 제공
---

### 5) Other Algorithms
- `N-SCAN`
  - SCAN의 변형 알고리즘
  - arm이 한 방향으로 움직이기 시작하면 그 시점 이후에 도착한 job은 되돌아올 때 service
- `LOOK` & `C-LOOK`
  - SCAN이나 C-SCAN은 요청이 있으나 없으나 헤드가 디스크 끝에서 끝으로 이동 (비효율적)
  - LOOK과 C-LOOK은 헤드가 진행하다가 그 방향에 더이상 기다리는 요청이 없다면 헤드 이동 방향을 즉시 반대로 이동
<br><br>

### 🔎 C-LOOK
<img width="500" alt="image" src="https://github.com/junseoparkk/mdn-web-docs/assets/98972385/bae166d2-0ec5-4f2f-96ae-3a911185bc50">
<br>

---

### 6) Disk-Scheduling Algorithm 결정
- SCAN, C-SCAN 및 응용 알고리즘은 LOOK. C-LOOK 등이 일반적으로 디스크 입출력이 많은 시스템에서 효율적인 것으로 알려짐
- File의 할당 방법에 따라 디스크 요청이 영향을 받음
- 디스크 스케줄링 알고리즘은 필요한 경우 다른 알고리즘으로 쉽게 교체할 수 있도록 OS와는 별도의 모듈로 작성되는 것이 바람직함
<br><br>

## 3️⃣ Swap-Space Management

### 1) Swap-Space 관리
- Disk를 사용하는 두 가지 이유
  - 메모리의 비휘발성 특성 (volatile) -> File System
  - 프로그램 실행을 위한 메모리 공간 부족 -> Swap Space (Swap Area) 사용
- Swap-Space
  - 가상 메모리 시스템에서는 디스크를 메모리의 연장 공간으로 사용
  - 파일 시스템 내부에 둘 수도 있지만, 별도의 partition 사용이 일반적임
    - `공간 효율성`보다는 `속도 효율성`이 우선 (휘발성이기 때문에 어차피 사라짐)
    - 일반 파일보다 훨씬 짧은 시간만 존재하고 자주 참조됨
    - block의 크기 및 저장 방식이 일반 파일 시스템과 다름
---

### 2) RAID (Redundant Array of Independent Disks)
- RAID
  - 여러 개의 디스크를 묶어서 사용
- RAID 사용 목적
    1. 디스크 처리 속도 향상
        - 여러 디스크에 block의 내용을 분산 저장
        - 병렬적으로 읽어 오는 기법 -> (interleaving, striping)
    2. 신뢰성 (reliability) 향상
        - 동일 정보를 여러 디스크에 중복 저장
        - 하나의 디스크가 고장난다면 다른 디스크에서 읽어옴 -> (mirroring, shadowing)
        - 단순 중복 저장이 아니라 일부 디스크에 parity를 저장하여 공간 효율성을 높일 수 있음