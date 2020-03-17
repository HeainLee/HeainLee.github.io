---
title: "윈도우10에서 MySQL 데이터베이스 설치 / SQL 기본문법"
date: 2020-03-17
permalink: /dev/post02
tags : sql
categories: sql
---

이 포스팅은 내 윈도우10 환경에 MySQL를 어떻게 설치했는지에 대한 내용을 정리한 것이다. 참고로 MySQL은 DBMS 랭킹2위로 많이 사용되는 DB 중 하나이다. 최근에는 빅데이터 분석을 위해 MongoDB와 같은 NoSQL이 많이 사용된다. 향후 MongoDB 설치와 사용법을 알아볼 예정이다.(MongoDB는 맥에 설치/사용 예정)

- DBMS 랭킹 보기 : https://db-engines.com/en/ranking


## 01. MySQL 설치 💻

[오라클 홈페이지]에 들어가서 윈도우 OS에 맞는 MySQL를 다운로드 받는다.

다운로드 받은 파일을 클릭해서 설치를 진행하면 된다. (Next를 열 번은 넘게 누르게 될 것이다..)

<figure >
    <img src="/assets/images/mysql_installing_zip.png">
</figure>

생각보다 오래 걸린다.. 근데 설치는 이게 끝이다 ㅋㅋ 


## 02. SQL 기초 문법의 이해 (데이터베이스/테이블 만들기) 🌱

### 🌱 데이터베이스 조회/생성/삭제

데이터베이스 목록 보기

```sql
SHOW DATABASES;
```

데이터베이스 생성 

```sql
CREATE DATABASE mydata;
```

데이터베이스 삭제

```sql
DROP DATABASE mydata;
```

dbname 데이터베이스 사용 시

```sql
USE dbname;
```

### 🌱 테이블 생성/조회/삭제/수정

테이블 목록 조회

```sql
SHOW TABLES;
```

테이블 생성

```sql
CREATE TABLE myproduct (
    MYKEY INT,
    PRODUCT_ID TEXT,
    TITLE TEXT,
    ORI_PRICE INT,
    DISCOUNT_PRICE INT,
    DISCOUNT_PERCENT INT,
    DELIVERY TEXT,
    PRIMARY KEY(MYKEY)
);
```

테이블 삭제

```sql
DROP TABLE myproduct;
```

테이블 상세 조회

```sql
DESC myproduct;
```

테이블 구조 수정 01 - 테이블에 새로운 컬럼 추가

```sql
ALTER TABLE [테이블명] ADD COLUMN [추가할 컬럼명][추가할 컬럼 데이터형]
ALTER TABLE customer_db ADD COLUMN model_type varchar(10) NOT NULL;
```

테이블 구조 수정 02 - 테이블 컬럼 타입 변경

```sql
ALTER TABLE [테이블명] MODIFY COLUMN [변경할 컬럼명][변경할 컬럼 타입]
ALTER TABLE customer_db MODIFY COLUMN name varchar(20) NOT NULL;
```

테이블 구조 수정 03 - 테이블 컬럼 이름 변경

```sql
ALTER TABLE [테이블명] CHANGE COLUMN [기존 컬럼명][변경할 컬럼명][변경할 컬럼 타입]
ALTER TABLE customer_db CHANGE COLUMN modelnumber model_num varchar(10) NOT NULL;
```

테이블 구조 수정 04 - 테이블 컬럼 삭제

```sql
ALTER TABLE [테이블명] DROP COLUMN [삭제할 컬럼명]
ALTER TABLE customer_db CHANGE DROP modelnumber age;
```


## 03. SQL 기초 문법의 이해 (데이터 다루기) 🌿

데이터 관리란 데이터를 생성하고, 읽고, 수정하고 삭젠하
CRUD - 데이터 생성, 읽기(검색), 수정(갱신), 삭제

### 🌿 데이터 생성: 테이블에 컬럼에 맞추어 테이터를 넣는 작업

