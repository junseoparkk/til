# SELECT

### 1. 테이블 모든 내용 조회
``````sql
SELECT * FROM [테이블명];
``````

#### ex)
``````sql
SELECT * FROM Customers;
``````
<br>

### 2. 테이블 특정 열(column) 조회
``````sql
SELECT [컬럼1], [컬럼명2] FROM [테이블명];
``````

#### ex)
``````sql
SELECT CustomerId, CustomerName FROM Customers;
``````
<br>


``````sql
SELECT CustomerId, 1, 'Example', NULL FROM Customers;
``````
<br>

### 3. 테이블 특정 행(row) 조회
``````sql
SELECT * FROM [테이블명]
WHERE [조건];
``````

#### ex)
``````sql
SELECT * FROM Customers
WHERE CustomerId = 3;
``````
<br>

### 4. 테이블 특정 기준 정렬
``````sql
SELECT * FROM [테이블명]
ORDER BY [기준1] [정렬방식1], [기준2] [정렬방식2]
``````

- ASC (default) : 오름차순 정렬
- DESC : 내림차순 정렬

#### ex)
``````sql
#고객 아이디 기준 오름차순 정렬, 같을 경우 이름 기준 내림차순 정렬

SELECT * FROM Customers
ORDER BY CustomerID, CustomerName DESC;
``````
<br>

### 5. 조회 데이터 갯수 제한
``````sql
SELECT * FROM [테이블명]
LIMIT [데이터 갯수];
``````

``````sql
SELECT * FROM [테이블명]
LIMIT [건너뛸 갯수], [데이터 갯수];
``````

#### ex)
``````sql
SELECT * FROM Customers
LIMIT 10;
``````
<br>

#### ex)
``````sql
# 6번 데이터 이후 10개 데이터 조회

SELECT * FROM Customers
LIMIT 5, 10;
``````
<br>

### 6. 테이블 컬럼명 별칭
``````sql
SELECT 
    [컬럼명1] AS [별칭1],
    [컬럼명2] AS [별칭2]
FROM [테이블명];
``````

#### ex)
``````sql
SELECT 
    CustomerId AS 'Id',
    CustomerName AS 'Name'
FROM Customers;
``````
<br>