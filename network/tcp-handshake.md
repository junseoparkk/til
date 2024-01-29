# 3-way & 4-way handshake (TCP Connection)

## 1. TCP (Transmission Control Protocol)

TCP (Transmission Control Protocol) 란 전송 제어 프로토콜로, IP와 함께 'TCP/IP'로도 불린다. OSI 7 계층 중 전송 계층과 관련있으며, UDP와 함께 가장 널리 쓰인다. TCP는 연결형 서비스로, 패킷의 순서를 보장하며 신뢰성있는 전송을 위해 연결을 시작할 때와 종료할 때 handshake 과정을 거치게 된다. 

즉, 3-way handshake 과정을 통해 연결을 설정하고, 4-way handshake 과정을 통해 안전하게 연결을 해제한다. TCP와 UDP의 개념은 중요하므로 다른 글에서 좀 더 자세히 다뤄보도록 하겠다.
<br><br>

## 2. Information

### 1) Port State Information (포트 상태 정보)

- CLOSED : 포트가 닫힌 상태
- LISTEN : 포트가 열린 상태로 연결 요청 대기 중
- SYN_RCV : SYNC 요청을 받아 상대방 응답 대기 중
- ESTABLISHED : 포트 연결 상태
<br><br>

### 2) Flag Information (플래그 정보)
TCP 헤더에는 CONTROL BIT(FLAG BIT, 6bit)가 존재하며, 각각의 bit는 순서대로 `URG-ACK-PSH-RST-SYN-FIN`을 의미한다. 만약 두 번째 위치의 bit가 1이면 해당 패킷이 담고 있는 내용을 알 수 있다.
<br>

- URG(Urgent) : 긴급 데이터(urgent data)가 포함되어 있음을 나타낸다. 긴급 데이터는 주로 TCP 스트림의 중요도가 높아 즉시 처리되어야 함을 의미한다.
- ACK(Acknowledgment) : 확인 응답 번호(ACK Number)가 유효함을 나타낸다. 해당 플래그가 설정되면 다음 번호로 전송할 준비가 된 데이터가 존재함을 의미한다.
- PSH(Push) : 수신측에게 데이터를 즉시 전달하라는 요청을 나타낸다. 버퍼링을 최소화하여 데이터를 즉시 처리하도록 유도한다.
- RST(Reset) : 연결을 재설정하고 상태를 초기화하는데 사용된다. 일반적으로 오류가 발생하거나 연결을 완전히 종료할 때 사용된다.
- SYN(Synchronize Sequence Number) : 연결 설정을 시작하는데 사용된다. 초기 연결을 설정할 때 사용되며, 시퀀스 번호를 동기화하고 통신의 시작을 알린다.
- FIN(Finish) : 송신자가 데이터 전송을 완료했음을 나타낸다. 종료 프로세스를 시작하기 위해 사용된다.
<br><br>

## 3. 3-way handshake

### 1) 개념
3-way handshake는 TCP/IP 프로토콜을 통해 통신하는 응용 프로그램이 데이터 전송을 시작하기 전에 신뢰성있는 전송을 보장하기 위해 통신 대상이 되는 호스트와 사전에 세션을 수립하는 과정이다.

이 때 데이터를 전송하기 전에 연결을 확립하기 위해 해킷 요청을 세 번 교환하는 것을 '3-way handshaking'이라고 한다. 이는 네트워크 통신에서 확실하게 데이터가 전송되었는지 확인하면서 이루어지는 통신 방식이다.
<br><br>

