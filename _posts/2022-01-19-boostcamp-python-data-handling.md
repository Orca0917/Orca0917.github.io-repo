---
title:  "[Python Basics for AI] Python Data Handling"
excerpt: "파이썬에서 다룰 수 있는 CSV, 웹, XML, JSON 데이터 타입"

categories:
  - boostcamp
tags:
  - [AI, Naver, boostcamp]
toc: true
toc_sticky: true
 
date: 2022-01-19 05:00:00
last_modified_at: 2022-01-19 05:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# CSV: Comma Separate Values
CSV는 필드를 쉼표로 구분한 **텍스트 파일**이다. 엑셀 양식의 데이터를 **프로그램에 상관없이** 쓰기 위한 데이터 형식이라고 생각하면 쉽다. 예를들어, Shell만을 사용하는 linux환경에서 엑셀 문서를 열때 사용하기 용이하다. CSV파일은 탭(TSV), 빈칸(SSV)등으로 구분해서 만들기도 한다. 이를 통칭하여 Character-separated values(CSV)라고 한다. 엑셀에서는 다른이름 저장으로 .csv확장자로 저장이 가능하다.

![image](https://user-images.githubusercontent.com/91870042/144852965-871ee0f1-85ab-473f-88ac-086243310e87.png){: .align-center}

## Python으로 CSV 파일 읽기/쓰기
예제 데이터: [📄customer.csv](https://bit.ly/3psoUZb)  
CSV의 입출력은 일반적인 텍스트 파일을 처리하듯 파일을 읽어온 후, 한 줄씩 데이터를 처리한다.

```py
line_counter = 0 #파일의 총 줄수를 세는 변수
data_header = [] #data의 필드값을 저장하는 list
customer_list = [] #cutomer 개별 List를 저장하는 List

with open ("customers.csv") as customer_data: #customer.csv 파일을 customer_data 객체에 저장
    while True:
        data = customer_data.readline() #customer.csv에 한줄씩 data 변수에 저장
        
        if not data: 
            break # 데이터가 없을 때, Loop 종료
        
        if line_counter==0: # 첫번째 데이터는 데이터의 필드
            data_header = data.split(",") # 데이터의 필드는 data_header List에 저장, 데이터 저장시 “,”로 분리
        
        else:
            customer_list.append(data.split(",")) # 일반 데이터는 customer_list 객체에 저장, 데이터 저장시 “,”로 분리
        
        line_counter += 1
        

print("Header :\t", data_header) # 데이터 필드 값 출력

for i in range(0,10): #데이터 출력 (샘플 10개만)
    print ("Data",i,":\t\t",customer_list[i])

print (len(customer_list)) #전체 데이터 크기 출력
```

```py
line_counter = 0
data_header = []
employee = []
customer_USA_only_list = []
customer = None

with open ("customers.csv", "r") as customer_data:
    while 1:
        data = customer_data.readline()

        if not data:
            break
            
        if line_counter==0:
            data_header = data.split(",")
        else:
            customer = data.split(",")

            if customer[10].upper() == "USA": #customer 데이터의 offset 10번째 값
                customer_USA_only_list.append(customer) #즉 country 필드가 “USA” 것만

        line_counter+=1 #sutomer_USA_only_list에 저장

print ("Header :\t", data_header)
for i in range(0,10):
    print ("Data :\t\t",customer_USA_only_list[i])

print (len(customer_USA_only_list))

with open ("customers_USA_only.csv", "w") as customer_USA_only_csv:
    for customer in customer_USA_only_list:
        customer_USA_only_csv.write(",".join(customer).strip('\n')+"\n")
        #cutomer_USA_only_list 객체에 있는 데이터를 customers_USA_only.csv 파일에 쓰기
```

## CSV객체의 활용

텍스트 파일 형태로 데이터 처리시 문장 내에 들어가 있는 `,`에 대해서 전처리 과정이 필요하다. (`,`를 기준으로 데이터를 구분하기 때문에 발생하는 현상). 파이썬에서는 간단히 CSV파일을 처리하기 위해 csv객체를 제공한다.  
예제 데이터: [📄korea_foot_traffic_data.csv](https://data.go.kr)

![image](https://user-images.githubusercontent.com/91870042/144854321-dcb72302-fc15-4785-b5a3-efc99d34b3c5.png){: .align-center}

위의 예제 데이터는 국내 주요 상권의 유동인구 현황정보를 나타낸다. CSV파일이 한글로 되어 있어 한글에 대한 처리도 따로 필요하다.

```py
import csv
reader = csv.reader(f, delimiter = ',', quotechar = '"', qutoing = csv.QUOTE_ALL)
```

|Attribute|Default|Meaning|
|---|---|---|
|delimiter|`,`|글자를 바꾸는 기준|
|lineterminator|`\r\n`|줄바꿈기준|
|quotechar|`"`|문자열을 둘러싸는 신호 문자|
|quoting|`QUOTE_MINIMAL`|데이터를 나누는 기준이 quotechar에 의해 둘러싸인 레벨|

<br>

```py
import csv

seoung_nam_data = []
header = []
rownum = 0

# 한글 파일이기 때문에 cp949로 인코딩 옵션을 설정해준다.
with open("korea_floating_population_data.csv","r", encoding="cp949") as p_file:
    csv_data = csv.reader(p_file) #csv 객체를 이용해서 csv_data 읽기
    
    for row in csv_data: #읽어온 데이터를 한 줄씩 처리
        if rownum == 0:
            header = row #첫 번째 줄은 데이터 필드로 따로 저장
        location = row[7]

        # "행정구역" 필드 데이터 추출, 한글 처리로 유니코드 데이터를 cp949로 변환
        # u: unicode의 약자.
        if location.find(u"성남시") != -1:
            seoung_nam_data.append(row)
        # "행정구역" 데이터에 성남시가 들어가 있으면 seoung_nam_data List에 추가
        rownum +=1

with open("seoung_nam_floating_population_data.csv","w", encoding="utf8") as s_p_file:
    writer = csv.writer(s_p_file, delimiter='\t', quotechar="'", quoting=csv.QUOTE_ALL)
    # csv.writer를 사용해서 csv 파일 만들기 delimiter 필드 구분자
    # quotechar는 필드 각 데이터는 묶는 문자, quoting는 묶는 범위
    writer.writerow(header) #제목 필드 파일에 쓰기
    
    for row in seoung_nam_data:
        writer.writerow(row) #seoung_nam_data에 있는 정보 list에 쓰기
```

<br>

# Web: World Wide Web

우리가 늘 쓰는 인터넷 공간의 정식 명칭이다. 팀 버너스리에 의해 1989년 처음 제안되었으며, 원래는 물리학자들 간 정보 교환을 위해서 사용되었다. 웹은 데이터 송수신을 위한 `HTTP`프로토콜을 사용하고, 데이터를 표시하기 위해서는 `HTML`형식을 사용한다.


## Web이 동작하는 방식

![image](https://user-images.githubusercontent.com/91870042/144856024-8c385e5d-09c6-431a-8bde-5e98c6c83107.png){: .align-center}

## HTML: Hyper Text Markup Language
HTML은 웹 상의 정보를 구조적으로 표현하기 위한 언어이다. 제목, 단락, 링크 등 요소 표시를 위해 `Tag`를 사용한다. 모든 요소들은 `<`와 `>`안에 둘러 쌓여 있다. 모든 HTML은 **트리 모양의 포함관계**를 가지며 일반적으로 웹 페이지의 HTML 소스 파일은 컴퓨터가 다운 받은 후, 웹 브라우저(크롬, IE)가 해석하여 표시한다.

```html
<!doctype html>
<html>
    <head>
        <title>Hello HTML</title>
    </head>
    <body>
        <p>Hello World!</p>
    </body>
</html>
```

> WHY? 왜 웹을 알아야 하는가?

정보의 보고, 많은 데이터들이 웹을 통해 공유된다. 예를 들어, 환율정보, 날씨정보, 미국 특허정보가 예시이다. HTML도 일종의 프로그램이며, 페이지 생성 규칙이 있다. 이런 규칙을 분석하여 데이터의 추출이 가능하다. 다시 이렇게 추출된 데이터를 바탕으로 하여 다양한 분석이 가능하다.

## 정규식: Regular Expression
정규식은 정규 표현식, regexp또는 regex로 불리게 된다. 복잡한 문자열 패턴을 정의하는 문자 표현 공식이며, 이를 토애 특정한 규칙을 가진 문자열의 집합을 추출할 수 있다.
```
ex1. 전화번호: 010-1234-5678 ^\d{3}-\d{4}-\d{3}$
ex2. IPv4주소: 123.123.111.222 ^\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}$
```

HTML은 Tag로 쌓인 규칙이 있어 정규식으로 추출하기 용이하다. 정규식에 대한 문법은 너무 방대하기 때문에 스스로 찾아서 하는 공부가 필요하다. 필요한 것들은 인터넷 검색을 통해 찾을 수 있다. 기본적인 것을 공부 한 후, 넓게 적용하는 것이 필요하다.

참고: [✏정규식 연습페이지](https://regexr.com/)

### 정규식 기본 문법: 메타 문자
정규식 표현을 위한 용도로 사용되는 문자를 의미한다.

|문자|정규식에서 의미|예시|
|---|---|---|
|`.`|줄바꿈 문자인 \n을 제외한 모든 문자와 매치|a[.]b|
|`*`|앞에 있는 글자를 반복해서 나올 수 있다|tomor*ow → tomorrow, tomoow, tomorrrow|
|`+`|앞에 있는 글자를 최소 1회 이상 반복한다|tomor+ow|
|`{m.n}`|반복 횟수를 지정한다|IPv4주소 → [0-9]{1, 3}|
|`?`|반복 횟수가 1회이다|01[01]?-[0-9]{4}-[0-9]{4}|
|`|`|or|(0\|1){3}|
|`^`|not||

### 정규식 in Python

- `search()`: 한개만 찾기
- `findall()`: 전체 찾기
- 추출된 패턴은 `tuple`로 반환된다.

#### 예제1: 유저ID 찾기
```py
import re #regular expression
import urllib.request

url = "https://bit.ly/3rxQFS4"
html = urllib.request.urlopen(url)
html_contents = str(html.read())
id_results = re.findall(r"([A-Za-z0-9]+\*\*\*)", html_contents)

#findall 전체 찾기, 패턴대로 데이터 찾기
for result in id_results:
    print (result)
```

#### 예제2: 미국 특허청에 존재하는 압축파일 찾기
```py
import urllib.request # urllib 모듈 호출
import re

url = "http://www.google.com/googlebooks/uspto-patents-grants-text.html"  #url 값 입력
html = urllib.request.urlopen(url) # url 열기
html_contents = str(html.read().decode("utf8")) 

# html 파일 읽고, 문자열로 변환
url_list = re.findall(r"(http)(.+)(zip)", html_contents)
for url in url_list:
    print("".join(url)) # 출력된 Tuple 형태 데이터 str으로 join
```

#### 예제3: 삼성 주식 현황 추출
```py
import urllib.request
import re

url = "http://finance.naver.com/item/main.nhn?code=005930"
html = urllib.request.urlopen(url)
html_contents = str(html.read().decode("ms949"))

stock_results = re.findall("(\<dl class=\"blind\"\>)([\s\S]+?)(\<\/dl\>)", html_contents)
samsung_stock = stock_results[0] # 두 개 tuple 값중 첫번째 패턴
samsung_index = samsung_stock[1] # 세 개의 tuple 값중 두 번째 값

# 하나의 괄호가 tuple index가 됨
index_list= re.findall("(\<dd\>)([\s\S]+?)(\<\/dd\>)", samsung_index)
for index in index_list:
    print (index[1]) # 세 개의 tuple 값중 두 번째 값
```

<br>

# XML: eXtensible Markup Language
데이터의 구조와 의미를 설명하는 TAG를 사용하여 표시하는 언어이다. TAG와 TAG사이에 값이 표시되고, 구조적인 정보를 표현할 수 있다. XML은 HTML과 문법이 비슷하며 대표적인 데이터 저장방식이다.

XML은 컴퓨터 간에(스마트폰-PC) 정보를 주고받기 매우 유용한 저장 방식으로 쓰이고 있다.

```xml
<?xml version="1.0"?> 
<고양이> 
    <이름> 나비 </이름> 
    <품종> 샴 </품종> 
    <나이> 6 </나이> 
    <중성화> 예 <중성화> 
    <발톱_제거> 아니요 </발톱_제거>
    <등록_번호> Izz138bod </등록_번호>
    <소유자> 이강주 </소유자>
</고양이>
```

XML도 HTML과 같이 구조적인 markup언어이다. 정규표현식으로 Parsing이 가능하며, 그 Paser중 가장 많이 사용하는 것이 `Beautiful Soup`이다.

## BeautifulSoup
HTML, XML등 Markup언어 Scraping을 위한 대표적인 도구이다. 속도는 상대적으로 느리나 간편히 사용할 수 있다는 장점이 있다.

```
# Conda가상환경으로 lxml과 beaultifulsup설치
activate python_mooc
conda install lxml
conda install -c beautifulsoup4
```

```py
from bs4 import BeautifulSoup

soup = BeautifulSoup(books_xml, "lxml") # 객체 생성
soup.find_all("author")  # Tag찾는 함수 find_all생성
```

- `find_all`: 정규식과 마찬가지고 해당 패턴을 모두 반환한다.
- `find(TAG_NAME)`: TAG_NAME으로 지정된 태그를 반환한다.
- `get_text()`: 태그와 태그 사이의 반환된 패턴의 값을 반환한다.

### 예제1

[📄데이터 다운로드](https://s3.ap-northeast-2.amazonaws.com/teamlab-gachon/books.xml)

```py
from bs4 import BeautifulSoup

with open("books.xml", "r", encoding="utf8") as books_file:
    books_xml = books_file.read() # File을 String으로 읽어오기

soup = BeautifulSoup(books_xml, "lxml") # lxml Parser를 사용해서 데이터 분석

# author가 들어간 모든 element 추출
for book_info in soup.find_all("author"):
    print (book_info)
    print (book_info.get_text())
```

### 예제2
특허의 출원번호, 출원일, 등록번호, 등록일, 상태, 특허명을 추출하기
```xml
<publication-reference> 
    <document-id>
        <country>US</country>
        <doc-number>08621662</doc-number>
        <kind>B2</kind>
        <date>20140107</date>
    </document-id>
</publication-reference>

<application-reference appltype="utility">
    <document-id>
        <country>US</country>
        <doc-number>13175987</doc-number>
        <date>20110705</date>
    </document-id>
</application-reference>
```

```py
publication_reference_tag = soup.find("publication-reference")
p_document_id_tag = publication_reference_tag.find("document-id")
p_country = p_document_id_tag.find("country").get_text()
p_doc_number = p_document_id_tag.find("doc-number").get_text()
p_kind = p_document_id_tag.find("kind").get_text()
p_date = p_document_id_tag.find("date").get_text()

application_reference_tag = soup.find("application-reference")
a_document_id_tag = publication_reference_tag.find("document-id")
a_country = p_document_id_tag.find("country").get_text()
a_doc_number = p_document_id_tag.find("doc-number").get_text()
a_date = p_document_id_tag.find("date").get_text()
```

<br>

# JSON: JavaScript Object Notation
원래 웹 언어인 Javascript의 데이터 객체 표현 방식이다. 간결성으로 기계/인간이 모두 이해하기가 편리하고, 데이터 용량이 적으며, Code로의 전환이 쉽다는 장점이 있다. 이로 인해서 XML의 대체제로 많이 활용되고 있다. 그 형태는 Python의 Dict Type과 유사하다.

```json
{
    "title": "Example Schema",
    "type": "object",
    "properties": {
        "firstName": {
            "type": "string"
        },
        "lastName": {
            "type": "string"
        },
        "age": {
            "description": "Age in years",
            "type": "integer",
            "minimum": 0
        }
    },
    "required": ["firstName", "lastName"]
}
```

## JSON과 XML의 비교
동일한 기능을 수행하는 코드이지만, JSON의 코드가 더 간결하고 보기 쉽기 때문에 훨 씬 큰 데이터에 대해서는 메모리도 줄일 수 있어서 효과적이다.

[참고🔍](http://tcpschool.com/json/json_intro_xml)

```xml
# XML
<?xml version="1.0" encoding="UTF-8" ?>
<employees>
    <name>Shyam</name>
    <email>shyamjaiswal@gmail.com</email> 
</employees>
<employees>
    <name>Bob</name>
    <email>bob32@gmail.com</email>
</employees>
<employees>
    <name>Jai</name>
    <email>jai87@gmail.com</email>
</employees>
```

```json
# JSON
{
    "employees":[ 
        {
            "name":"Shyam", 
            "email":"shyamjaiswal@gmail.com"
        }, 
        {
            "name":"Bob", 
            "email":"bob32@gmail.com"
        }, 
        {
            "name":"Jai", 
            "email":"jai87@gmail.com"
        } 
    ]
} 
```

## JSON in Python
json모듈을 사용하여 쉽게 parsing하고 저장하는 것이 가능하다. 데이터 저장 및 읽기는 `Dict Type`과 상호 호환이 가능하다는 장점이 존재한다. 페이스북, 트위터, Github와 같은 웹에서 제공하는 API는 대부분 정보 교환시 JSON을 활용한다.

```json
{
    "employees":[
        {
            "firstName":"John", 
            "lastName":"Doe"
        },
        {
            "firstName":"Anna", 
            "lastName":"Smith"
        },
        {
            "firstName":"Peter", 
            "lastName":"Jones"
        }
    ]
}
```
### Read
```py
import json

with open("json_example.json", "r", encoding="utf8") as f:
    contents = f.read()
    json_data = json.loads(contents)
    print(json_data["employees"])
```

### Write
```py
import json

dict_data = {'Name': 'Zara', 'Age': 7, 'Class': 'First'}
with open("data.json", "w") as f:
    json.dump(dict_data, f)
```