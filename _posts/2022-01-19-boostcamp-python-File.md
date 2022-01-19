---
title:  "[Python Basics for AI] File / Exception / Log Handling"
excerpt: "파이썬 프로그램의 예외처리와 파일 다루기, 기록을 남기는 로깅"

categories:
  - boostcamp
tags:
  - [AI, Naver, boostcamp]
toc: true
toc_sticky: true
 
date: 2022-01-19 04:00:00
last_modified_at: 2022-01-19 04:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Exception
1. 예상가능한 예외  
개발자가 발생 여부를 사전에 인지할 수 있는 예외이다. 예시로 사용자의 잘못된 입력, 파일 오출시 파일 없음과 같은 현상이 있다. 이런 예외에 대해서는 개발자가 반드시 명시적으로 정의 해야한다.

2. 예상 불가능한 예외  
인터프리터 과정에서 발생하는 예외, 개발자 실수로 발생한다. 예시로 리스트의 범위를 넘어가는 값 호출하는 것과, 수를 0으로 나누는 예외가 있다. 수행 불가시 인터피리터가 자동 호출된다.

## Exception Handling: 예외 처리
예외가 발생할 경우 후속 조치 등 대처 필요하다. 예를 들어서, 없는 파일을 호출하게 되면 파일이 없음을 알리거나, 게임이 튕겼을 때 그 전에 게임정보를 저장하는 것이 있다. 프로그램도 하나의 제품이기 때문에 모든 잘못된 상황에 대한 대처가 필요하다. 따라서 반드시 필요한 과정이 Exception handling과정이다.

### try ~ except 문법
```py
try:
    # 예외 발생 가능 코드
except <Exception Type>:
    # 예외 발생시 대응하는 코드
```

**예시: 0으로 숫자를 나눌 때 예외처리 하기**
```py
for i in range(10):
    try:
        print(10 // i)
    except ZeroDivisionError:
        print("Error: Not divided by 0")
    except Exception as e: # 한 번에 Exception을 잡아버리면, 어디서 오류가 발생했는지 찾기가 힘들기 때문에 좋은 코드는 아니다.
        print(e)
```

### Built-in Exception의 종류
- `IndexError`: List의 Index범위를 넘어갈 때 발생하는 오류
- `NameError`: 존재하지 않는 변수를 호출할 때 발생하는 오류
- `ZeroDivisionError`: 0으로 숫자를 나눌 때 발생하는 오류
- `ValueError`: 변환할 수 없는 문자, 숫자를 변활할 때 발생하는 오류
- `FileNotFoundError`: 존재하지 않는 파일을 호출할 때 발생하는 오류
이외에도 훨씬 다양한 Built-in Exception이 존재한다.

### try~except~else 구문
```py
for i in range(10):
    try:
        result = 10 // i
    except ZeroDivisionError:
        print("Error: Not divided by 0")
    else:
        print(result)
```

### try~except~finally 구문
```py
for i in range(10):
    try:
        result = 10 // i
    except ZeroDivisionError:
        print("Error: Not divided by 0")
    finally:
        print("###")
```

### raise 구문
`raise`는 필요에 따라 강제로 Exception을 발생시킨다.
```py
while True:
    value = input("변환할 정수 값을 입력해주세요")
    for digit in value:
        if digit not in "0123456789":
            raise ValueError("숫자값을 입력하지 않으셨습니다.")
    print("정수값으로 변환된 숫자", int(value))
```

### assert 구문
`assert`는 특정 조건에 만족하지 않을 경우 예외를 발생시킨다.
```py
def get_binary_number(decimal_number):
    assert isinstance(decimal_number, int)
    return bin(decimal_number)

print(get_binary_number(10))
```
<br>

# File Handling
File system, OS에서 파일을 저장하는 트리구조 저장 체계이다.
> Wikipedia  컴퓨터 등의 기기에서 의미 있는 정보를 담는 논리적 단위. 모든 프로그램은 파일로 구성되어 있고, 파일을 사용한다.

