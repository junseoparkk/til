# Docker 기본 명령어

### 도커 버전 확인
```bash
# docker version 명령어가 더 자세히 보여준다.
docker version
docker -v
```
<br><br>

## 1. 컨테이너 제어 명령어

### 1) 컨테이너 내려받기
```bash
# docker pull NAME[:TAG]
$ docker pull mysql:5.7
```
<br>

### 2) 컨테이너 실행 (없다면 내려받음)
```bash
# docker run [OPTION] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]
$ docker run ubuntu:20.04
$ docker run --rm -it ubuntu:20.04 /bin/sh
$ docker run --rm -it centos:8 /bin/sh
```
<br>

|OPTION|Meaning|
|:---:|:---:|
|-d|detached mode(백그라운드 모드)|
|-p|호스트와 컨테이너 포트 연결(바로 뒤에 포트 작성)|
|-v|호스트와 컨테이너 디렉토리 연결|
|-e|컨테이너 내 사용할 환경변수 설정|
|--name|컨테이너 이름 설정|
|--rm|프로세스 종료시 컨테이너 자동 제거|
|--it|-i와 -t를 동시에 사용한 것. 터미널 입력 옵션|
|--network|네트워크 연결|
<br>

### 3) 컨테이너 이름 지정
```bash
# docker run --name [설정할 이름] IMAGE[:TAG]
$ docker run -it --name my_ubuntu ubunt:16.04 /bin/sh
```
<br>

### 4) 컨테이너 포트 연결
```bash
# docker run -p port:port IMAGE[:TAG]
$ docker run --rm -p 5678:5678 hashicorp/http-auto -text="hello"
```
<br>

### # Redis 연결

mac m1 이상인 경우 telnet을 설치해줘야 한다.
만약 'Homebrew'가 설치되어 있다면 다음 명령어 입력.

```bash
$ brew tap theeternalsw0rd/telnet
$ brew install telnet
```

```bash
$ docker run --rm -p 1234:6379 redis
$ telent localhost 1234
```
<br>

### # MySQL 연결
```bash
$ docker run -d -p 3306:3306 -e MYSQL_ALLOW_EMPTY_PASSOWRD=true --name mysql:5.7 
```
```bash
$ docker exec -it mysql mysql
$ create database db CHARACTER SET utf8;
$ grant all privileges on wp.* to dp@'%' identified by 'db';
$ flush privileges;
$ quit
```
<br>

여기서 'exec' 명령어는 'run' 명령어와는 달리 실행중인 도커 컨테이너에 접속할 때 사용.<br>
컨테이너 안에 ssh server 등을 설치하지 않고 exec 명령어로 접속.
<br><br>

## 2. 컨테이너 관리 명령어 

### 1) 컨테이너 목록 확인
```bash
# 현재 가동되고 있는 컨테이너 정보 출력
$ docker ps

# 모든 컨테이너 목록 확인
$ docker ps -a
```
<br>

### 2) 컨테이너 재시작 및 연결
```bash
# 중지된 컨테이너 재시작
$ docker start my_container

# 해당 컨테이너 접속
$ docker attach my_container
```
<br>

### 3) 컨테이너 정지
```bash
$ docker stop 
```
<br>

### 4) 컨테이너 종료
```bash
# 컨테이너 종료
$ exit
$ docker stop 
```
<br>

### 5) 컨테이너 삭제
```bash
# 삭제
$ docker rm

# 정지와 삭제 동시에
$ docker rm -f

# 컨테이너 목록에 있는 모든 컨테이너 삭제
$ docker container prune

# 컨테이너 종료시 자동 삭제
$ docker --rm 
```
<br>

## 3. 이미지 관련 명령어

### 1) 도커 이미지 목록
```bash
$ docker images
```
<br>

### 2) 도커 이미지 삭제
```bash
# docker rmi IMAGE[:TAG]
$ docker rmi ubuntu:20.4
```