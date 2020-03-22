---
title: "파이썬으로 MySQL 다루기 / pymysql 라이브러리 사용하기"
date: 2020-03-22
permalink: /dev/post03
tags : sql python
categories: sql
---



본 포스팅은 파이썬에서 mysql을 사용할 수 있는 라이브러리인 pymysql을 통해 mysql 핸들링하는 방법에 대한 내용이다. (pymysql 외에도 MySQLdb, MySQL connector 등 다양한 라이브러리 존재)



## 01. pymysql 설치 

```bash
pip install PyMySQL
```

* 일반적인 mysql 핸들링 코드 작성 순서
1. PyMySQL 모듈 import
2. pymysql.connect() 메소드를 통해 MySQL 연결 (호스트명, 포트, 로그인, 암호, 접속할 DB 등 파라미터 지정)
3. Connection 객체로부터 cursor() 메서드를 호출하여 Cursor 객체를 가져옴
4. Cursor 객체의 execute() 메서드를 사용하여 SQL 문장을 DB 서버에 전송
5. SQL 쿼리의 경우 Cursor 객체의 fetchall(), fetchone(), fetchmany() 등의 메서드를 사용하여 서버로부터 가져온 데이터를 코드에서 활용
6. 삽입, 갱신, 삭제 등의 DML(Data Manipulation Language) 문장을 실행하는 경우, INSERT/UPDATE/DELETE 후 Connection 객체의 commit() 메서드를 사용하여 데이터 확정
7. Connection 객체의 close() 메서드를 사용하여 DB 연결을 닫음



## 02. pymysql 기본 사용법 - 테이블 생성

### 1. PyMySQL 라이브러리 가져오기

```python
import pymysql
```

### 2. 접속하기 connect() 



[주요 파라미터]
- host : 접속할 mysql server 주소
- port : 접속할 mysql server 의 포트 번호
- user : mysql ID 
- passwd : mysql ID의 암호
- db : 접속할 데이터베이스
- charset='utf8' : mysql에서 select하여 데이터를 가져올 때 한글이 깨질 수 있으므로 연결 설정에 넣어줌

```python
db = pymysql.connect(host='localhost', port=3306, user='root', passwd='password', db='db_name', charset='utf8')
```

### 3. 커서 가져오기 cursor()

```python
cursor = db.cursor() # Cursor Object 가져오기
```

### 4. 구문 실행 execute()

```python
sql = """
    CREATE TABLE product (
        PRODUCT_CODE VARCHAR(20) NOT NULL,
        TITLE VARCHAR(200) NOT NULL,
        ORI_PRICE INT,
        DISCOUNT_PRICE INT,
        DISCOUNT_PERCENT INT,
        DELIVERY VARCHAR(2),
        PRIMARY_KEY(PRODUCT_CODE)
    );
    """

cursor.execute(sql) # SQL 실행 (Cursor 객체의 execute() 메서드 사용하여 INSERT, UPDATE, DELETE 문장을 DB서버에 보냄)
```

### 5. DB 커밋 commit()

```python
db.commit() # 실행 mysql 서버에 확정 반영하기
```

### 6. DB 연결 닫기 close()

```python
db.close() # DB 연결 닫음
```



## 03. pymysql 기본 사용법 - 데이터 입력과 검색

### 1. 데이터 삽입(INSERT)
- Cursor Object 가져오기 
- SQL 실행하기
- 실행 mysql 서버에 확정 반영하기

```python
import pymysql

db = pymysql.connect(host='127.0.0.1', port=3306, user='root', passwd='password', db='db_name', charset='utf8')

cursor = db.cursor()

sql = """
    INSERT INTO product VALUES (
    '215673140', '스위트바니셔츠', 23000, 6900, 70, 'F'
    );
"""

cursor.execute(sql)

db.commit()

db.close()
```

### 2. 데이터 조회(SELECT)
- Cursor Object 가져오기
- SQL 실행하기
- mysql 서버로부터 데이터 가져오기 : fetch 메서드 사용
    - fetchall() : Fetch all rows
    - fetchone() : Fetch the next row
    - fetchmany(size=None) : Fetch several rows

```python
import pymysql

db = pymysql.connect(host='127.0.0.1', port=3306, user='root', passwd='password', db='db_name', charset='utf8')

cursor = db.cursor()

sql = "SELECT * FROM product"
cursor.execute(sql)

result = cursor.fetchall() # result는 타입: tuple

for record in result:
    print(record[0])

db.close()
```



## 04. pymysql 기본 사용법 - 데이터 수정과 삭제