### 2) 과정
![image](https://github.com/dev-study-team/2024-CS-Study/assets/98972385/6e4e88ca-4d37-4dff-9e10-33953d2ab72d)
<br><br>

### 1. [Client -> Server]

네트워크 통신을 위해 수신 측 컴퓨터의 허락을 받아야 한다. 따라서 송신 측 컴퓨터는 수신 측 컴퓨터에 연결 확립 허가를 받기 위해 'SYN' 요청을 보낸다. 

이 때 송신자가 최초로 데이터 전송시 'Sequence Number'를 임의의 숫자로 지정하고, SYN 플래그 비트를 1로 설정한 세그먼트를 전송한다. 클라이언트 측 포트 상태는 'CLOSED' -> 'SYN_SENT' 로 변환되며, 서버 측 포트 상태는 'LISTEN'이 된다.
<br><br>

### 2. [Server -> Client]

수신 측 컴퓨터는 송신 측 컴퓨터가 보낸 요청을 받고, 허가 응답 회신을 위해 연결 확립 응답인 'ACK'를 보낸다. 이와 동시에 수신 측 컴퓨터는 송신 측 컴퓨터로부터 데이터 전송 허가를 받기 위해 연결 확립 요청인 'SYN'을 보낸다. 

이 때 연결을 확립하기 위해 코드 비트의 SYN과 ACK가 1로 활성화되며, ACK Number 필드를 'Sequence Number + 1'로 지정하여 새그먼트를 전송한다. 클라이언트 측 포트 상태는 'CLOSED', 서버 측 포트 상태는 'SYN_RCV'가 된다.
<br><br>

### 3. [Client -> Server]

수신 측 컴퓨터의 요청을 받은 송신 측 컴퓨터는 수신 측 컴퓨터에 허가한다는 응답으로 연결 확립 응답인 'ACK'를 보낸다.

이 때 전송할 데이터가 있으면 이 단계에서 데이터 전송이 가능하다. 클라이언트 측 포트 상태는 'ESTABLISHED', 서버 측 포트 상태는 'SYN_RCV' -> 'ESTABLISHED'가 된다.
<br><br>

![image](https://github.com/dev-study-team/2024-CS-Study/assets/98972385/1d7ce5e4-8c64-4572-8888-323ccf024429)
<br><br>

## 4. 4-way handshake

### 1) 개념
4-way handshake 는 3-way handshake와 반대로 연결을 해제하는 과정이다. 이 때 하나의 플래그가 더 추가되는데, 바로 'FIN(Finish)' 플래그이다. 세션 종료시 사용되며, 더 이상 전송할 데이터가 없음을 나타낸다. 명칭 그대로 4번의 메시지 교환이 이루어진다.
<br><br>

### 2) 과정
![image](https://github.com/dev-study-team/2024-CS-Study/assets/98972385/8d4ee4f4-ffcc-44c9-b42b-8cf53f3b2054)
<br><br>

### 1. [Client -> Server]

클라이언트가 통신을 종료하려고 할 때, 먼저 서버에게 연결을 종료하겠다는 의미를 담은 'FIN' 플래그가 설정된 TCP 세그먼트를 전송한다. 클라이언트 측은 'FIN_WAIT_1' 상태로 변한다
<br><br>

### 2. [Server -> Client]

서버는 클라이언트로부터 'FIN'을 받으면 이에 대한 확인 메시지인 'ACK'를 보내고, 클라이언트의 요청을 수락하여 통신이 끝날때까지 기다린다. 이 때 'TIME_WAIT' 상태로 바뀐다. 

이후 서버는 ACK Number 필드를 'Sequence Number + 1'로 지정하고, ACK 플래그 비트를 1로 설정한 세그먼트를 전송한다. 서버 측은 'CLOSE-WAIT' 상태가 되며, 서버가 종료될 준비가 되었다는 'FIN'을 전송하기 전까지 클라이언트 측은 'FIN_WAIT_2' 상태가 된다.
<br><br>
 
### 3. [Server -> Client]

서버는 통신을 종료하겠다는 의사를 표시하기 위해 'FIN' 플래그가 설정된 TCP 세그먼트를 클라이언트에게 보낸다. 이후 승인 번호를 보내줄 때까지 'LAST_ACK' 상태가 된다.
<br><br>

### 4. [Client -> Server]

클라이언트는 서버로부터 'FIN'을 받으면 이에 대한 확인 메시지인 'ACK'를 서버로 전송한다. 하지만 아직 서버로부터 수신하지 못한 데이터가 존재할 수 있으므로 'TIME_WAIT' 상태가 되어 기다린다. 이는 예상치 못한 오류로 인해 연결이 데드락 상태로 빠지는 것을 방지한다. 만약 오류가 발생하여 종료가 늦춰저 특정 시간이 초과되면 'CLOSED' 상태로 변한다.
<br><br>

![image](https://github.com/dev-study-team/2024-CS-Study/assets/98972385/a76485fc-50b2-426d-9bd2-b1e395a251f6)
<br><br>

##### 블로그
https://jnsodevelop.tistory.com/75
<br><br><br>

###### 참고
https://jeongkyun-it.tistory.com/180<br>
https://m.hanbit.co.kr/store/books/book_view.html?p_code=B7721595096<br>
https://www.javatpoint.com/tcp-connection-termination