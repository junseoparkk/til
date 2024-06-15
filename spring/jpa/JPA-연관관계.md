- **목차**

# 1️⃣ JPA 연관 관계

## 1) JPA 연관 관계 종류

### 방향 (Direction)

1. **단뱡항 관계 (Unidirectional)**
    - 한쪽 엔티티가 다른 엔티티를 참조, 반대로는 참조되지 않는 관계
    - A → B 참조, B → A 참조 X
- ex) 단방향 N:1 관계

```java
@Entity
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne
    @JoinColumn(name = "customer_id")
    private Customer customer;

    ...
}
```

```java
@Entity
public class Customer {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

    ...
}
```

1. **양방향 관계 (Bidirectional)**
    - 두 엔티티가 서로 참조하는 관계
    - A → B 참조, B → A 참조. 즉, A ↔ B
- ex) 양방향 1:N 관계

```java
@Entity
public class Customer {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

    @OneToMany(mappedBy = "customer")
    private List<Order> orders;

    ...
}
```

```java
@Entity
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne
    @JoinColumn(name = "customer_id")
    private Customer customer;

    ...
}
```

### 다중성 (Multiplicity)

1. **다대일 (N:1)**
    - @ManyToOne + @JoinColumn & @OneToMany
    - 여러 엔티티가 하나의 엔티티를 참조하는 관계
    - 위 단방향 관계 예시 코드 참고
    - ex) Board : User = N : 1
2. **일대다 (1:N)**
    - @OneToMany & @ManyToOne + @JoinColumn
    - 하나의 엔티티가 여러 엔티티를 참조하는 관계
    - 위 양방향 관계 예시 코드 참고
    - ex) Board : Comment = 1 : N
3. **일대일 (1:1)**
    - @OneToOne + @JoinColumn
    - 하나의 엔티티가 다른 하나의 엔티티를 참조하는 관계
    - ex) User : Profile = 1 : 1

```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @OneToOne
    @JoinColumn(name = "profile_id")
    private Profile profile;
    
    ...
}
```

```java
@Entity
public class Profile {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @OneToOne(mappedBy = "profile")
    private User user;
    
    ...
}
```

1. **다대다 (N:M)**
    - @ManyToMany
    - 여러 엔티티가 여러 엔티티를 참조하는 관계
    - ex) Student : Course = N : M

```java
@Entity
public class Student {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

    @ManyToMany
    @JoinTable(
        name = "student_course",
        joinColumns = @JoinColumn(name = "student_id"),
        inverseJoinColumns = @JoinColumn(name = "course_id"))
    private List<Course> courses;
    
    ...
}
```

```java
@Entity
public class Course {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String title;

    @ManyToMany(mappedBy = "courses")
    private List<Student> students;
    
    ...
}
```

---

## 2) Object ↔ Table 패러다임 불일치 (ORM 불일치)

### **객체**

- 참조용 필드가 존재하는 쪽에서만 참조 가능
- 한쪽만 참조하면 단방향
- 양쪽이 서로 참조하면 양방향

### 테이블

- FK 하나로 양방향 참조 가능
- 실제로는 방향의 개념이 없다

<aside>
💡 그래서 어떤 문제가 발생하는데?

</aside>

1. 상속 구조 매핑 문제
2. 복합 키 매핑 문제
3. 연관 관계 불일치
4. 지연 로딩과 즉시 로딩 문제
5. 객체 그래프 탐색 문제
6. 동시성 문제
7. 데이터 정규화, 비정규화 문제

### 연관 관계 불일치

- 객체는 연관 관계를 필드나 참조를 통해 직접적으로 표현 가능
- 아래는 객체를 테이블에 맞게 모델링한 예시

```java
// 학생 정보 저장
Student student = new Student();
student.setName("student1");
em.persist(student);

// 코스 정보 저장
Course course = new Course();
course.setName("course A");
course.addStudent(student);
em.persist(student);

// 코스 조회
Course findCourse = em.find(Course.class, course.getId());

// 연관 관계 X
Student findStudent = em.find(Student.class, student.getId());
```

- 데이터베이스에서는 FK를 사용하여 연관 관계를 표현

→ 다양한 연관 관계 매핑 어노테이션 지원

### 지연 로딩과 즉시 로딩 문제

