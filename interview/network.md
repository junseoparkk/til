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


