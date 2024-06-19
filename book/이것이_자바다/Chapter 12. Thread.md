# 1️⃣ Multi Thread 개념

## <font color="#d99694">1.1 Process & Thread</font>

![image](https://github.com/user-attachments/assets/9b67fbd4-faed-47c8-a0de-e27b0b56798f)

### Process (프로세스)

- 운영체제에서 실행 중인 하나의 애플리케이션
- 애플리케이션 실행시 운영체제로부터 실행에 필요한 메모리를 할당, 애플리케이션 코드를 실행
- 하나의 애플리케이션은 다중 프로세스를 만들기도 함
  - ex) Chrome 브라우저 두 개 실행시 두 개의 Chrome 프로세스가 생성된 것

### Thread (스레드)

- 한 가지 작업을 실행하기 위해 순차적으로 실행할 코드를 실처럼 이어 놓은 것
- 하나의 스레드는 하나의 코드 실행 흐름을 의미
- 만약 한 프로세스 내에 스레드가 두 개라면, 두 개의 코드 실행 흐름이 생긴다는 의미

##### 스레드를 언제 사용하면 좋은가?

1. I/O 작업이 많은 경우 : 파일 읽기/쓰기, 네트워크 통신 등 대기 시간이 길어질 수 있는 작업 비동기 처리할 때
2. UI 응답성 향상 : GUI 애플리케이션에서 긴 작업을 백그라운드에서 처리하여 UI가 멈추지 않도록 할 때
3. 병렬 처리 : 여러 작업을 동시에 수행하여 성능을 향상시킬 때

### 용어 정리

**1. 멀티 태스킹 (Multi Tasking)** : 두 가지 이상의 작업을 동시에 처리하는 것

- OS는 멀티 태스킹을 할 수 있도록 CPU 및 메모리 자원을 프로세스마다 적절히 할당하여 병렬로 실행
- 멀티 태스킹은 꼭 멀티 프로세스를 뜻하지는 않음 (한 프로세스 내에서 멀티 태스킹 가능한 경우)
- 멀티 태스킹이 가능한 이유는 멀티 스레드에 있음
- 멀티 프로세스들은 운영체제로부터 할당받은 자신의 메모리를 갖고 독립적으로 실행함. 따라서 한 프로세스에 오류가 발생해도 다른 프로세스에는 영향을 미치지 않음
  <br><br>

**2. 멀티 스레드 (Multi Thread)** : 애플리케이션 내부에서의 멀티 태스킹

- 하나의 프로세스 내부에 생성. 따라서 한 스레드가 예외를 발생시키면 프로세스 자체가 종료될 수 있어 다른 스레드에게 영향을 미침
- 예외 처리에 많은 신경을 써야 함
  <br>

## <font color="#d99694">1.2 Main Thread</font>

- 모든 자바 애플리케이션은 `메인 스레드(main thread)` 가 `main()` 메서드를 실행하면서 시작 됨
- 메인 스레드는 main() 메서드의 첫 코드부터 아래로 순차적으로 실행
- main() 메서드의 마지막 코드를 실행하거나, return문을 만나면 실행이 종료됨
- 메인 스레드는 필요에 따라 작업 스레드를 만들어 병렬로 코드 실행 가능
  - 멀티 스레드 생성 -> 멀티 태스킹 수행

### 용어 정리

**1. Single Thread (싱글 스레드)**

- 싱글 스레드 애플리케이션에서는 메인 스레드가 종료하면 프로세스도 종료
  **2. Multi Thread (멀티 스레드)**
- 멀티 스레드 애플리케이션에서는 실행 중인 스레드가 하나라도 있다면, 프로세스는 종료되지 않음
- 메인 스레드가 작업 스레드보다 먼저 종료되더라도 작업 스레드가 실행 중이라면, 프로세스는 종료되지 않음
  <br><br><br>

# 2️⃣ 작업 스레드 생성과 실행

1. `java.lang.Thread` 클래스를 직접 객체화해서 생성
2. `Thread` 를 직접 상속해서 하위 클래스를 만들어 생성

## <font color="#d99694">2.1 Thread 클래스로부터 직접 생성</font>

```java
Thread thread = new Thread(Runnable target);
```

### Runnable (interface)

- `java.lang.Thread` 클래스로부터 작업 스레드 객체를 직접 생성하려면 **Runnable** 을 매개값으로 갖는 생성자 호출
- **Runnable** 은 작업 스레드가 실행할 수 있는 코드를 가지는 객체로, 인터페이스 타입 -> 구현 객체 생성 필요
- **Runnable** 은 `run()` 메서드 하나가 정의되어 있어, 구현 클래스는 이를 재정의해야 함
- 실제 스레드는 아니며, 작업 내용을 가지는 객체
- Thread 객체는 Runnable 의 구현체

