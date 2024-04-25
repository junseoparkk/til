# File System Implementation

## 1️⃣ Allocation of File Data in Disk

### 1) Contiquous Allocation (연속 할당)
<img width="400" alt="image" src="https://github.com/junseoparkk/kakao-cloud-school/assets/98972385/4b0aeaf7-df2f-44ff-bba4-b6fd37989685">
<br><br>

- 하나의 파일이 디스크에 연속적으로 할당되는 방법
- 블럭의 크기만큼 연속적으로 할당됨
<br><br>

### 🔎 장점 및 단점
#### 단점
1. external fragmentation (외부 조각)
   - 각 파일들의 길이가 일정하지 않기 때문에 외부 조각이 발생할 수 있음
   - 중간중간 비어있는 블럭이 발생할 수 있어도 활용할 수 없음
2. File grow가 어려움
   - 파일 생성시 얼마나 큰 크기만큼 배당할 것인가? (미리 할당)
   - grow 가능 vs 낭비 (internal fragmentation)

#### 장점
1. Fast I/O
    - 한 번의 seek/rotation으로 많은 바이트 전송
    - Realtime file 용으로, 또는 이미 실행 중이던 프로세스의 swapping 용으로 적합
      - 공간 효율성보단 시간 효율성이 중요하기 때문
    - Direct acess (= random access) 가능
---

### 2) Linked Allocation
<img width="400" alt="image" src="https://github.com/junseoparkk/kakao-cloud-school/assets/98972385/d45412de-c3bc-46c2-95a6-87ad46e69e13">
<br><br>

- 디렉토리가 파일의 시작 위치만 가지고 있고, 다음 위치는 실제 파일의 위치에 기록해놓는 방법
<br><br>

### 🔎 장점 및 단점
#### 장점
1. External fragmentation 발생 안함
    - 비어있는 공간을 찾아 연결하기 때문

#### 단점
1. No random access
    - 순차 접근에 의한 시간이 오래 걸림
2. Reliability
    - 파일을 구성하는 섹터 중 하나가 고장나면 포인터가 유실되면서 많은 부분을 잃음 (다음 목적지를 찾이 못함)
3. 포인터를 위한 공간이 block의 일부가 되어 공간 효율성을 떨어뜨림

#### 변형
- File-allocation table (FAT) 파일 시스템
  - 포인터를 별도의 위치에 보관하여 reliabilty와 공간 효율성 문제를 해결
---

### 3) Indexed Allocation
<img width="400" alt="image" src="https://github.com/junseoparkk/kakao-cloud-school/assets/98972385/c0b3da99-4bb2-4a10-83b5-7eed321bdae6">
<br><br>

- Index를 통해 직접 접근이 가능하며, 순차 접근의 문제를 보완한 방법
- Index를 저장하는 블럭 + 실제 데이터를 저장하는 블럭

### 🔎 장점 및 단점
#### 장점
1. External fragmentation 발생 안함
2. Direct access 가능

#### 단점
1. 크기가 작은 파일의 경우 공간 낭비 (실제로 많은 파일들의 크기가 작음)
2. 크기가 너무 큰 파일의 경우 하나의 block으로 인덱스를 저장하기엔 부족
    - 해결 방안
        1. Linkced scheme
        2. Multi-level index
---

## 2️⃣ 파일 시스템의 구조

### 1) UNIX 파일 시스템
<img width="600" alt="image" src="https://github.com/junseoparkk/kakao-cloud-school/assets/98972385/33cdb9cd-7ed7-4c70-b792-f4c9a08d6a51">

1. Boot block
    - 부팅에 필요한 정보들을 보관
2. Superblock
    - 파일 시스템에 관한 총체적인 정보
    - 어디가 빈 블럭이고, 어디가 사용 중인 블럭인지?
    - 어디까지 Inode인지?
3. Inode list
    - 파일의 디렉토리에 메타데이터가 있지만, 그 외 메타 데이터는 Inode list에 존재
    - 파일 이름은 디렉토리가 가짐, 나머진 특정 번호의 inode에서 가짐
    - 파일 하나당 Inode 할당

- 기본적으로 index allocation을 변형해서 사용
---

### 2) FAT 파일 시스템
<img width="600" alt="image" src="https://github.com/junseoparkk/kakao-cloud-school/assets/98972385/097677ac-3552-45dc-abf8-2f7b60377cd3">
<br><br>

- 파일의 이름, 접근 권한 등 모든 메타데이터를 디렉토리가 가짐
- FAT 이라는 별도의 테이블에 다음 블럭에 대한 정보를 가지고 있음
  - n개의 블럭이 있다면 FAT 배열의 크기가 n이 됨