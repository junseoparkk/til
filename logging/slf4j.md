# Logging - SLF4J

### SLF4J
수 많은 로깅 라이브러리들을 통합하여 인터페이스로 제공한 것. SLF4J는 인터페이스이고, Logback같은 로그 라이브러리를 선택. 실무에서는 Spring boot가 기본으로 제공하는 Logback 주로 사용.
<br><br>

### 1. 로그 선언
``````java
1. private Logger log = LoggerFactory.getLogger(getClass());
2. private static final Logger log = LoggerFactory.getLogger(xxx.class)
3. @Slf4j //Annotation
``````
<br>

### 2. 로그 호출
``````java
1. log.info("content"); (O)
2. System.out.println("content"); (X)
``````
<br>

### 3. 로그 테스트
- 로그가 출력되는 형식 확인
  - 시간, 로그 레벨, 프로세스 ID, 쓰레드명, 클래스명, 로그 메시지
- 로그 레벨 설정
  - LEVEL : TRACE > DEBUG > INFO > WARN > ERROR
  - 개발 서버 : DEBUG
  - 운영 서버 : INFO
<br><br>

### 4. 로그 레벨 설정
``````java
//[application.properties]

logging.level.root=info
logging.level.packageA.packageB=debug //packageA > packageB
``````
<br>

### 5. 로그 사용법
``````java
/** 
 * 만약 로그 출력 레벨을 'INFO'보다 상위로 설정하더라도, '+' 로 인해 연산이 수행된다.
 * 이때문에 불필요한 메모리를 사용할 수 있다. 
 */
1. log.debug("content=" + content)

// 해당하지 않는 로그 출력 레벨이라면 건너뛴다.
2. log.debug("date={}", content)
``````
<br>

### 6. 로그 사용시 장점
- 쓰레드 정보, 클래스 이름 등 정보를 함께 볼 수 있다.
- 사용자 정의 출력 형식
- 로그 레벨에 따라 서버별로 로그 사용 범위 설정 가능
- 파일, 네트워크 등 위치에 별도로 로그 기록 가능. 특히 특정 용량에 따라 로그를 분할하는 것도 가능.
<br><br>


###### [참고]<br>인프런 김영한님 강의 - 스프링 MVC 1편  