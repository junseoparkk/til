# HTTP

### 1. 개념
- HTTP (HyperText Tranfrer Protocol)
- W3 상에서 정보를 주고받을 수 있는 프로토콜
- 클라이언트 - 서버간 메시지 교환 규칙
- HTTP Message 를 통해 데이터 교환
- 연결 방식
    - TCP 연결 : HTTP/1.0, HTTP/1.1, HTTP/2
    - UDP 연결 : HTTP/3 (도입중)
- HTTP/2 는 HTTPS 연결에서만 작동한다. 만약 HTTP 연결일 경우, HTTP/1.1로 연결 된다.
<br><br>

### 2. 구조
#### `1. HTTP Message` : ASCII 로 인코딩된 텍스트 정보, 클라이언트 - 서버간 데이터 교환 방식
- HTTP/1.1 에서는 클라이언트 - 서버 연결을 통해 공개적으로 전달
- HTTP/2 에서는 최적화와 성능 향상을 위해 HTTP 프레임으로 나누어졌다.
- Start-Line, Header, Body 로 구성
<br><br>

#### `2. HttpRequest(요청)` : 클라이언트 -> 서버 : 데이터 전달하여 필요한 동작을 요청 
1. Start-Line : [HTTP Method] [URL/Protocol/Port/Domain 등 절대 경로] [Protocol Version]
   1) HTTP Method : 서버가 수행할 동작 ex) GET, PUT, POST, PATCH, DELETE, HEAD, OPTIONS etc.
   2) Request Target : 절대 경로로 나타낼 수 있다.
   3) Protocol Version : 응답 메시지에서 사용할 HTTP 버전
   - ex) GET http://www.github.com HTTP/1.1
<br><br>

2. Header : Request Headers, General Headers, Representation Headers
   1) Request Headers
      - Host : 서버의 도메인명과 서버가 받는 TCP 포트 번호 명시
      - User-Agent : 사용자 에이전트의 애플리케이션 타입, OS, SW 버전/벤더 등 포함
      - Accept : 반환할 데이터 타입을 서버에 알려준다. MIME 타입이다.
      - Accept-Language : 서버가 돌려줄 예상 언어 ex) ko, en-US 등
      - Accept-Encoding : 인코딩 알고리즘 ex) UTF-8 등
   2) General Headers
      - Connection : 현재 트랜잭션이 끝난 뒤 네트워크 연결을 열린 상태로 둘지 여부
      - Upgrade-Insure-Requests : 암호화된 응답
   3) Representation Headers
      - Content-Type : 수신자에게 전송할 자원의 형식
      - Content-Length : 사용자를 위한 언어를 설명하여 선호하는 언어에 따라 구분
<br><br>

3. Body
- 요청의 마지막 부분에 들어가며, 일부 요청에는 포함되지 않는다.
- GET, DELETE, HEAD, OPTIONS 등 리소스를 가져오는 요청에서는 필요가 없다.
- PUT, PATCH 등의 메소드는 서버에 요청할 리소스를 본문에 담아 보낸다.
- 타입 : JSON, TEXT, HTML, XML 등
<br><br>

#### `3. HttpResponse(응답)` : 서버 -> 클라이언트 : 클라이언트 요청에 대한 서버의 답변
1. Start-Line : [Protocol Version] [Status Code] [Status Text]
   1) Protocol Version : 응답 메시지에서 사용할 HTTP 버전
   2) Status Code : 요청의 성공 여부 ex) 200, 404, 302
   3) Status Text : 상태 코드에 대한 설명을 짧은 글로 나타낸 것
   - ex) HTTP/1.1 404 Not Found
<br><br>

2. Header : General Headers, Response Headers, Entity Headers
   1) General Headers : 메시지 전체에 적용되는 헤더
   2) Response Headers : 상태 줄에 담지 못한 서버에 대한 추가 정보를 제공
   3) Entity Header : 요청 내에 본문이 있는 경우, 본문 내용과 관련된 정보를 제공
<br><br>

### 4. HTTP Request Data
1. GET - Query Parameter
   - /url?key1=value1&key2=value2 형식
   - 메시지 바디 없이 URL 의 쿼리 파라미터에 데이터를 포함하여 전달
   - ex) 검색, 필터, 페이징 등에서 주로 사용
2. POST - HTML Form
   - content-type: application/x-www-form-urlencoded -> HTML Form 으로 전달된 데이터
   - 메시지 바디에 쿼리 파라미터 형식으로 데이터를 포함하여 전달
   - ex) 회원 가입, 상품 주문 등에서 주로 사용
3. HTTP message body
   - body 에 데이터를 직접 담아서 요청
   - HTTP API 에서 주로 사용 ex) JSON, XML, TEXT
   - 주로 JSON 형식으로 데이터를 전달
   - POST, PUT, PATCH

