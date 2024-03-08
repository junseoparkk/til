# 디자인 패턴 기초

❗️ 용어 설명
- 라이브러리 (Library)
  - 공통으로 사용될 수 있는 특정 기능들을 모듈화한 것
  - 폴더명, 파일명 등에 대한 규칙이 없음
  - 프레임워크에 비해 자유로움
  - 제어권이 사용자에게 있음
- 프레임워크 (Framework)
  - 공통으로 사용될 수 있는 특정 기능들을 모듈화한 것
  - 폴더명, 파일명 등에 대한 규칙이 존재
  - 라이브러리에 비해 엄격
  - 제어권이 프로그램에게 있음
<br><br>

## 1️⃣ 디자인 패턴 (Design Pattern)
프로그램을 설계할 때 자주 발생한 문제를 객체 간의 상호 관계 등을 이용해 해결할 수 있도록 하나의 규약 형태로 만들어 놓은 것
<br><br>

### 1) 싱글톤 패턴 (Singleton Pattern)
- 하나의 클래스에 오직 하나의 인스턴스만 가지는 패턴
- 하나의 인스턴스만 만들어 놓고 해당 인스턴스를 다른 모듈들이 공유하며 사용
- ex) 데이터베이스 연결 모듈, 스프링 컨테이너, 서블릿 등
<br><br>
- 장점
  - 인스턴스 생성 비용이 적음
- 단점
  - 객체간 의존성이 높아짐
  - TDD 등에서 단위 테스트가 어려움
  - 공유 변수 사용시 조심해야 함
<br><br>

```java
public class Singleton {
    private static final Singleton instance = new Singleton();

    private Singleton() {
    }

    public static synchronized Singleton getInstance() {
        return instance;
    }
}
```
<br>

### 🔎 의존성 주입 (DI, Dependency Injection)
싱글톤 패턴의 단점인 모듈 간의 강한 결합을 어느 정도 해소할 수 있음
- 장점
  - 모듈들을 쉽게 교체할 수 있어 테스트 및 마이그레이션이 쉬움
  - 애플리케이션 의존성 방향이 일관됨
  - 모듈 간의 관계들이 명확해지며 결합이 느슨해짐
- 단점
  - 모듈들이 더욱 분리되므로 클래스가 늘어남
  - 프로그램이 복잡해질 수 있음
  - 약간의 런타임 패널티가 발생할 수 있음 (static?)
---


### 2) 팩토리 패턴 (Factory Pattern)
- 객체를 사용하는 코드에서 객체 생성 부분을 분리해 추상화한 패턴
- 상속 관계의 두 클래스
  - 상위 클래스 : 중요한 뼈대 결정
  - 하위 클래스 : 객체 생성에 대한 구체적 내용 결정
  - 두 클래스가 분리되어 결합이 느슨함
  - 상위 클래스는 인스턴스 생성 방식을 몰라도 됨 -> 높은 유연성
- 객체 생성 로직이 분리 -> 높은 유지 보수성
<br><br>

```java
// 상위 클래스 - 음식
abstract class Food {
    public abstract int getPrice();

    @Override
    public String toString() {
        return "Price of food : " + getPrice();
    }
}

// 음식 종류
public enum FoodType {
    RICE, NOODLE;
}

// 팩토리 클래스
class FoodFactory {
    public static Food getFood(FoodType type, int price) {
        if (type == FoodType.RICE) {
            return new Rice(price);
        } else if (type == FoodType.NOODLE) {
            return new Noodle(price);
        } else {
            return new DefaultFood();
        }
    }
}

// 하위 클래스 - default
class DefaultFood extends Food {
    private int price;

    public DefaultFood() {
        this.price = 0;
    }

    @Override
    public int getPrice() {
        return price;
    }
}

// 하위 클래스 - 밥
class Rice extends Food {
    private int price;

    public Rice(int price) {
        this.price = price;
    }

    @Override
    public int getPrice() {
        return price;
    }
}

// 하위 클래스 - 국수
class Noodle extends Food {
    private int price;

    public Noodle(int price) {
        this.price = price;
    }

    @Override
    public int getPrice() {
        return price;
    }
}
```
---
### 3) 전략 패턴 (Strategy Pattern)
- 정책 패턴 (Policy Pattern) 이라고도 부름
- 객체의 행위를 바꾸고 싶은 경우 직접 수정하지 않고 '전략'이라는 캡슐화한 알고리즘을 컨텍스트 안에서 바꿔주면서 상호 교체가 가능하게 만드는 패턴
- ex) 결제 방법, 인증 방법 (Node.js의 passport 등)
---
### 4) 옵저버 패턴 (Observer Pattern)
- 주체가 어떤 객체의 상태 변화를 관찰
- 상태 변화가 생길 때 메서드 등을 통해 옵저버 목록의 옵저버들에게 변화를 알려줌
    - 주체 : 객체의 상태 변화를 보고 있는 관찰자
    - 옵저버 : 추가 변화 사항이 생기는 객체