- 지연 로딩 (Lazy Loadiing) vs 즉시 로딩 (Eager Loading)
- `객체 지향 모델`에서는 필요한 시점에 데이터를 로딩하는 것이 자연스러움
    - ex) 특정 메서드 호출시 객체 로딩
- `데이터베이스 쿼리` 에서는 한 번에 필요한 모든 데이터를 불러오는 것이 효율적임

→ JPQL 등을 사용하여 엔티티 매핑시 `fetch` 속성을 지정하여 조정

### 데이터 정규화 및 비정규화 문제

- `객체 모델`은 비정규화된 데이터 구조를 선호
- `관계형 데이터베이스`는 정규화를 통해 중복을 최소화한 데이터 구조를 선호
- 따라서 N:M → 1:N + N:1 관계로 분리해야 함
- ex) N:M 관계 → DB는 이를 직접 지원하지 않기 때문에 중간 테이블을 두자!

```java
@Entity
public class Student {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

    @OneToMany(mappedBy = "student")
    private List<StudentCourse> studentCourses;

    ...
}
```

```java
@Entity
public class Course {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String title;

    @OneToMany(mappedBy = "course")
    private List<StudentCourse> studentCourses;

    ...
}
```

```java
@Entity
public class StudentCourse {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne
    @JoinColumn(name = "student_id")
    private Student student;

    @ManyToOne
    @JoinColumn(name = "course_id")
    private Course course;

    ...
}
```

```
+--------------+          +-----------------+          +-------------+
|   Student    |          |  StudentCourse  |          |    Course   |
+--------------+          +-----------------+          +-------------+
| id (PK)      |<-------+ | id (PK)         | +------->| id (PK)     |
| name         |          | student_id (FK) |          | title       |
+--------------+          | course_id (FK)  |          +-------------+
	                        +-----------------+

```

---

## 3) FK는 누가 관리할건데?

### 배경

- 데이터베이스 테이블은 FK를 통해 연관 관계를 설정
- 객체는 A → B, B → A 서로 참조
- 따라서 객체 설계시 FK를 관리할 곳을 지정해야 함

### FK 위치

- Customer : Order ⇒ 1 : N
- `N` 에 해당하는 엔티티에서 FK를 관리
- `1` 에 해당하는 엔티티는 조회만 가능하며, FK에 영향 X

### 예시 코드

```java
@Builder
@Getter
@Entity
@AllArgsConstructor
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Table(name = "users")
public class User {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "user_id")
    private Long id;
    
    @OneToMany(mappedBy = "user", cascade = CascadeType.ALL)
    private List<Board> boards = new ArrayList<>();

    @OneToMany(mappedBy = "user", cascade = CascadeType.ALL)
    private List<Comment> comments = new ArrayList<>();
    
    ...
}
```

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/38552da6-340d-42c1-a9a1-b181ff331f03/dee33d44-3dda-4045-955b-14fa8c1df419/Untitled.png)

```java
@Getter
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Table(name = "boards")
public class Board extends BaseTimeEntity {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "board_id")
    private Long id;
	  
	  @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id")
    private User user;

    @OneToMany(mappedBy = "board", cascade = CascadeType.ALL)
    private List<Comment> comments = new ArrayList<>();
    
    ...
}
```

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/38552da6-340d-42c1-a9a1-b181ff331f03/ee91c57e-6e14-4097-8fc3-3bdaddd54967/Untitled.png)

```java
@Getter
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Table(name = "comments")
public class Comment extends BaseTimeEntity {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "comment_id")
    private Long id;
    
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id")
    private User user;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "board_id")
    private Board board;
    
    ...
}
```

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/38552da6-340d-42c1-a9a1-b181ff331f03/c1513ef3-b754-413f-9732-1c2e1e7af5d4/Untitled.png)

---

# 2️⃣ MySQL 기준 FK 생성 방식 (feat. 연관 관계 매핑)

## 1) 엔티티 클래스 정의

- JPA가 `@Entity` 어노테이션이 붙은 클래스를 찾아 데이터베이스 테이블에 매핑
- 외래 키 관계를 `@OneToMany` , `@ManyToOne` , `@OneToOne` , `@ManyToMany` 등 어노테이션을 통해 정의
- `@JoinColumn` 어노테이션을 통해 FK 컬럼의 이름과 속성을 지정

## 2) 메타데이터 처리(파싱)

