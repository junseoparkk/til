# Thymeleaf Basic

![image](https://github.com/junseoparkk/til/assets/98972385/ac7607c7-9cc5-48c8-8912-985eff87eb4d)

## 1️⃣ Thymeleaf

### 1) 개념
- Java 웹 애플리케이션을 개발할 때 사용되는 서버 측 템플릿 엔진
- HTML, XML, JavaScript, CSS 및 일반 텍스트 처리에 사용
- 서버 측 렌더링을 수행하며, 클라이언트 측에서도 JS와 함께 사용 가능
<br><br>

### 2) 장점
- 자연스러운 문법 : Thymeleaf의 문법은 HTML과 유사하여 학습 곡선이 낮음
- 통합성 : Spring과의 통합을 통해 Spring 기반 웹 애플리케이션에서 간편하게 사용 가능
- SSR : 서버 측에 템플릿을 처리하여 동적인 컨텐츠 생성 가능
<br><br>

### 3) 단점
- 성능 : 서버 측 랜더링을 수행하므로 비동기적인 CSR보다는 성능이 안좋을 수 있다. 특히 많은 양의 동적 컨텐츠가 있는 경우 성능 저하가 더 두드러짐
- 복잡성 : 문법은 간단하지만, 복잡한 로직을 다룰 경우 코드가 복잡해질 수 있음
<br><br>

## 2️⃣ 기본 문법

### 타임리프 사용 선언
```html
<html xmlns:th="http://www.thymeleaf.org">
```

- `th` 키워드
  - `th:xxx` 가 있는 부분 : 서버사이드에서 랜더링되어 기존 것을 대체
  - `th:xxx` 가 없는 부분 : 기존 html의 속성이 그대로 사용
- HTML을 파일로 직접 열었을 때 웹 브라우저는 `th` 속성을 모르기 때문에 무시됨
- HTML 파일 보기를 유지하면서 템플릿 기능도 가능
- 대부분의 HTML 속성을 변경할 수 있음
<br><br>

### 1) URL 링크 표현식 - `@{...}`
🔎 e.g) `th:href="@{/css/style.css}"`
- URL 링크를 사용하는 경우 `@{...}` 사용 -> 링크 표현식
- URL 링크 표현식을 사용하면 서블릿 컨텍스트를 자동으로 포함
<br><br>

🔎 e.g) `th:href="@{/basic/users/{userId}(userId=${user.id})}"`<br>
🔎 e.g) `th:href="@{/basic/users/{userId}(userId=${user.id}, query='test')}"`
- 경로 변수도 포함 가능 ex) /basic/users/1
- 쿼리 파라미터 생성 가능 ex) /basic/users/1?query=test
<br><bR>

### 2) 리터럴 대체 - `|...|`
- 타임리프에서 문자와 표현식 등은 분리되어 있기 때문에 더해서 사용해야 함
  - `<span th:text="'Hello, ' + ${user.name} + '!'"></span>`
- 리터럴 대체 문법을 사용하면 더하지 않고 표현 가능
  - `<span th:text="|Hello, ${user.name}!|">`
- 주소를 표현할 때도 사용 가능
  - `th:onclick="|location.href='@{/user/save}'|"`

🔎 e.g) `th:href="@{|/basic/users/${user.id}|}"`
<br><br>

### 3) 반복 출력 - `th:each`
🔎 e.g) `<tr th:each="user : ${users}">`
- Model 에 포함된 'users'라는 이름을 가진 컬렉션 데이터가 'user' 변수에 하나씩 포함됨
- 컬렉션의 수 만큼 `<tr>..</tr>` 이 하위 태그를 포함해 생성됨
<br><br>

### 4) 변수 표현식 - `${...}`
🔎 e.g) `<td th:text="${user.name}">hello</td>`
- 모델에 포함된 값이나, 타임리프 변수로 선언한 값을 조회
- 프로퍼티 접근법 사용 ex) user.getName()
  

