---
title: "크롤링과 데이터베이스(MySQL) 사용법"
date: 2020-03-27
permalink: /dev/post05
tags : sql python
categories: sql
---


본 포스팅은 실제 예제(크롤링)를 통해 알아보는 MySQL 사용법을 정리한 내용이다. 쇼핑몰 사이트에서 상품 정보와 상품의 랭킹을 크롤링해서 데이터베이스에 저장하는 방법을 예시로 사용한다.


## 01. Schema 정의


```sql
CREATE TABLE ranking (
    num INT AUTO_INCREMENT NOT NULL PRIMARY KEY,
    main_category VARCHAR(50) NOT NULL,
    sub_category VARCHAR(50) NOT NULL,
    item_ranking TINYINT UNSIGNED NOT NULL,
    item_code VARCHAR(20) NOT NULL,
    FOREIGN KEY (item_code) REFERENCES items(item_code)
);

CREATE TABLE items (
    item_code VARCHAR(20) NOT NULL PRIMARY KEY,
    title VARCHAR(200) NOT NULL,
    ori_price INT NOT NULL,
    dis_price INT NOT NULL,
    discount_percent INT NOT NULL,
    provider VARCHAR(100)

);
```


* 한글 처리에 문제가 있을 경우, DB, TABLE에 DEFAULT CHARSET=utf8 COLLATE=utf8_bin 옵션을 넣음


```sql
CREATE DATABASE bestproducts DEFAULT CHARSET=utf8 COLLATE=utf8_bin;
CREATE TABLE tablename(field definitions) DEFAULT CHARSET=utf8 COLLATE=utf8_bin;
```

PyMySQL 라이브러리를 사용해서 다음과 같이 테이블을 생성한다.


```python
import pymysql
db = pymysql.connect(host='localhost', port=3306, user='root', passwd='password', db='bestproducts', charset='utf8')
cursor = db.cursor()

sql = '''
    CREATE TABLE items (
        item_code VARCHAR(20) NOT NULL PRIMARY KEY,
        title VARCHAR(200) NOT NULL,
        ori_price INT NOT NULL,
        dis_price INT NOT NULL,
        discount_percent INT NOT NULL,
        provider VARCHAR(100)

    );

    CREATE TABLE ranking (
        num INT AUTO_INCREMENT NOT NULL PRIMARY KEY,
        main_category VARCHAR(50) NOT NULL,
        sub_category VARCHAR(50) NOT NULL,
        item_ranking TINYINT UNSIGNED NOT NULL,
        item_code VARCHAR(20) NOT NULL,
        FOREIGN KEY (item_code) REFERENCES items(item_code)
    );
'''

cursor.execute(sql)

db.commit()
db.close()
```

## 02. BeautifulSoup을 통한 데이터 크롤링

* 파이썬으로 크롤링하기 위해서 BeautifulSoup(bs4), Selenium, Scrapy 등 라이브러리 사용 가능


### 메인 카테고리 정보 가져오기


```python
import requests
from bs4 import BeautifulSoup

res =. requests.get('http://corners.gmarket.co.kr/Bestsellers')
soup = BeautifulSoup(res.content, 'html.parser')

categories = soup.select('div.gbest-cate ul.by-group li a')
for category in categories:
    get_category('http://corners.gmarket.co.kr/'+category['href'], category.get_text())

```


### 메인/서브 카테고리 정보 가져오기


```python
def get_category(category_link, category_name):
    print(category_link, category_name)
    res = requests.get(category_link)
    soup = BeautifulSoup(res.content, 'html.parser')

    sub_categories = soup.select('div.navi.group ul li a')
    for sub_category in sub_categories:
        print(category_link, category_name, sub_category.get_text(), 'http://corners.gmarket.co.kr/'+sub_category['href'])
```


### 메인/서브 카테고리 + 상품 정보 + 상품 코드 + 판매자(제공자) 크롤링


```python
import requests
from bs4 import BeautifulSoup

res =. requests.get('http://corners.gmarket.co.kr/Bestsellers')
soup = BeautifulSoup(res.content, 'html.parser')

categories = soup.select('div.gbest-cate ul.by-group li a')
for category in categories:
    get_category('http://corners.gmarket.co.kr/' + category['href'], category.get_text())
```


* 카테고리 정보를 가져오는 함수 


```python
def get_category(category_link, category_name):
    print(category_link, category_name)
    res = requests.get(category_link)
    soup = BeautifulSoup(res.content, 'html.parser')

    get_items(soup, category_name, 'ALL')

    sub_categories = soup.select('div.navi.group ul li > a')
    for sub_category in sub_categories:
        res = requests.get('http://corners.gmarket.co.kr/' + sub_category['href'])
        soup = BeautifulSoup(res.content, 'html.parser')
        get_items(soup, category_name, sub_category.get_text())

```