- 애플리케이션 시작시 엔티티 클래스의 메타데이터를 읽어 ORM 설정을 초기화
- 엔티티 클래스 + 필드 어노테이션을 분석하여 매핑 정보 수집

## 3) DDL 생성

- 엔티티 매핑 정보를 바탕으로 DDL 스크립트를 생성
- `schema-generation` 옵션을 통해 자동으로 이루어짐
- `Hibernate` 를 사용할 경우, `hibernate.hbm2ddl.auto` 속성을 통해 테이블 및 외래 키 생성 가능

```java
spring:
	jpa:
		hibernate:
			ddl-auto = update | create | ...
```

## 4) 테이블 및 FK 생성

```sql
CREATE TABLE Student (
    id BIGINT AUTO_INCREMENT NOT NULL,
    name VARCHAR(255),
    PRIMARY KEY (id)
);

CREATE TABLE Course (
    id BIGINT AUTO_INCREMENT NOT NULL,
    title VARCHAR(255),
    PRIMARY KEY (id)
);

CREATE TABLE StudentCourse (
    id BIGINT AUTO_INCREMENT NOT NULL,
    student_id BIGINT,
    course_id BIGINT,
    PRIMARY KEY (id),
    FOREIGN KEY (student_id) REFERENCES Student(id),
    FOREIGN KEY (course_id) REFERENCES Course(id)
);
```

- 위와 같은 스크립트를 생성하여 데이터베이스에 쿼리를 날림

---

# 3️⃣ 추가 정보

- @Entity 설정 정보를 바탕으로 SQL문을 어떻게 생성해주는지 궁금하여 해당 클래스를 찾아봄
- JPA의 대표적인 구현체인 `Hibernate` 기준
    - `org.hibernate.tool.schema.spi.SchemaManagementTool` : interface
    - `org.hibernate.tool.schema.internal.HibernateSchemaManagementTool` : class
    - 역할 : 엔티티 매핑 정보를 기반으로 데이터베이스 스키마 생성 및 관리

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/38552da6-340d-42c1-a9a1-b181ff331f03/a0a96d61-5365-49b6-85f9-90cbb6afb67e/Untitled.png)

## 1) 주요 메서드 (HibernateSchemaManagementTool)

### getSchemaDropper

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/38552da6-340d-42c1-a9a1-b181ff331f03/a9aa9794-8a25-48d5-9d63-3c4fa731af69/Untitled.png)

- 데이터베이스 스키마 삭제를 담당하는 `SchemaDropper` 인스턴스 반환
- `options` : 설정 매핑 정보

### getSchemaCreator

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/38552da6-340d-42c1-a9a1-b181ff331f03/9b48636f-9dd2-418d-ad2d-4b9f9e7c63e2/Untitled.png)

- 데이터베이스 스키마 생성을 담당하는 `SchemaCreator` 인스턴스 반환
- `options` : 설정 매핑 정보

## 2) 스키마 생성

### SchemaCreator

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/38552da6-340d-42c1-a9a1-b181ff331f03/06cae415-cb26-4e3e-838f-6195f06eabf1/Untitled.png)

- 뭔진 잘 몰라도 메타데이터를 사용하는 것 같다! 이말이에요

### 예상되는 생성 흐름

```java
public void createSchemaExample() {
        // 표준 서비스 레지스트리 생성
        StandardServiceRegistry registry = new StandardServiceRegistryBuilder()
                .configure("hibernate.cfg.xml")
                .build();

        // 메타데이터 소스 생성
        MetadataSources metadataSources = new MetadataSources(registry);

        // 메타데이터 생성
        Metadata metadata = metadataSources.buildMetadata();

        // 스키마 관리 도구 생성
        SchemaManagementTool tool = new HibernateSchemaManagementTool();

        // 스키마 생성자 가져오기
        SchemaCreator schemaCreator = tool.getSchemaCreator(new HashMap<>());

        // 스키마 생성
        schemaCreator.doCreation(metadata, true, EnumSet.of(TargetType.DATABASE));
    }
```

그냥 뭔가가 엄청 많습니다이🥶🥶🥶

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/38552da6-340d-42c1-a9a1-b181ff331f03/57103b98-d1e4-4384-8f22-24017db50608/Untitled.png)

---

## References

[JPA :: Spring Data JPA](https://docs.spring.io/spring-data/jpa/reference/jpa.html)