### 1. 데이터 수정(UPDATE)
- Cursor Object 가져오기 
- SQL 실행하기 (UPDATE ...)
- 실행 mysql 서버에 확정 반영하기

```python
import pymysql

db = pymysql.connect(host='127.0.0.1', port=3306, user='root', passwd='password', db='db_name', charset='utf8')

try:
    with db.cursor() as cursor:
        sql = """
        UPDATE product 
        SET TITLE='린넨원피스', 
            ORI_PRICE=33000, 
            DISCOUNT_PRICE=9900, 
            DISCOUNT_PERCENT=70
        WHERE PRODUCT_CODE='215673140'
        """
        cursor.execute(sql)
        db.commit()
        print(cursor.rowcount)
finally:
    db.close()
```

### 2. 데이터 삭제(DELETE)
- Cursor Object 가져오기 
- SQL 실행하기 (DELETE ...)
- 실행 mysql 서버에 확정 반영하기

```python
import pymysql

db = pymysql.connect(host='127.0.0.1', port=3306, user='root', passwd='password', db='db_name', charset='utf8')

try:
    with db.cursor() as cursor:
        sql = "DELETE FROM product WHERE PRODUCT_CODE='215673140'"
        cursor.execute(sql)
        db.commit()
        print(cursor.rowcount)
finally:
    db.close()
```



## 05. 파일로 실행하는 SQL 명령

데이터베이스/테이블 생성 또는 데이터 입력을 위한 sql 구문을 따로 .sql 파일로 저장해서 한꺼번에 명령을 실행시킬 수 있다.

1. MySQL 워크벤치
- File -> Open SQL Script -> filename.sql

2. MySQL 터미널 명령

```sql
mysql> SOURCE filename.sql;
```

- SOURCE filename.sql 또는 SOURCE ../../filename.sql 
- filename.sql 파일 위치는 터미널 명령을 실행하는 동일 디렉토리에 있거나, 해당 디렉토리까지 명시해줘야 함


### 1. 파일로 한번에 데이터 입력하기 

1. MySQL 워크벤치
- Go to Schemas -> Find database -> Go to Tables -> Go to table -> Table Data Import Wizard -> csv 파일 선택 -> Source Column/ Dest Column 설정 -> Import

2. MySQL CLI로 CSV 파일 로드 

```sql
mysql> LOAD DATA INFILE 'CSV 데이터 파일 경로' INTO TABLE db_name.table_name (field_name1, field_name2, field_name3, field_name4);
```


### 2. pandas와 pymysql : 파일로 데이터 저장하기

- pandas 의 read_sql() 메서드로 데이터 읽기
- pandas 의 to_csv() 메서드로 데이터 저장
```python
import pymysql
import pandas as pd 

host_name = 'localhost'
host_port = 3306
username = 'root'
password = 'password'
database_name = 'db_name'

db = pymysql.connect(host=host_name, 
                     port=host_port, 
                     user=username, 
                     passwd=password, 
                     db=database_name, 
                     charset='utf8')

# pd.read_sql(쿼리, 연결된 db connection 객체)

SQL = "SHOW TABLES"
df = pd.read_sql(SQL, db)

SQL = "SELECT * FROM students"
df = pd.read_sql(SQL, db)

# pd.to_csv('파일명', sep='분할자', header='헤더여부', encoding='인코딩유형')

df.to_csv('save_table.csv', sep=',', header=False, encoding='utf8')
```

## 번외 (외래키 사용) 

외래키(FOREIGN KEY) 사용 이유: 두 테이블 사이에 관계를 선언해서, 데이터의 무결성을 보장하기 위해

```sql
CREATE DATABASE ecommerce;
USE ecommerce;
SHOW DATABASES;

CREATE TABLE product(
    PRODUCT_CODE VARCHAR(20) NOT NULL,
    TITLE VARCHAR(200) NOT NULL,
    ORI_PRICE INT,
    DISCOUNT_PRICE INT,
    DISCOUNT_PERCENT INT,
    DELIVERY VARCHAR(2),
    PRIMARY KEY(PRODUCT_CODE)
);
DESC product;

CREATE TABLE ranking(
    ID INT UNSIGNED NOT NULL AUTO_INCREMENT,
    CATEGORY VARCHAR(50),
    SUBCATEGORY VARCHAR(50),
    RANKING INT NOT NULL,
    PRODUCT_CODE VARCHAR(20) NOT NULL,
    PRIMARY KEY(ID)
    FOREIGN KEY(PRODUCT_CODE) REFERENCES product(PRODUCT_CODE)
);
```