테이블 전체 컬럼에 대응하는 값을 모두 넣기

```sql
INSERT INTO mytable VALUES(1, 'i7', '7700', 'Kaby Lake');
INSERT INTO mytable (name, model_name, model_type) VALUES('i7', '7700', 'Kaby Lake');
```

### 🌿 데이터 읽기: 테이블에 저장된 데이터를 읽는 작업

테이블 전체 컬럼의 데이터를 모두 읽기
```sql
SELECT * FROM [테이블명];
```

테이블 특정 컬럼의 데이터만 읽기

```sql
SELECT [컬럼명] FROM [테이블명];
```

테이블 특정 컬럼의 데이터를 검색하되, 표시할 컬럼명도 다르게 하기

```sql
SELECT [컬럼명] AS [바꿀컬럼이름] FROM [테이블명];
```

데이터 정렬해서 읽기 (DESC: 내림차순, ASC: 오름차순)

```sql
SELECT [컬럼명1], [컬럼명2] FROM [테이블명] ORDER BY [정렬기준컬럼명] ASC; 
```

조건에 맞게 데이터만 검색하기(비교) - >, <, =

```sql
SELECT [컬럼명1], [컬럼명2] FROM [테이블명] WHERE [컬럼명1] > [조건값]; 
```

조건에 맞게 데이터만 검색하기(논리연산자) - OR, AND

```sql
SELECT [컬럼명1], [컬럼명2] FROM [테이블명] WHERE ([컬럼명1] > [조건값]) OR ([컬럼명1] > [조건값]); 
```

조건에 맞는 데이터만 검색하기(LIKE를 활용한 부분 일치)

```sql
SELECT * FROM [테이블명] WHERE [필드명] LIKE '홍%'; # 홍으로 시작되는 값 모두 찾기

SELECT * FROM [테이블명] WHERE [필드명] LIKE '%홍%'; # 홍이 들어간 값 모두 찾기

SELECT * FROM [테이블명] WHERE [필드명] LIKE '홍__'; # 홍으로 시작되고 뒤에 2글자가 붙은 값 찾기
```

결과 중 일부 데이터만 가져오기 (LIMIT 활용)

```sql
SELECT * FROM [테이블명] LIMIT 10; # 결과 중 처음부터 10개만 가져오기

SELECT * FROM [테이블명] LIMIT 100, 10; # 결과 중 100번째부터 10개만 가져오기
```

조건 조합 : 위에 나열한 조건을 조합해서 다양한 Query를 작성할 수 있음

```sql
SELECT [컬럼명1], [컬럼명2] FROM [테이블명] WHERE [컬럼명1] < [조건값] AND [컬럼명2] LIKE [조건값] ORDER BY [컬럼명2] DESC LIMIT 2;
```

### 🌿 데이터 수정: 테이블에 저장된 데이터를 수정하는 작업

보통 WHERE 조건문과 함께 쓰여서, 특정한 조건에 맞는 데이터만 수정하는 경우가 많음

```sql
UPDATE [테이블명] SET [수정할 컬럼명] == [수정값] WHERE [특정 컬럼명] = [값];
```

다수의 컬럼 값을 수정할 수 있음

```sql
UPDATE [테이블명] SET [수정할 컬럼명] == [수정값], [수정할 컬럼명2] == [수정값] WHERE [특정 컬럼명] = [값];
```

### 🌿 데이터 삭제: 테이블에 저장된 데이터를 삭제하는 작업

보통 WHERE 조건문과 함께 쓰여서, 특정한 조건에 맞는 데이터만 삭제하는 경우가 많음

```sql
DELETE FROM [테이블명] WHERE [특정 컬럼명] = [값];
```

테이블에 저장된 모든 데이터를 삭제할 수 있음

```sql
DELETE FROM [테이블명];
```


[오라클 홈페이지]: https://dev.mysql.com/downloads/