### 스레드를 구현하는 여러가지 방법

Runnable 구현체

```java
class Task implements Runnable {
	@Override
	public void run() {
		// 스레드 실행 코드
	}
}
```

익명 객체

```java
Thread thread = new Thread(new Runnable() {
	@Override
	public void run() {
		// 스레드 실행 코드
	}
})
```

람다 함수

```java
Thread thread = new Thread(() -> {
	// 스레드 실행 코드
})
```

결국 위처럼 여러가지 방법으로 스레드를 구현해도 실행 방법은 같다.

```java
thread.start();
```

- start() 메서드가 호출되면, 작업 스레드는 매개값으로 받은 Runnable 의 run() 메서드를 실행하면서 작업을 처리

## <font color="#d99694">2.2 Thread 하위 클래스로부터 생성</font>

- 작업 스레드가 실행 작업을 **Runnable**로 만들지 않고, **Thread** 하위 클래스로 작업 스레드를 정의하는 방법
- Thread 클래스 상속 후 `run()` 메서드를 재정의하여 스레드가 실행할 코드를 작성

Thread 상속

```java
public class WorkerThread extends Thread {
	@Override
	public void run() {
		// 스레드 실행 코드
	}
}

Thread thread = new WorkerThread();
```

익명 객체

```java
Thread thread = new Thread() {
	@Override
	public void run() {
		// 스레드 실행 코드
	}
}
```

## <font color="#d99694">2.3 스레드 이름</font>

- 스레드는 이름을 가지며, 디버깅할 때 어떤 스레드가 어떤 작업을 하는지 나타낼 수 있다.
- 메인 스레드는 "main" 이라는 이름을 가진다.
- 직접 생성한 스레드는 자동적으로 "Thread-n" 이라는 이름을 가진다. (n은 스레드 번호)

### 이름 관련 메서드

스레드 이름 설정

```java
thread.setName("스레드 이름");
```

스레드 이름 조회

```java
thread.getName();
```

- 위 두 메서드는 Thread의 인스턴스 메서드이므로 스레드 객체의 참조 필요
- 만약 스레드 객체의 참조를 가지고 있지 않다면, 정적 메서드인 `currentThread()` 를 통해 얻을 수 있음

참조를 갖지 않을 때 이름 조회

```java
Thread thread = Thread.currentThread();
thread.getName();
```

---

# 3️⃣ 스레드 우선순위 (Priority)