## 파일과 디렉토리
- File(파일)
컴퓨터에서 정보를 저장하는 논리적인 단위이며 파일명과 확장자로 구성되어 있다. 파일은 실행, 쓰기, 읽기 동작을 수행할 수 있다.
- Directory(디렉토리)
폴더 또는 디렉토리로 불리며 파일과 다른 디렉토리를 포함할 수 있다.

## 파일의 종류
기본적인 파일 종류로는 `text파일`과 `binary파일`로 나뉘어진다. 컴퓨터는 text파일을 처리하기 위해서 binary파일로 변환을 시킨다. 모든 text파일도 실제로는 binary파일이며, 우리가 볼 수 있는 것은, ASCII/UNICODE 형태의 문자열 집합으로 저장되어 사람이 읽을 수 있는 것이다.

### Binary파일
![image](https://user-images.githubusercontent.com/91870042/144778613-393e4c87-70ce-4a56-8e46-b709c7d0a550.png){: .align-center}
컴퓨터만 이해할 수 있는 형태인 이진형식으로 저장된 파일이다. 일반적으로 메모장으로 열면 내용이 깨져서 보인다. 그 예시로, 엑셀파일이나 워드파일이 있다.

### Text파일
![image](https://user-images.githubusercontent.com/91870042/144778518-70011282-9ffa-4afa-8b87-0aa2b41eecea.png){: .align-center}

인간도 이해할 수 있는 형태인 문자열 형식으로 저장된 파일이다. 메모장으로 열면 우리 눈으로 읽을 수 있고, 메모장에 저장된 파일, HTML파일, 파이썬 코드파일이 대표적이다.

## Python File I/O
파이썬은 파일 처리를 위해서 `open`키워드를 사용한다.
```py
f = open("<파일 이름>", "접근 모드") # 파일에 있는 주소를 연결
f.close()
```

|파일 열기 모드|설명|
|---|---|
|r (읽기모드) | 파일을 **읽기**만 할때 사용한다. |
|w (쓰기모드) | 파일에 내용을 **쓸** 때 사용한다. |
|a (추가모드) | 파일의 **마지막에 새로운 내용을 추가**시킬 때 사용한다. |

### Python File Read
```py
f = open("i_have_a_deram.txt", "r")
contents = f.read()
print(contents)
f.close()
```

```py
# with구문과 함께 사용한 파일 입출력. // Indentation이 끝나면, close를 자동으로 해준다.
with open("i_have_a_dream.txt", "r") as my_file:
    contents = my_file.read()
    print(type(contents), contents)
```

`readlines()`: 텍스트 파일을 한 줄씩 출력하는 것도 가능하다. 하지만 이때도, 메모리에는 모든 텍스트가 올라가서 출력이 되는 것이다.

```py
# 파일로부터 한줄 씩 읽어와서 출력하는 방법
with open("i_have_a_dream.txt", "r") as my_file:
    contents = my_file.readlines()
    print(type(contents)) # list
    print(content_list)
```

`readline()`: 텍스트 파일이 너무 커서 한번에 메모리 상에 올리기 힘들경우, 한 줄씩 읽어서 보여주는 방법도 가능하다.

```py
with open("i_have_a_dream.txt", "r") as my_file:
    i = 0
    while True:
        line = my_file.readline()
        if not line:
            break
        print(str(i) + " === " + line.replace("\n", "")) #한줄 씩 값 출력
        i = i + 1
```

### Python File Write
파일을 읽는것과 비슷하게 옵션을 `w`로 바꾸어 주면 된다. 하지만 이때 `encoding=utf8`옵션이 추가된다. 그 이유는 문자를 저장할 때 어떠한 방식으로 진행할 것인지 알려줘야 하기 때문이다. Windows에서는 특이하게 `CP949`형식의 인코딩 옵션이 존재한다.
```py
f = open("count_log.txt", "w", encoding="utf8")
for i in range(1, 11):
    data = "%d번째 줄입니다.\n" % i
    f.write(data)
f.close()
```
```py
f = open("count_log.txt", "a", encoding="utf8") # append모드
for i in range(1, 11):
    data = "%d번째 줄입니다.\n" % i
    f.write(data)
f.close()
```

## Python Directory 다루기
```py
import os
os.mkdir("test dir")
```

```py
import os
try:
    os.mkdir("abs")
except FileExistsError: # 해당 파일이 이미 존재하는 경우, 발생되는 Exception
    print("Already created Directory")
```

```py
import shutil
source = "i_have_a_dream.txt"
dest = os.path.join("abc", "text.txt") # text.txt파일을 abc디렉토리 내부로 이동
shutil.copy(source, dest)
```

`pathlib`모듈을 사용하여 path를 객체로 다루는 방법도 존재한다.
```py
import pathlib
cwd = pathlib.Path.cwd() # current working directory
print(cwd) # WindowsPath('D:/workspace')
print(cwd.parent) # WindowsPath('D:/')
print(list(cwd.parents)) # [WindowsPath('D:/')]
print(list(cwd.glob("*"))) # workspace내에 존재하는 파일 출력.
```

<br>

# Log Handling

## Log파일 생성하기
```py
import os
if not os.path.isdir("log"):
    os.mkdir("log")
if not os.path.exists("log/count_log.txt"):
    f = open("log/count_log.txt", "w", encoding="utf8")
    f.write("기록이 시작됩니다\n")
    f.close()

with open("log/count_log.txt", 'a', encoding="utf8") as f:
    import random, datetime
    for i in range(1, 11)
        stamp = str(datetime.datetime.now())
        value = random.random() * 1000000
        log_line = stamp + "\t" + str(value) + " 값이 생성되었습니다\n"
        f.write(log_line)
```

## Pickle
`Pickle`은 파이썬 객체를 지속적으로 유지할 수 있게(=영속화) 하는 built-in객체이다. 데이터, object등 실행중인 정보를 저장하고 다시 불러와서 사용한다. 정보나 계산결과를 저장해야할 때 많이 사용한다.

```py
import pickle
f = open("list.pickle", "wb")
test = [1, 2, 3, 4, 5]
pickle.dump(test, f)
f.close()
```

```py
import pickle
f = open("list.pickle", "rb")
test_pickle = pickle.road(f)
print(test_pickle)
f.close()
```

## Logging: 로그 남기기
`로깅`은 프로그램이 실행되는 동안 일어나는 정보 기록을 남기는 행동이다. 주로 남기는 기록은 `유저의 접근`, `프로그램의 Exception`, `특정 함수의 사용`이다. 남길 수있는 방법에도 여러가지 있지만 대표적으로 **Console화면에 출력, 파일에 남기기, DB에 남기기**가 있다.

이렇게 기록된 로그를 분석하여 의미있는 결과를 도출할 수도 있다. 로그는 시점에 따라서 2가지 기록으로 나뉠 수 있다.
- **실행시점**에 남겨야 하는 기록: 유저를 분석하는 용도
- **개발시점**에 남겨야 하는 기록: 에러를 사전에 잡는 용도

기록을 print로 남기는 것도 가능하다. 하지만 console창에 남기는 기록은 분석시 사용이 불가능하다. 때로는 **레벨별(개발, 운영)로 기록을 남길 필요**가 있고 모듈별로 별도의 로깅을 남길 필요도 있다. 이러한 기능을 체계적으로 지원하는 모듈이 필요하다.

### Logging 모듈
`import logging` Python의 기본 log 관리 모듈이다.
```py
import logging
logging.debug("check debug")
logging.info("check info")
logging.warning("check warning")
logging.error("check error")
logging.critical("check critical")
```

### Logging level
프로그램 진행 상황에 따른 level의 로그를 출력해야 한다. 개발 시점, 운영시점마다 다른 log를 남길 수 있도록 지원해야 하며 다음과 같이 구분될 수 있다.
> Debug → info → warning → error → critical

Logging level설정은 log관리시 가장 기본이 되는 설정 정보이다. 이렇게 설정을 하게 되면 로그가 어떤 단계에서 부터 보여질 지 선택할 수 있다.

![image](https://user-images.githubusercontent.com/91870042/144779727-4b118e8c-70a1-4009-b854-52e8dbe66e0b.png){: .align-center}

```py
import logging

if __name__ == "__main__":
    logger = logging.getLogger("main")
    logging.basicConfig(level = logging.DEBUG) # 출력을 진행할 level을 설정.

    # 출력되는 로그를 저장할 곳은 지정한다.
    stream_handler = logging.FileHandler("my.log", "a", encoding="utf8")
    logger.addHandler(stream_handler)
```

## configparser
python에서 config파일을 불러올 때 사용하는 모듈이다. 프로그램의 실행 설정을 file에 저장하고 `Section`, `Key`, `Value` 형태로 설정된 설정 파일을 사용한다. 설정 파일을 Dict type으로 호출 후 사용한다.

```
# example.cfg

[SectionOne]
Status: Single
Name: Derek
Value: Yes
Age: 30
Single: True

[SectionTwo]
FavoriteColor = Green

[SectionThree]
FamilyName: Johnson
```
```py
import configparser
config = configparser.ConfigParser()

config.read('example.cfg')
print(config.sections())

for key in config['SectionTwo']:
    value = config['SectionTwo'][key]
    print("{0} : {1}".format(key, value)})
```

## argparser
`argparser`모듈은 파이썬 스크립트의 명령행 옵션을 파싱할때 사용하는 모듈이다. 특수 모듈도 많이 존재하지만, 일반적으로 argparse를 사용한다. `Command-Line Option`이라고 부른다.

```py
import argparse

parser = arparse.ArgumentParser(description='Sum two integers.')

parser.add_argument('-a', "--a_value", dest="A_value", help="A integers", type=int)
parser.add_argument('-b', "--b_value", dest="B_value", help="B integers", type=int)

args = parser.parse_args()
print("arguments = ", args)
print("args.a = ", args.a)
print("args.b = ", args.b)
print("sum = ", args.a + args.b)
```
```
# in Command Line
C:\projects\example>python argparse_example -a 10 -b 20
arguments = 10 20
args.a = 10
args.b = 20
sum = 30
```

## Logging formatter
LOG 결과값의 format을 지정해줄 수 있다. 이것을 설정정보에 저장하고 다시 불러와서 사용할 수도 있다.
```py
formatter = logging.Formatter('%(asctime)s %(levelname)s %(process)d %(message)s')

# config file로 부터 불러와서 사용하기
logging.config.fileConfig('logging.conf')
logger = logging.getLogger()
```
```
2018-01-18 22:47:04,385 ERROR 4410 ERROR occurred
2018-01-18 22:47:22,458 ERROR 4439 ERROR occurred
2018-01-18 22:47:22,458 INFO 4439 HERE WE ARE
2018-01-18 22:47:24,680 ERROR 4443 ERROR occurred
2018-01-18 22:47:24,681 INFO 4443 HERE WE ARE
2018-01-18 22:47:24,970 ERROR 4445 ERROR occurred
2018-01-18 22:47:24,970 INFO 4445 HERE WE ARE
```
Logging 예시
```py
logger.info('Open file {0}'.format("customers.csv",))

try:
    with open("customers.csv", "r") as customer_data:
        customer_reader = csv.reader(customer_data, delimiter=',', quotechar='"')
        for customer in customer_reader:
            if customer[10].upper() == "USA": #customer 데이터의 offset 10번째 값
                logger.info('ID {0} added'.format(customer[0],))
                customer_USA_only_list.append(customer) #즉 country 필드가 “USA” 것만

except FileNotFoundError as e:
    logger.error('File NOT found {0}'.format(e,))
```
