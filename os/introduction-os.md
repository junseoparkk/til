# Introduction OS

## 1️⃣ 운영체제 (OS, Operating System)

![image](https://github.com/junseoparkk/til/assets/98972385/8326b7ba-98a1-4e3a-bb73-270b60d6ffa8)


### 1) 운영체제란?
운영체제(OS, Operating System)는 컴퓨터 하드웨어 바로 위에 설치되어 사용자 및 다른 모든 소프트웨어, 하드웨어를 연결하는 소프트웨어 계층이다.

- 협의의 운영체제(커널) : 운영체제의 핵심 부분, 메모리에 상주
- 광의의 운영체제 : 커널 뿐만 아니라 다른 각종 주변 시스템 유틸리티까지 포함
<br>

### 2) 운영체제의 목적

#### 1. 컴퓨터 시스템 자원을 효율적으로 관리
- 프로세서, 기억장치, I/O장치 등 하드웨어 자원을 효율적으로 관리. 사용자간 형평성 있는 자원 분배(형평성) + 주어진 자원으로 최대 성능(효율성)
- 사용자 및 운영체제 자신을 보호
- 프로세스, 파일, 메시지 등 소프트웨어 자원을 효율적으로 관리

#### 2. 컴퓨터 시스템을 편리하게 사용할 수 있는 환경 제공
- 동시 사용자/프로그램들이 각각 독립적인 컴퓨터에서 수행되는 것 같은 환경 제공
- 하드웨어를 다루는 복잡한 부분을 운영체제가 대행
<br>

### 3) 운영체제의 종류

#### 1. 동시 작업 가능 여부
- 단일 작업 (single tasking) : 한 번에 하나의 작업만 처리 ex)MS-DOS (한 명령에 대한 수행이 끝나기 전에 다른 명령 수행 불가능)
- 다중 작업 (multi tasking) : 대부분 현대의 운영체제, 동시에 두 개 이상의 작업을 처리 ex)UNIX, MS Windows

#### 2. 사용자의 수
- 단일 사용자 (single user) ex)MS-DOS, MS-Windows
- 다중 사용자 (multi user) ex)UNIX, NT server (서버의 개념) -> 컴퓨터 한 대를 여러 사용자가 동시에 접속이 가능

#### 3. 처리 방식
- 일괄 처리 (batch processing) : 작업 요청의 일정량을 모아서 한 번에 처리, 작업 종료될 때까지 기다려야 함
- 시분할 (time sharing) : 대부분 현대의 운영체제, 여러 작업을 수행할 때 컴퓨터 처리 능력을 일정 시간 단위로 분할하여 사용. 일괄처리에 비해 수행 시간이 짧다. -> 'interactive'한 방식 (키보드로 입력하면 결과를 바로 확인) ex)UNIX
- 실시간 (Realtime OS) : 정해진 시간 내에 어떤 일이 반드시 종료됨이 보장되어야 하는 실시간 시스템을 위한 운영체제. ex)원자로/공장 제어, 미사일 제어, 반도체 제어, 로봇 제어 등 => 정확한 시간을 맞춰야 함
<br><br>

✳︎ 실시간 방식의 분류
- 경성 실시간 시스템 (Hard Realtime system) : 데드라인을 정확히 지켜야 함
- 연성 실시간 시스템 (Soft Realtime system) : 데드라인은 있지만 지켜지지 못해도 큰 피해는 없음

#### 4. 용어
Multitasking ≒ Multiprogramming ≒ Time sharing ≒ Multiprocess
- 해당 용어들은 기본적으로 컴퓨터에서 여러 작업을 동시에 수행하는 것을 뜻함
- Multiprogramming : 여러 프로그램이 메모리에 올라가 있음을 강조 (메모리 측면)
- Time Sharing : CPU 시간을 분할하여 나누어 쓴다는 의미 강조 (CPU 측면)
- Multiprocessor : 하나의 컴퓨터에 CPU(processer)가 여러 개 붙어있음을 의미
<br><br>

## 2️⃣ 운영체제의 예

### 1) 유닉스 (UNIX)
- 대형 컴퓨터를 위해 만들어짐
- 대부분의 커널 코드는 C언어로 작성 : C언어는 유닉스를 만들기 위해 탄생한 언어
- 높은 이식성 : C언어로 작성되었기 때문
- 최소한의 커널 구조 : 핵심적인 부분만 커널에 넣음
- 복잡한 시스템에 맞게 확장에 용이
- 소스 코드 공개 : 오픈소스
- 다양한 버전 ex) System V, FreeBSD, SunOS, Solaris, Linux

### 2) DOS (Disk Operating System)
- 개인용 PC를 위해 만들어짐
- MS사에서 IBM-PC를 위해 개발
- 단일 사용자용 운영체제
- 메모리 관리 능력의 한계 (주 기억 장치의 최대 용량 : 640KB)

### 3) MS-Windows
- 개인용 PC를 위해 만들어짐
- 다중 작업용 GUI 기반 운영체제
- Plug and Play, 네트워크 환경 강화
- DOS용 응용 프로그램과 호환성 제공
- 풍부한 지원 소프트웨어 
---
<br>

references<br>
KOCW 반효경-Introduction to Operating Systems