- 이벤트 기반 시스템에서 주로 사용
- MVC 패턴에서도 사용
- ex) SNS 서비스
<br><br>

❗️ 용어 설명
- 상속 (extends)
  - 자식 클래스가 부모 클래스의 메서드 등을 상속 받아 사용
  - 자식 클래스에서 상속 받은 메서드를 추가 및 확장 가능
  - 재사용성, 중복성의 최소화
  - 일반 클래스 or abstract 클래스를 기반으로 구현
- 구현 (implements)
  - 부모 인터페이스를 자식 클래스에서 재정의하여 구현
  - 상속과는 달리 부모 클래스의 메서드를 재정의해서 구현해야 함
  - 인터페이스를 기반으로 구현
<br><br>

### 🔎 프록시 객체 (proxy)
- 어떠한 대상의 기본적인 동작 (속성 접근, 할당, 순회, 열거, 함수 호출 등) 의 작업을 가로챌 수 있는 객체
- 자바스크립트에서 프록시 객체는 두 개의 매개변수를 가짐
  - target : 프록시할 대상
  - handler : 프록시 객체의 target 동작을 가로채 정의할 동작들이 정해져 있는 함수
- 자바스크립트에서 옵저버 패턴은 프록시 객체를 통해 구현 가능
---
### 5) 프록시 패턴 & 프록시 서버
- 프록시 패턴 (Proxy Pattern)
  - 대상 객체에 접근하기 전에 접근에 대한 흐름을 가로채 객체 앞단의 인터페이스 역할을 하는 디자인 패턴
  - 객체의 속성, 변환 등을 보완하며 보안, 데이터 검증, 캐싱, 로깅 등에 사용됨
  - 프록시 객체로도 쓰이지만 프록시 서버로도 활용 가능
- 프록시 서버 (Proxy Server)
  - 서버와 클라이언트 사이에서 클라이언트가 자신을 통해 다른 네트워크 서비스에 간접적으로 접속할 수 있게 해주는 컴퓨터 시스템이나 응용 프로그램
- 프록시 서버의 캐싱 (Caching)
  - 캐시 안에 정보를 담아두고, 해당 정보가 필요한 요청에 대해 원격 서버가 아닌 캐시 내부의 데이터를 활용
  - 이를 통해 불필요한 외부와 연결하지 않아도 되어 트래픽을 줄일 수 있음
<br><br>

### 🔎 Nginx
- 비동기 이벤트 기반의 구조와 다수의 연결을 효과적으로 처리할 수 있는 웹 서버
- 주로 Node.js 서버 앞단의 프록시 서버로 활용
  - Node.js의 버퍼 오버플로우 취약점을 예방할 수 있음
  - nginx를 프록시 서버로 앞단에, Node.js를 뒤쪽에 놓는 구조를 많이 사용
  - 익명 사용자의 직접적인 서버 접근을 차단, 보안성을 강화
  - 프록시 서버를 통해 실제 포트를 숨길 수 있고, 정적 자원을 압축하거나 로깅도 가능

### 🔎 CloudFlare
- 전 세계적으로 분산된 서버가 있고, 이를 통해 어떠한 시스템의 콘텐츠 전달을 빠르게 할 수 있는 CDN 서비스
- DDOS 공격 방어, HTTPS 구축 가능 -> 프록시 서버를 사용하기 때문
  - CloudFlare는 의심스러운 트래픽, 사용자가 아닌 시스템에서 오는 트래픽을 자동으로 차단 -> DDOS 방어
  - 인증서를 기반으로도 HTTPS 구축 가능하지만, CloudFlare는 별도의 인증서 설치 없이 쉽게 HTTPS 구축 가능
<br><br>