![image](https://github.com/user-attachments/assets/c1d9af8f-e3e2-4957-add2-2bbda9133b8e)

### 용어 정리

**1. 동시성 (Concurrency)**

- 멀티 작업을 위해 하나의 코어에서 멀티 스레드가 번갈아가며 실행하는 성질

**2. 병렬성 (Parallelism)**

- 멀티 작업을 위해 멀티 코어에서 개별 스레드를 동시에 실행하는 성질

**3. 스레드 스케줄링 (Thread Scheduling)**

- 스레드 개수가 코어 수보다 많을 경우, 스레드를 어떤 순서에 의해 동시성으로 실행할 것인가를 결정해야 함
- 싱글 코어 CPU를 이용한 멀티 스레드 작업은 동시성 작업이다. (번갈아 실행하는 것이 워낙 빨라 병렬성처럼 보이는 것)
- 스레드 스케줄링에 의해 스레드들은 아주 짧은 시간에 번갈아가며 run() 메서드를 조금씩 실행
- 자바의 스레드 스케줄링은 <font color="#95b3d7">우선순위(Priority) 방식</font>과 <font color="#95b3d7">순환 할당(Round-Robin) 방식</font>을 사용

### 우선순위 방식

```java
thread.setPriority(priority);    // 직접 값을 넣어 설정

thread.setPriority(Thread.MAX_PRIORITY);    // 10
thread.setPriority(Thread.NORM_PRIORITY);    // 5
thread.setPriority(Thread.MIN_PRIORITY);    // 1
```

- 우선순위가 높은 스레드가 실행 상태를 더 많이 가지도록 스케줄링하는 방식
- 스레드 객체에 우선순위 번호를 부여할 수 있기 때문에 개발자가 코드로 제어 가능
- 우선순위는 1 ~ 10까지 부여되며, 1이 가장 낮고, 10이 가장 높음. 우선순위를 부여하지 않으면 기본적으로 5의 우선순위를 할당 받음
- 만약 우선순위를 변경하고 싶다면 `setPriority()` 메서드 사용

### 순환 할당 방식

- 시간 할당량(Time Slice)를 정하여 하나의 스레드를 정해진 시간만큼 실행하고 다시 다른 스레드를 실행하는 방식
- JVM에 의해 정해지기 때문에 개발자가 코드로 제어 불가능

#### 어떤 방법을 선택해야 하는가?

- 싱글 스레드인 경우 우선순위 방식을 사용해 스레드에 우선순위를 부여하여 높은 우선순위를 가지는 스레드가 실행 기회를 더 가져감
- 만약 쿼드 코어일 경우 4개의 스레드가 병렬성으로 실행 가능하기에 우선순위 방식은 의미가 없음. - 만약 5개 이상의 스레드가 존재하면 우선순위의 영향을 받음
  <br><br><br>

# 4️⃣ 동기화 메서드와 동기화 블록

## 4.1 공유 객체 사용할 때 주의할 점

![image](https://github.com/user-attachments/assets/4defe9b4-b0ce-4d6f-b5bc-2cec376f2341)

- 싱글 스레드 프로그램에서는 <font color="#00b050">한 개 스레드가 객체를 독차지</font>하여 사용
- 멀티 스레드 프로그램에서는 <font color="#00b050">여러 개 스레드가 객체를 공유</font>하여 사용

#### MainThread

```java
public class MainThread {
	public static void main(String[] args) {
		Calculater calculator = new Calculator();

		User1 user1 = new User1();
		user1.setCalculator(calculator);
		user1.start();

		User2 user2 = new User2();
		user2.setCalculator(calculator);
		user2.start();
	}
}
```

#### Calculator

```java
public class Calculator {
	private int memory;

	public int getMemory() {
		return memory;
	}

	public void setMemory(int memory) {
		this.memory = memory;

		try {
			Thread.sleep(2000);
		} catch(InterruptedException e) {
			System.out.println(Thread.currentThread().getName + " exception");
		}
	}
}
```

#### User1

```java
public class User1 extends Thread {
	private Calculator calculator;

	public void setCalculator(Calculator calculator) {
		this.setName("User1");
		this.calculator = calculator;
	}

	public void run() {
		calculator.setMemory(100);
	}
}
```

#### User2

```java
public class User2 extends Thread {
	private Calculator calculator;

	public void setCalculator(Calculator calculator) {
		this.setName("User2");
		this.calculator = calculator;
	}

	public void run() {
		calculator.setMemory(500);
	}
}

```

결국 위 `MainThread` 를 실행하고 최종 결과를 확인하면 memory엔 `500`이 저장된다.

## 4.2 동기화 메서드 및 동기화 블록

### 용어 정리

**1. 임계 영역 (Critical Section)** : 멀티 스레드 프로그램에서 단 하나의 스레드만 접근하여 실행할 수 있는 코드 영역
**2. 동기화 (Synchronization)** : 두 가지 이상의 작업을 일치시키는 것
**3. 락 (Lock)** : 한 번에 하나의 스레드만 특정 자원에 접근하도록 제한하는 도구

#### 자바 동기화 구현

- 자바는 임계 영역을 지정하기 위해 동기화 메서드와 블록을 제공
- 스레드가 객체 내부의 동기화 메서드 또는 블록에 들어가면 즉시 객체에 잠금(Lock)을 걸어 다른 스레드가 임계 영역 코드를 실행하지 못하게 함
- 메서드 선언에 `synchronized` 키워드를 붙여 구현, 인스턴스와 정적 메서드 모두 사용 가능

#### 동기화 메서드

```java
public synchronized void method() {
	// 임계 영역
}
```

- 동기화 메서드는 메서드 전체 내용이 임계 영역이므로, 스레드가 동기화 메서드를 실행하는 즉시 객체에는 잠금이 일어나고, 스레드가 해당 메서드를 실행 종료하면 잠금이 풀림

#### 동기화 블록

```java
public void method() {
	// 여러 스레드가 사용 가능한 영역

	synchronized(공유객체) {
		// 임계 영역
	}

	// 여러 스레드가 사용 가능한 영역
}
```

- 동기화 블록의 외부 코드는 여러 스레드가 동시에 실행 가능
- 동기화 블록의 내부 코드는 임계 영역이므로 한 번헤 하나의 스레드만 실행 가능
- 만약 동기화 메서드와 블록이 여러 개인 경우, 스레드가 이들 중 하나를 실행할 때 다른 스레드는 해당 메서드는 물론 다른 동기화 메서드 및 블록 실행 불가능 (일반 메서드는 실행 가능)

#### 4.1 의 공유 객체 수정

##### 동기화 메서드로 구현

```java
public class Calculator {
	private int memory;

	public int getMemory() {
		return memory;
	}

	public synchronized void setMemory(int memory) {
		this.memory = memory;
		... // 이후 로직 동일
	}
}
```

##### 동기화 블록으로 구현

```java
public class Calculator {
	private int memory;

	public int getMemory() {
		return memory;
	}

	public void setMemory(int memory) {
		synchronized (this) {
			this.memory = memory
			try {
				Thread.sleep(2000);
			} catch (InterruptedException e) {
			}
		}
	}
}
```

1. User1 스레드가 Calculator 객체의 setMemory()를 실행하는 순간 해당 객체를 잠금
2. 메인 스레드가 User2 스레드를 실행하지만, 동기화 메서드를 실행하진 못하고 대기 (User1 종료시까지)
3. User1 스레드가 setMemory() 를 모두 실행한 뒤 User2 스레드가 해당 메서드를 실행
   <br><br><br>

# 5️⃣ 스레드 상태

![image](https://github.com/user-attachments/assets/a7b3836f-8afd-417e-8148-9391f328e1af)

#### 1. 실행 대기 상태 (Runnable)

- 아직 스케줄링되지 않아서 실행을 기다리고 있는 상태

#### 2. 실행 상태

- 실행 대기 상태에 있는 스레드 중 스레드 스케줄링으로 선택된 스레드가 CPU를 점유, `run()` 메서드를 실행한 상태
- `run()` 메서드를 모두 실행하기 전에 스레드 스케줄링에 의해 다시 실행 대기 상태로 돌아갈 수 있음
- 실행 대기 상태에 있는 다른 스레드가 선택되어 실행 상태가 됨
- 스레드는 실행 대기 상태와 실행 상태를 번갈아가면서 자신의 `run()` 메서드를 조금씩 실행

#### 3. 종료 상태 (Terminated)

- `run()` 메서드가 종료되면, 더 이상 실행할 코드가 없어 스레드의 실행이 멈춘 상태

![[Pasted image 20240528172653.png|500]]

#### 4. 일시 정지 상태

- 경우에 따라 스레드가 실행 상태에서 실행 대기 상태로 가지 않을 수 있음
- 일시 정지 상태는 스레드가 실행할 수 없는 상태
- **WAITING, TIMED_WAITING, BLOCKED**
- 스레드가 다시 실행 상태로 가기 위해서는 일시 정지 상태에서 실행 대기 상태로 가야함

| **상태**  | **열거 상수** |                           **설명**                           |
| :-------: | :-----------: | :----------------------------------------------------------: |
| 객체 생성 |      NEW      | 스레드 객체가 생성, 아직 start() 메서드가 호출되지 않은 상태 |
| 실행 대기 |   RUNNABLE    |             실행 상태로 언제든지 갈 수 있는 상태             |
| 일시 정지 |    WAITING    |          다른 스레드가 통지할 때까지 기다리는 상태           |
| 일시 정지 | TIMED_WAITING |                주어진 시간동안 기다리는 상태                 |
| 일시 정지 |    BLOCKED    |    사용하고자 하는 객체의 락이 풀릴 때까지 기다리는 상태     |
|   종료    |  TERMINATED   |                       실행을 마친 상태                       |

<br><br><br>

# 6️⃣ 스레드 상태 제어

#### 스레드 상태 제어란?

- 실행 중인 스레드의 상태를 변경하는 것
- 멀티 스레드 프로그램을 만들기 위해서는 정교한 스레드 상태 제어가 필요

|         메서드          |                                                                                                                                             설명                                                                                                                                              |
| :---------------------: | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|       interrupt()       |                                                                      일시 정지 상태의 스레드에서 `InterruptedException` 예외를 발생시켜, 예외 처리 코드에서 실행 대기 상태로 가거나 종료 상태로 갈 수 있도록 하는 메서드                                                                      |
| notify()<br>notifyAll() |                                                                                            동기화 블록 내에서 `wait()` 메서드에 의해 일시 정지 상태에 있는 스레드를 실행 대기 상태로 만드는 메서드                                                                                            |
|        resume()         |                                                                             `suspend()` 메서드에 의해 일시 정지 상태에 있는 스레드를 실행 대기 상태로 만드는 메서드 Deprecated (대신 notify(), notifyAll() 사용)                                                                              |
|         sleep()         |                                                      - sleep(long millis), sleep(long millis, int nanos) 로 나뉨<br>- 주어진 시간 동안 스레드를 일시 정지 상태로 만든다. 주어진 시간이 지나면 자동적으로 실행 대기 상태로 만드는 메서드                                                       |
|         join()          |                             - join(), join(long millis), join(long millis, int nanos)<br>- join() 메서드를 호출한 스레드는 일시 정지 상태가 된다. 실행 대기 상태로 가려면, join() 메서드를 멤버로 가지는 스레드가 종료되거나, 매개값으로 주어진 시간이 지나야 함                              |
|         wait()          | - wait(), wait(long millis), wait(long millis, int nanos)<br>- 동기화(synchronized) 블록 내에서 스레드를 일시 정지 상태로 만듦<br>- 매개값으로 주어진 시간이 지나면 자동적으로 실행 대기 상태가 된다. 시간이 주어지지 않으면 notify(), notifyAll() 메서드에 의해 실행 대기 상태로 갈 수 있다. |
