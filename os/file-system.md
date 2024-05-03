# File System

## 1️⃣ File System

### 1) File & File System
#### `File`
- 관련 있는 정보들의 모음에 이름을 붙인 것
- 일반적으로 비휘발성의 보조기억장치에 저장됨
- 운영체제는 다양한 저장 장치를 `file` 이라는 동일한 논리적 단위로 볼 수 있게 해줌
- Operation
  - create, read, write, reposition(lseek), delete, open, close 등
  - 위 연산들은 I/O 작업이므로 system call 발생
#### `File attribute (metadata)`
- 파일 자체의 내용이 아닌, 파일 관리를 위한 정보들
  - 파일 이름, 유형, 저장된 위치, 크기
  - 접근 권한 (읽기/쓰기/실행), 시간 (생성/변경/사용), 소유자 등
#### `File System`
- 운영체제에서 파일을 관리하는 부분
- 파일 및 파일의 메타데이터, 디렉토리 정보 등 관리
- 파일 저장 방법 결정
- 파일 보호 등
---

### 2) Directory & Logical Disk
#### `Directory`
- 파일의 메타데이터 중 일부를 보관하고 있는 일종의 특별한 파일
- 해당 디렉토리에 속한 파일 이름 및 attribute 등
- Opration
  - search for a file, create a file, delete a file
  - list a directory, rename a file, traverse the fily system

#### `Partition (= Logical Disk)`
- 일반적으로 하나의 물리적 디스크 안에 여러 파티션을 둠
  - C드라이브, D드라이브 등
- 여러 개의 물리적 디스크를 하나의 파티션으로 구성하기도 함
- 물리적 디스크를 파티션으로 구성한 뒤 각각의 파티션에 file system을 깔거나 swapping 등 다른 용도로 사용할 수 있음
---

### 3) Open()

- 파일의 메타데이터를 메모리에 올려놓는 것
<img width="600" alt="image" src="https://github.com/junseoparkk/kakao-cloud-school/assets/98972385/fcb584c3-d58b-4ec1-930b-e73bde98088b">
<br><br>

- ex) open("/a/b/c")
  - 디스크로부터 파일 c의 메타데이터를 메모리로 가지고 옴
  - 이를 위해 디렉토리 경로를 검색
    - 루트 디렉토리인 "/"를 open => 파일 "a"의 위치 획득
    - 파일 "a"를 open한 뒤 read => 파일 "b"의 위치 획득
    - 파일 "b"를 open한 뒤 read => 파일 "c"의 위치 획득
    - 파일 "c"를 open
  - 디렉토리 경로 검색에 너무 많은 시간이 소요됨
    - Open을 read/write와 별도로 두는 이유
    - 한번 Open한 파일은 read/write시 디렉토리 검색 불필요
  - `Open file table`
    - 현재 open된 파일들의 메타데이터 보관소 (in memory)
    - 디스크의 메타데이터보다 몇몇 정보를 더 추가
      - Open한 프로세스 수
      - File offset : 파일 어느 위치에 접근 중인지 표시 (별도 테이블 필요)
  - `File descriptor` (file handle, file control block)
    - Open file table에 대한 위치 정보 (프로세스 별로)
---

### 4) File Protection
- 각 파일에 대해 누구에게 어떤 유형의 접근 (read/write/execuriont) 을 허락할 것인가?
- Access Control 방법
#### `Access control Matrix`
- Access control list : 파일별로 누구에게 어떤 접근 권한이 있는지를 표시
- Capability : 사용자별로 자신이 접근 권한을 가진 파일 및 해당 권한을 표시

#### `Grouping`
- 전체 유저를 'owner', 'group', 'public' 세 그룹으로 구분
- 각 파일에 대해 세 그룹의 접근 권한(rwx)를 3비트씩 표시

#### `Password`
- 파일마다 password를 두는 방법 (디렉토리 파일에 두는 방법 또한 가능)
- 모든 접근 권한에 대해 하나의 password : all-of-noting
- 접근 권한별 password : 암기 문제, 관리 문제
---

### 5) Mounting
<img width="600" alt="image" src="https://github.com/junseoparkk/kakao-cloud-school/assets/98972385/5a56f969-e0b5-4668-a66e-2d6a561d5a14">
<br><br>

- 각 논리적인 시스템엔 파일 시스템이 존재
- 다른 파일 시스템의 파티션에 접근하려면 마운팅이 필요
---

### 6) Access Methods
 - 시스템이 제공하는 파일 정보의 접근 방식
   - 순차 접근 (sequential access)
     - 카세트 테이프를 사용하는 방식처럼
     - 읽거나 쓰면 offset은 자동적으로 증가
   - 직접 접근 (direct, random access)
     - LP 레코드 판처럼
     - 파일을 구성하는 레코드를 임의의 순서로 접근할 수 있음
