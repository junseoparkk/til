# HttpServletRequest

### 1. HttpServletRequest 개념

클라이언트가 WAS에 요청을 하면 WAS는 'HttpServletRequest', 'HttpServletResponse' 객체를 생성하여 저장한다.

이 두 객체는 서블릿에 전달되는데, 그 중 'HttpServletRequest'는 요청 관련 객체이다.

해당 객체는 클라이언트의 요청에 대한 다양한 기능을 제공하는데, 다음과 같다.
<br><br>

#### 1) HTTP 요청 메시지 파싱 기능
- Start Line
  - HTTP Method
  - URL
  - Query String
  - Schma, Protocol
- Header
  - Host
  - User-Agent
  - Accept, Accept-Language, Accept-Encoding
- Body
  - Request Resources
<br><br>

HTTP 요청 메시지를 파싱하여 위 정보들을 HttpServletRequest 객체에 저장한다. 이후 서블릿에게 전달한다.
<br><br>

#### 2) 임시 저장소 기능

해당 HTTP 요청이 시작될 때부터 끝날 때까지 유지되는 임시 저장소 기능이 있다.

예를 들어 요청에 특정 객체를 담아 저장하는 기능, 꺼내오는 기능 등을 수행할 수 있다.
<br><br>

``````java
@WebServlet(name = "requestBasicServlet", urlPatterns = "/request-basic")
public class RequestBasicServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    	String data = "data1";
        request.setAttribute("data", data); //저장

        String findData = String.valueOf(request.getAttribute("data")); //조회
        System.out.println("findData = " + findData);
        
        response.getWriter().write("ok");
    }
}
``````
<br>

실제로 위처럼 콘솔에 출력하기 위한 용도라기보단, JSP 등의 뷰 페이지와 데이터를 주고 받기 위해 사용된다.

예를 들어 위처럼 'setAttribute()'를 통해 데이터를 저장한 뒤, JSP에서 'getAttribute()'를 통해 데이터를 가져 온다.
<br><br>

|메서드명|반환 타입|기능|
|:---:|:---:|:---:|
|setAttribute(String name, Object value)|void|'name' 을 이름으로 가지는 속성인 value 저장|
|getAttribute(String name)|Object|	'name' 을 이름으로 가지는 속성 값 조회. 없다면 null 반환|
|removeAttribute(String name)|	void|'name' 을 이름으로 가지는 속성 삭제|
|getAttributeNames()|Enumeration|	속성 이름 목록 조회|
<br>

#### 3) 세션 관리 기능
request.getSession(create: true) 등의 명령어를 통해 세션을 가져올 수도 있다.

---

### 2. HttpServletRequest 사용법

#### 1) Start Line

|메서드명|반환 예시|설명|
|:---:|:---:|:---:|
|getMethod()|GET, POST, DELETE, PUT	|HTTP 메소드명 반환|
|getProtocol()|HTTP/1.1, HTTP/1.2|	HTTP 버전 반환|
|getScheme()|http, https|HTTP, HTTPS 구분|
|getRequestURL()|http://localhost:8080/example|URL 반환|
|getRequestURI()|/example|URI 반환|
|getQueryString()|name=hello|ex) http://localhost8080?name=hello 쿼리 스트링 반환|
|isSecure()|true / false|HTTPS 사용 유무 반환|
<br>

``````java
private void printStartLine(HttpServletRequest request) {
        System.out.println("--- START REQUEST-LINE ---");

        System.out.println("request.getMethod() = " + request.getMethod());
        System.out.println("request.getProtocol() = " + request.getProtocol());
        System.out.println("request.getScheme() = " + request.getScheme());

        System.out.println("request.getRequestURL() = " + request.getRequestURL());
        System.out.println("request.getRequestURI() = " + request.getRequestURI());

        System.out.println("request.getQueryString() = " + request.getQueryString());
  
        System.out.println("request.isSecure() = " + request.isSecure());
        
        System.out.println("--- END REQUEST-LINE ---");
}
``````
<br>

#### 2) Header

|메서드명|반환 예시|설명|
|:---:|:---:|:---:|
|getServerName()|localhost|서버 이름 반환|
|getServerPort()|8080|포트 번호 반환|
|getLocale(), getLocales()|ko_KR, en_US, ko, en|언어 정보 반환|
|getCookies()|-|쿠키 반환|
|getContentType()|x-www-form-urlencode, application/json|데이터 타입 정보 반환|
|getContentLength()|숫자|데이터 길이 반환|
|getCharacterEncoding()|UTF-8|	데이터 인코딩 방식 반환|
<br>

``````java
private void printHeaderUtils(HttpServletRequest request) {
        System.out.println("--- START Header INFO ---");
        
        // Host
        System.out.println("request.getServerName() = " + request.getServerName());
        System.out.println("request.getServerPort() = " + request.getServerPort());
        System.out.println();

        // Accept-Length
        request.getLocales().asIterator()
                .forEachRemaining(locale -> System.out.println("locale = " + locale));
        System.out.println("request.getLocale() = " + request.getLocale());
        System.out.println();

        // Cookie
        if (request.getCookies() != null) {
            for (Cookie cookie : request.getCookies()) {
                System.out.println(cookie.getName() + ": " + cookie.getValue());
            }
        }
        System.out.println();

        // Content
        System.out.println("request.getContentType() = " + request.getContentType());
        System.out.println("request.getContentLength() = " + request.getContentLength());
        System.out.println("request.getCharacterEncoding() = " + request.getCharacterEncoding());

        System.out.println("--- END Header INFO ---");
}
``````
<br><br>

###### 참고 인프런 김영한님 강의 - 스프링 MVC 1편