* 아이템 정보를 가져오는 함수


```python
def get_items(html, category_name, sub_category_name):
    items_result_list = list()
    best_item = html.select('div.best-list') #best-list 여러 개가 있음
    for index, item in enumerate(best_item[1].select('li')): # 베스트 상품 리스트
        data_dict = dict()

        ranking = index + 1
        title = item.select_one('a.itemname')
        ori_price = item.select_one('div.o-price')
        dis_price = item.select_one('div.s-price strong span')
        discount_percent = item.select_one('div.s-price em')

        if ori_price == None or ori_price.get_text() == '':
            ori_price = dis_price

        if dis_price == None:
            ori_price, dis_price = 0, 0
        else:
            ori_price = ori_price.get_text().replace(',','').replace('원','')
            dis_price = dis_price.get_text().replace(',','').replace('원','')

        if discount_percent == None or discount_percent.get_text() == '':
            discount_percent = 0
        else:
            discount_percent = discount_percent.get_text().replace('%','')

        product_link = item.select_one('div.thumb > a')
        item_code = product_link.attrs['href'].split('=')[1]

        res = requests.get(product_link.attrs['href'])
        soup = BeautifulSoup(res.content, 'html.parser')
        provider = soup.select_one('div.item-topinfo_headline > p > a > strong')
        if provider == None:
            provider = ''
        else:
            provider = provider.get_text()

        data_dict['category_name'] = category_name
        data_dict['sub_category_name'] = sub_category_name
        data_dict['ranking'] = ranking
        data_dict['title'] = title.get_text()
        data_dict['ori_price'] = ori_price
        data_dict['dis_price'] = dis_price
        data_dict['discount_percent'] = discount_percent
        data_dict['item_code'] = item_code
        data_dict['provider'] = provider
        
        save_data(data_dict)        
        # print(category_name, sub_category_name, ranking, item_code, provider, title.get_text(), ori_price, dis_price, discount_percent)

```


## 03. 크롤링 데이터 DB 저장


### 파이썬으로 크롤링한 데이터를 데이터베이스에 넣기 위한 SQL 구문 (INSERT)


```python
item_info = {'category_name': 'ALL', 'sub_category_name': 'ALL', 'ranking': 1, 'title': '오설록 프리미엄 티', 
             'ori_price': 13000, 'dis_price': 11200, 'discount_percent': 10, 'item_code': 1230222200, 'provider': '깨끗한식품'}

# items 테이블 INSERT 구문 예시
sql = """
    INSERT INTO items VALUES (
    '""" + item_info['item_code'] + """',
    '""" + item_info['title'] + """',
    """ + item_info['ori_price'] + """,
    """ + item_info['dis_price'] + """,
    """ + item_info['discount_percent'] + """,
    '""" + item_info['provider'] + """',
    )
"""

sql.replace('\n','')

# ranking 테이블 INSERT 구문 예시
sql = """
    INSERT INTO ranking (main_category, sub_category, item_ranking, item_code) VALUES (
    '""" + item_info['category_name'] + """',
    '""" + item_info['sub_category_name'] + """',
    '""" + str(item_info['ranking']) + """',
    '""" + item_info['item_code'] + """',
    )
"""

sql.replace('\n', '')

```


### 프로그래밍을 통한 데이터베이스 데이터 INSERT

파이썬을 통해 자동으로 데이터를 저장하기 위해 다음과 같이 데이터 저장 함수(save_data)를 사용할 수 있다.


* COUNT SQL : 검색 결과의 row 수를 가져올 수 있는 SQL 문법
    - SQL 예제 : SELECT COUNT(item_code) FROM items


```python
import pymysql

db = pymysql.connect(host='localhost', port=3306, user='root', passwd='password', db='bestproducts', charset='utf8')
cursor = db.cursor()

def save_data(item_info):
    sql = """ SELECT COUNT(*) FROM items WHERE item_code = '""" + item_info['item_code'] + """';"""
    cursor.execute(sql)
    result = cursor.fetchone()
    if result[0] == 0:        
        # items 테이블 INSERT 구문
        sql = """
            INSERT INTO items VALUES (
            '""" + item_info['item_code'] + """',
            '""" + item_info['title'] + """',
            """ + str(item_info['ori_price']) + """,
            """ + str(item_info['dis_price']) + """,
            """ + str(item_info['discount_percent']) + """,
            '""" + item_info['provider'] + """')"""
        cursor.execute(sql)

    # ranking 테이블 INSERT 구문
    sql = """
        INSERT INTO ranking (main_category, sub_category, item_ranking, item_code) VALUES (
        '""" + item_info['category_name'] + """',
        '""" + item_info['sub_category_name'] + """',
        '""" + str(item_info['ranking']) + """',
        '""" + item_info['item_code'] + """')"""
    cursor.execute(sql)

```

