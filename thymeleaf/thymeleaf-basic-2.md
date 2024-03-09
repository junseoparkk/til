# Thymeleaf Basic 2

![image](https://github.com/junseoparkk/til/assets/98972385/ac7607c7-9cc5-48c8-8912-985eff87eb4d)

## 1️⃣ 텍스트
- 타임리프는 HTML 태그 속성에 기능을 정의해서 동작
- 콘텐츠 데이터 출력
  - `th:text`
  - `th:utext`

### 1) text
- HTML 콘텐츠에 직접 데이터 출력
<br>
  ```html
    <span th:text="${data}></span>
  ```

- HTML 콘텐츠 영역 안에 직접 데이터 출력
<br>
  ```html
    <span>[[{$data}]]</span>
  ```
<br><br>

### ❗️ HTML 엔티티 & 이스케이프
- 웹 브라우저는 `<`를 HTML 태그 시작으로 인식
- `<`를 태그가 아닌 문자로 표현하는 방법 : HTML 엔티티
- HTML 엔티티로 변경하는 것 : 이스케이프 (escape)
<br><br>

### 2) utext
- HTML 콘텐츠에 직접 데이터 출력
<br>
  ```html
    <span th:utext="${data}></span>
  ```

- HTML 콘텐츠 영역 안에 직접 데이터 출력
<br>
  ```html
    <span>[({$data})]</span>
  ```
<br><br>

#### ❗️ escape를 기본으로
- 개발시 escape를 사용하지 않아 HTML 랜더링이 안될 수 있음
- escape를 기본, 필요할 때만 unescape를 사용
---

### 2️⃣ SpringEL
- 변수를 사용할 때 변수 표현식을 사용 : `${...}`

#### 1) Object
- `object.xxx` = object.getXxx()
- `object['xxx']` = object.getXxx
- `object.getXxx()` = 직접 호출

#### 2) List
- `list[0].xxx` = list[0] 의 xxx 프로퍼티 접근
- `list[0]['xxx']`
- `list[0].getXxx()`

#### 3) Map
- `map['key'].xxx` = key값으로 찾은 객체의 xxx 프로퍼티 접근
- `map['key']['xxx']`
- `map['key'].getXxx()`

#### 지역 변수
- `th:with="var=${object}"`

## 3️⃣ 기본 객체
- HttpServletRequest
  - `${request}`
- HttpServletResponse
  - `${response}`
- HttpSession
  - `${session}`
- ServletContext
  - `${servletContext}`
- Locale
  - `${#locale}`
- Request Parameter (쿼리 파라미터)
  - e.g) localhost:8080?param=paramData
  - `${param.paramData}`
- Session
  - `${session.sessionData}`
- Spring Bean
  - `${@beanName.method()}`

## 4️⃣ 날짜 포맷
`${#temporals.format(localDateTime, 'yyyy-MM-dd HH:mm:ss)}`
  
## 5️⃣ URL 링크

- 단순 URL
  - `@{...}`
- 쿼리 파라미터
  - `@{/user(name=${username, age=${age}})}`
  - e.g) /user?username=testname&age=testage
- 경로 변수
  - `@{/user/{userId}/save/{username}(userId=${userId}, username=${userName})}`
  - `@{|/user/${userId}/save/${username}|}`
- 경로 변수 + 쿼리 파라미터
  - `@{/user/{userId}(userId=${userId}, age=${userage})}`
  - e.g) /user/1?age=20

## 6️⃣ 리터럴
- 리터럴은 소스 코드상에 고정된 값
- 문자 리터럴을 항상 `''` 로 감싸야 함
  - `th:text="'hello'"`
  - 문자 리터럴은 하나의 토큰만 인식함
  - `th:text="'hello world'"` : 오류 발생
  - `||` 로 감싸면 정상 작동
  - `th:text="|hello world|"`
- 리터럴 대체 문법
  - `th:text="|hello ${data}|"`

## 7️⃣ 연산

### 1) 비교 연산
- > : gt
- < : lt
- >= : ge
- <= : le
- ! : not
- == : eq
- != : neq, ne

### 2) 조건식
- Java 조건식과 유시
- `th:text="(10 % 2 == 0) ? '짝수':'홀수'"`

### 3) Elvis 연산자
- 조건식 편의 연산자
- `th:text="${data} ? : 'null값'"`
- `th:text="${nullData} ? : "null값"`

### 4) _ 연산자
- 타임리프가 실행되지 않는 것처럼 동작
<br>
  ```html
  <span th:text="${data} ? : _">Null Data</span>
  ```

## 8️⃣ 태그 속성

### 1) 속성 설정
- `th:*` 속성을 지정하면 기존 속성을 대체. 없다면 새로 만듦
- `th:attrappend` : 속성 값 뒤에 값 추가
- `th:attrprepend` : 속성 값 앞에 값 추가
- `th:classappend` : class 속성에 추가 (보통 이걸로 사용)

### 2) Checked 처리
- HTML에서는 'checked' 속성이 있는 경우 아예 checked 처리가 되어버림
- 타임리프에서는 'checked'가 false인 경우 속성 자체를 제거
  - `th:checked="false"`

## 9️⃣ 반복

### 1) 반복 기능
- `th:each="data: ${datas}"`
- List 뿐만 아니라 Iterable, Enumeration을 구현한 모든 객체에서 반복 사용 가능
- Map을 사용할 경우 Map.Entry가 담긴다.

### 2) 반복 상태 유지
- `th:each="data, dataStat : ${datas}"`
- 두 번째 파라미터는 생략 가능
  - 지정한 변수명 (data) + Stat = dataStat
- 반복 상태 유지 기능
  - index : 0부터 시작하는 값
  - count : 1부터 시작하는 값
  - size : 전체 크기
  - even, odd : 홀수, 짝수 여부 (boolean)
  - first, last : 처음, 마지막 여부 (boolean)
  - current : 현재 객체 (그냥 해당 객체 사용하면 됨)

## 1️⃣0️⃣ 조건부 평가

### 1) if, unless
- `th:if` , `th:unless`
- 타임리프에선 조건식이 맞지 않으면 태그 자체를 렌더링하지 않음
<br>

```html
    <span th:text="미성년자" th:if="${user.age lt 20}"></span>
```

### 2) switch
```html
<tr th:each="user, userStat : ${users}">
    <td th:text="${userStat.count}">1</td>
    <td th:text="${user.username}">username</td>
    <td th:switch="${user.age}"></td>
        <span th:case="10">10살</span>
        <span th:case="20">20살</span> 
        <span th:case="*">기타</span>
    </td> 
</tr>

```
<br><br>
---
references
###### 인프런 김영한님 강의 - 스프링 MVC 2편

