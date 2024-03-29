# Process Management

## 1️⃣ 프로세스 생성 (Process Creation)
- 부모 프로세스 (Parent Process) 가 자식 프로세스 (Children Process) 를 생성
- 프로세스의 트리 (계층 구조) 형성
- 프로세스는 자원을 필요로 함
  - 운영체제로부터 받음
  - 부모 프로세스와 공유 (복제 생성)
    - 주소공간인 code, data, stack 복사
    - CPU 문맥 -> PC Register 복사
- 자원의 공유
  - 부모와 자식이 모든 자원을 공유하는 모델
  - 일부를 공유하는 모델
  - 전혀 공유하지 않는 모델 (일반적인 모델)
- 수행 (Execution)
  - 부모와 자식이 공존하며 수행되는 모델
  - 자식이 종료(terminate)될 때까지 부모가 기다리는(wait) 모델
- 주소 공간 (Address space)
  - 자식은 부모의 공간을 복사 (binary and OS data)
  - 자식은 복사한 공간에 새로운 프로그램을 올림
- ex) UNIX
  1. 'fork() 시스템 콜'이 새로운 프로세스를 생성
    - 부모를 그대로 복사 (OS data except PID + binary)
    - 주소 공간 할당
  2. fork 다음에 이어지는 'exec() 시스템 콜'을 통해 새로운 프로그램을 메모리에 올림
<br><br>

### ❗️ Copy-on-write (COW)
- 기존 내용이 바뀔 때 (write) 부모의 메모리를 복사 (copy)
- 가능하면 부모와 자식이 자원을 공유하면 효율적이지만, 별개의 프로세스가 원칙
<br><br>

## 2️⃣ 프로세스 종료 (Process Termination)
- 프로세스가 마지막 명령을 수행한 뒤 운영체제에게 이를 알려줌 (exit) -> 자발적 종료
  - 자식 프로세스가 종료된 뒤 부모 프로세스가 종료 
  - 자식이 부모에게 output data를 보냄 (via wait)
  - 프로세스의 각종 자원들이 운영체제에게 반납됨
- 부모 프로세스가 자식의 수행을 종료시킴 (abort) -> 강제 종료
  - 자식이 할당 자원의 한계치를 넘어선 경우
  - 자식에게 할당된 task가 더 이상 필요하지 않음
  - 부모가 종료 (exit) 하는 경우
    - 운영체제는 부모 프로세스가 종료하는 경우 자식이 더 이상 수행되지 않도록 함
    - 단계적인 종료
<br><br>

## 3️⃣ 시스템 콜

### 3-1) fork() 시스템 콜

- 프로세스는 'fork()' 시스템 콜에 의해 생성됨

```c
int main() {
    int pid;
    pid = fork();

    if (pid == 0) {
        printf("\n Hello, I am child \n");
    } else if (pid > 0) {
        printf("\n Hello, I am parent \n");
    }
}
```

1. 부모 프로세스가 순차적으로 코드를 실행
2. 'fork()' 메서드가 실행되면 새로운 프로세스를 생성
3. 부모 프로세스는 fork() 메서드 실행 이후 다음 코드를 실행
4. 자식 프로세스는 main() 메서드 처음부터가 아닌 'fork()' 메서드 이후를 실행
    - 복제한 program counter 가 해당 메서드 이후를 가리킴
<br><br>

### 🔎 fork() 를 통해 복제 생성했을 때
### 문제점
1. 자식이 부모를 복제본으로 취급할 수 있음
2. 프로그램들이 똑같은 제어 흐름을 따라갈 수 있음
<br>

### 해결 방법
- 복제를 할 때 운영체제가 부모와 자식을 구분함
- fork() 실행 이후 리턴값이 다름
  - 부모 프로세스 > 0
  - 자식 프로세스 ≒ 0
<br><br>
---

### 3-2) exec() 시스템 콜
- 프로세스는 'exec()' 시스템 콜을 통해 서로 다른 프로그램을 실행할 수 있음
- 'execlp()' 이후의 코드는 실행되지 않음

```c
int main() {
    int pid;
    pid = fork();

    if (pid == 0) {
        printf("\n Hello, I am child! Now I'll run date \n");
        execlp("/bin/date", "/bin/date", (char *) 0);   // 완전히 새로운 프로그램으로 덮음
    } else if (pid > 0) {
        printf("\n Hello, I am parent \n");
    }
}
```
<br>

---