❗️ 용어 설명
CDN (Cross-Origin Resource Sharing)
- 각 사용자가 인터넷에 접속하는 곳과 가까운 곳에서 콘텐츠를 캐싱 or 배포하는 서버 네트워크
- CDN을 통해 사용자가 웹 서버로부터 콘텐츠를 다운하는 시간을 줄일 수 있음
<br><br>

### 🔎 CORS & Proxy Server
- CORS (Cross-Origin Resource Sharing)
  - 서버가 웹 프라우저에 리스를 로드할 때 다른 오리진을 통해 로드하지 못하게 하는 HTTP 헤더 기반 메커니즘
  - 오리진 (Origin) : 프로토콜, 호스트 이름, 포트 조합을 말함
  - e.g) http://localhost:8080
<br><br>
---

### 6) MVC 패턴
- 모델(Model) + 뷰(View) + 컨트롤러(Controller) 로 이루어짐

#### 🔎 모델 (Model)
- 애플리케이션의 데이터인 데이터베이스, 상수, 변수 등
- 컨트롤러에게 받은 데이터를 조작하는 역할
- 데이터와 관련된 기능 + 값과 기능
<br><br>
- 규칙
  - 사용자가 편집하려는 모든 데이터를 가진다.
  - View나 Controller에 의존하면 안된다.
  - 변경이 일어나면, 변경 통지에 대한 처리 방법을 구현해야 한다.

#### 🔎 뷰 (View)
- 입력값이나 체크박스 등 사용자 인터페이스 요소
- 컨트롤러에게 받은 모델 데이터를 사용자에게 시각적으로 보여주기 위한 역할
- 사용자에게 직접 보여지는 화면
<br><br>
- 규칙
  - Model이 가지는 정보를 따로 저장하면 안된다.
  - Model이나 Controller에 의존하면 안된다.
  - 변경이 일어나는 부분에 대해서만 값을 받아와야 한다.

#### 🔎 컨트롤러 (Controller)
- 모델과 뷰 사이에서 데이터 흐름을 제어
- 사용자가 접근한 URL을 매핑하여 비즈니스 로직을 처리한다.
- 결과는 모델이 담아 뷰에 전달한다.
- 모델과 뷰 사이에서 역할을 분리
<br><br>
- 규칙
  - 모델이나 뷰에 대해서 알고 있어야 한다.
  - 모델이나 뷰의 변경을 감지해야 한다.
<br><br>

❗️MVC 규칙
1. 모델은 컨트롤러, 뷰에 의존하면 안된다.
   - 모델 내부에 컨트롤러 및 뷰 관련 코드가 없어야 함
2. 뷰는 모델에만 의존해야 하고, 컨트롤러에 의존하면 안된다.
   - 뷰 내부에 모델 코드만 있을 수 있고, 컨트롤러 관련 코드가 없어야 함
3. 뷰가 모델에게 데이터를 받을 떈 사용자마다 다르게 보여줘야 하는 데이터만 받아야 한다.
4. 컨트롤러는 모델과 뷰에 의존해도 된다.
   - 컨트롤러 내부에 모델과 뷰 코드가 있을 수 있음
5. 뷰가 모델의 데이터를 받을 때엔 반드시 컨트롤러를 거쳐야 한다.
<br><br>

🧐 MVC 패턴 예시 : React.js
- 리액트는 유저 인터페이스를 구축하기 위한 라이브러리
- 가상 'DOM'을 통해 실제 DOM을 조작하는 것을 추상화
- 특성
  - 불변성(immutable)
  - 단방향 바인딩 적용
  - 자유도가 높음
---

### 7) MVP 패턴, MVVM 패턴

#### 🔎 MVP 패턴
- MVC 패턴으로부터 파생
- MVC 의 컨트롤러가 프레젠터 (presenter)로 교체된 패턴
- 뷰와 프레젠터는 일대일 관계이기 때문에 MVC 패턴보다 더 강한 결합을 지님
<br><br>

#### 🔎 MVVM 패턴
- MVC 패턴으로부터 파생
- MVC 의 컨트롤러가 뷰 모델 (View Model)로 교체된 패턴
<br><br>

🧐 MVVM 패턴 예시 : Vue.js
- 반응형 (reactivity) 이 특징인 프론트엔드 프레임워크
- 특징
  - 함수를 사용하지 않고 값 대입만으로도 변수가 변경
  - 양방향 바인딩
  - HTML을 토대로 컴포넌트 구축 가능
  - 재사용 가능한 컴포넌트 기반으로 UI 구축 가능
