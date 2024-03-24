# 기술 면접 질문 - 네트워크

## 1) REST API

### Q. REST란?
- 'Representational Status Transfer'의 약자
- 자원을 이름으로 구분하여 해당 자원의 상태를 주고받는 모든 것
- HTTP URI로 자원을 명시하고, HTTP Method를 통해 해당 자원에 대한 CRUD 연산을 적용하는 것
  - C (Create) : POST
  - R (Read) : GET
  - U (Update) : PUT
  - D (Delete) : DELETE
<br><br>
---

### Q. API란?
- 'Application Programming Interface'의 약자
- 응용 프로그램에서 사용할 수 있도록 운영체제나 프로그래밍 언어가 제공하는 기능을 제어할 수 있도록 만든 인터페이스
- ex) REST API, Open API
<br><br>
---

### Q. REST 구성 요소
1. 자원 (Resource) : HTTP URI
2. 자원에 대한 행위 (Verb) : HTTP Method
3. 자원에 대한 행위의 내용 (Representations) : HTTP Message Pay Load
<br><br>
---

### Q. REST 특징
- Server - Client 구조
- Stateless (무상태)
- Cacheable (캐시 처리 가능)
- Layerd System (계층화)
- Uniform Interface (인터페이스 일관성)
<br><br>
---

### Q. REST 장단점
1. 장점
  - HTTP 프로토콜의 인프라를 그대로 사용하므로 REST API 사용을 위한 별도의 인프라 구축이 필요 없음
  - HTTP 프로토콜의 표준을 최대한 활용해 여러 장점을 함께 가져갈 수 있음
  - HTTP 표준 프로토콜을 따르는 모든 플랫폼에서 사용 가능
  - REST API 메시지가 의도하는 바를 명확히 나타내므로 의도하는 바를 쉽게 파악 가능
  - 서버 - 클라이언트의 역할을 명확하게 분리
2. 단점
  - 표준 자체가 존재하지 않아 정의가 필요
  - HTTP Method 형태가 제한적
  - 구형 브라우저에서 호환되지 않아 지원하지 않는 동작이 있음
<br><br>
---

### Q. REST API란?
- REST 원리를 따르는 API를 의미
<br><br>
---

## 2) 네트워크

### Q. OSI 7 Layer
- 'Open Systems Interconnection'의 약자로, 서로 다른 시스템 또는 장치간의 네트워크 통신을 위한 표준 모델
- OSI는 네트워크 통신에 필요한 구성요소들을 7계층으로 분리하여 표준화함

- TO-BE : 각 계층별 설명
<br><br>
---

### Q. TCP/UDP
- 전송 계층의 주요 프로토콜

||TCP|UDP|
|:---:|:---:|:---:|
|연결방식|연결형|비연결형|
|패킷교환|가상 회선 방식|데이터그램 방식|
|순서 보장|보장|보장하지 않음|
|신뢰성|높음|낮음|
|전송속도|느림|빠름|

<br><br>
---

### Q. IP
- 네트워크에 연결되는 장치들을 식별하기 위한 주소체계. 모든 네트워크 인터페이스는 고유 번호를 부여 받음.
- IPv4는 32bit, IPv6는 128bit
- 매우 큰 주소 범위를 가지기 때문에 이를 효과적으로 관리하기 위해 IP Class로 구조화
  - Class A : 1-126, 대규모 네트워크에서 사용
  - Class B : 127~191, 중형 네트워크에서 사용
  - Class C : 192~223, 소형 네트워크에서 사용
<br><br>
---

### Q. NAT
- 'Network Address Translation'의 약자로, 내부 네트워크의 장치가 인터넷에 접근할 수 있도록 하는 네트워킹 기술
- 내부 네트워크는 private ip를 사용하기 때문에 직접적으로 인터넷에 연결할 수 없음. NAT는 private ip <-> public ip로 변환
<br><br>
---

### Q. VPC
- 'Virtual Private Cloud'의 약자로, 가상의 네트워크를 구성하는 것.
- VPC를 사용해 자체 데이터 센터로 생각할 수 있는 논리적으로 격리된 네트워크를 만들 수 있음
- VPC 네트워크는 IP 주소, 서브넷, 라우팅 테이블, 인터넷 게이트웨이, NAT를 포함해 가상 네트워크 환경을 제어할 수 있음