### 3-3) wait() 시스템 콜
만약 프로세스 A가 'wait()' 시스템 콜을 호출하면
    - 커널은 child process 가 종료될 때까지 프로세스 A를 sleep 시킴 (block 상태)
    - child process 가 종료되면 커널은 프로세스 A를 깨움 (ready 상태)
    - 즉, 자식이 종료될 때까지 부모가 기다리는 시스템 콜
<br><br>

### ❗️예시
### 1. 컨테이너를 포그라운드에서 실행
```bash
docker run --name process1 -e MYSQL_ROOT_PASSWORD=1234 mysql
```
- Docker 는 컨테이너 프로세스를 생성하는 부모 프로세스 역할을 수행
- Docker 가 해당 컨테이너 프로세스의 종료를 기다리기 위해 'wait()' 시스템 콜 호출 
<br><br>

### 2. 컨테이너를 백그라운드에서 실행
```bash
docker run -d --name process2 -e MYSQL_ROOT_PASSWORD=1234 mysql
```
- Docker 는 컨테이너 생성 후 해당 프로세스를 독립적으로 실행
- Docker 는 부모 프로세스 역할을 하지 않으므로, 'wait()' 시스템 콜 호출하지 않음
<br><br>
---
### 3-4) exit() 시스템 콜

#### 1. 자발적 종료
- 마지막 statement 수행 후 exit() 시스템 콜을 통해 종료
- 프로그램에 명시적으로 넣어주지 않아도 main 함수가 return 되는 위치에 컴파일러가 넣어줌
#### 2. 비자발적 종료
- 부모 프로세스가 자식 프로세스를 강제 종료시킴
  - 자식 프로세스가 한계치를 넘어서는 자원 요청
  - 자식에게 할당된 task가 더 이상 필요하지 않음
- 키보드로 kill, break, ^C 등을 입력한 경우 
- 부모가 종료하는 경우
  - 부모 프로세스를 종료하기 전에 자식 프로세스가 먼저 종료됨
<br><br>
---

### 🔎 프로세스 관련 시스템 콜 정리

|System call|task|
|:---:|:---:|
|fork()|create a child()|
|exec()|overlay new image|
|wait()|sleep until child is done|
|exit()|frees all the resources, notify parent|
|||

## 4️⃣ 프로세스간 협력

### 1) 독립적 프로세스 vs 협력 프로세스
1. 독립적 프로세스 (Independent process)
    - 프로세스는 각자의 주소 공간을 가지고 수행됨
    - 원칙적으로 하나의 프로세스는 다른 프로세스 수행에 영향을 미치지 못함
2. 협력 프로세스 (Cooperating process)
    - 프로세스 협력 메커니즘을 통해 하나의 프로세스가 다른 프로세스 수행에 영향을 미칠 수 있음
<br><bR>

### 2) 프로세스 협력 메커니즘 (IPC, Interprocess Communication)
- 메시지 전달 방법
  - message passing : 커널을 통해 메시지 전달
- 주소 공간 공유 방법
  - shared memory : 서로 다른 프로세스 간에도 일부 주소 공간을 공유하게 하는 'shared memory' 메커니즘
  - 커널의 도움을 받아 공유할 물리적 메모리 영역을 매핑
  - thread : thread 는 사실상 하나의 프로세스이므로 프로세스 간 협력으로 보기는 어렵지만, 동일한 프로세스를 구성하는 thread 간에는 주소 공간을 공유하므로 협력 가능
<br><br>

![image](https://github.com/junseoparkk/til/assets/98972385/14dcee0c-d939-4f3b-bb58-9c5c7a54c608)
<br><br>

### 3) Message Passing
- Message System
  - 프로세스 사이에 공유 변수 (shared variable) 를 일체 사용하지 않고 통신하는 시스템
  - 운영체제 커널을 통해 메시지 전달 (사용자 프로세스끼리 메시지 전달은 불가능)
<br>

#### 1. Direct Communication
- 통신하려는 프로세스의 이름을 명시적으로 표시

![image](https://github.com/junseoparkk/til/assets/98972385/7d986bf5-822d-46c6-864f-883a72dd7b51)
<br>

#### 2. Indirect Communication
- mailbox 또는 port 를 통해 메시지를 간접 전달
  
![image](https://github.com/junseoparkk/til/assets/98972385/12bbc9f7-38c3-4501-84b8-cab8b1481e5b)
<br><br>
---
#### references
- ABRAHAM SILBERSCHATZ ET AL., OPERATING SYSTEM CONCEPTS, NINTH EDITION, WILEY, 2013
- 반효경, 운영체제와 정보기술의 원리, 이화여자대학교 출판부, 2008