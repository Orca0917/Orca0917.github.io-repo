---
title:  "[Python Basics for AI] Python Module & Project"
excerpt: "파이썬 프로젝트의 기본이 되는 모듈과 패키지, 그리고 프로젝트의 개념에 대한 설명"

categories:
  - boostcamp
tags:
  - [AI, Naver, boostcamp]
toc: true
toc_sticky: true
 
date: 2022-01-19 03:00:00
last_modified_at: 2022-01-19 03:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# <span style = "color: #00adb5">Module: 모듈</span>

## Module overview
모듈 어떤 대상의 부분 혹은 조각이다. 하지만 이런 모듈(조각)을 이용해서 더 큰 프로젝트를 완성시킬 수 있게 된다. 다른 사람의 모듈을 이용해서 코드에 사용할 수 있는데 대표적으로 Built-in 모듈인 random을 사용하여 난수를 쉽게 생성할 수 있다.
> Package(패키지) 는 모듈을 모아놓은 단위, 하나의 프로그램이다.

## Module 만들기
파이썬의 모듈은 py파일을 의미한다. 같은 폴더에 모듈에 해당하는 .py파일과 사용하는 파일을 저장한 후 import문을 사용해 module을 호출한다.

```py
# in fah_converter.py
def convert_c_to_f(celcius_value):
    return celcius_value * 9.0 / 5 + 32

# in module_ex.py
import fah_converter

if __name__ == "__main__":  # import를 했을 때, 출력되는 것을 방지한다.
    print("Enter a celcius value : ")
    celcius = float(input())

    fah = fah_converter.convert_c_to_f(celcius)
    print("That's {0} degrees Fahrenheit".format(fah))
```

이렇게 모듈로 호출을 하게 되면, 📁`__pycahce__`라는 폴더 내에 `.pyc`의 확장자를 가진 파일이 생성된다. 이는 파이썬이 미리 .py파일을 기계어로 번역을 해서 만들어 높은 파일이다. 이렇게 하게 되면 다음에 더 빠른 접근이 가능하게 되어서 실행 시간에 효과적이다.

## namespace
`namespace`는 모듈을 호출할 때 범위를 정하는 방법이다. 모듈 안에는 다양한 함수와 클래스 등이 존재할 수 있다. 따라서 필요한 내용만 골라서 호출할 수 있어야 하는데, 이를 위해서는 `from`과 `import` 키워드를 사용한다. 또한 그 방법에는 3가지가 존재한다.

1. Alias 설정: 모듈명을 별칭으로 써서 사용하기  
Alias를 통해 namespace를 설정하는 것이 선호되는 방식인데, 이는 해당 함수 또는 클래스가 어디서 불러온 것인지 알 수 있기 때문이다.
```py
import fah_converter as fah
print(fah.convert_c_to_f(41.6))
```

2. 모듈에서 특정 함수 또는 클래스만 호출하기
```py
from fah_converter import convert_c_to_f
print(convert_c_to_f(41.6))
```

3. 모듈에서 모든 함수 또는 클래스 호출하기
```py
from fah_converter import *
print(convert_c_to_f(41.6))
```

## Built-in Modules
파이썬이 기본 제공하는 라이브러리이다. **문자처리, 웹, 수학** 등 다양한 모듈이 제공되며 별 다른 조치없이 import문으로 활용할 수 있다.
```py
import random
print(random.randint(0, 100))
print(random.random()) # 일반적인 난수 생성
```
```py
import time
print(time.localtime()) # 현재 시간 출력
```
```py
import urllib.request # 웹
response = urllib.request.urlopen("http://thetemlab.io")
print(response.read())
```
요새는 직접 모든 코드를 작성하기 보다 필요한 코드를 검색해서 가져와 사용하는 것이 더욱 효과적이다. 그렇기에 본인에게 맞는 모듈을 검색하여 사용하는 것이 중요하다.
- 구글검색, 모듈 import 후 구글 검색
- `help`사용하기
- 공식문서 읽어보기

<br>

# <span style = "color: #00adb5">Package: 패키지</span>

## Package overview
패키지는 하나의 대형 프로젝트를 만드는 **코드의 묶음**이다. 다양한 모듈들의 합 또는 폴더로 연결되어 있으며 , `__init__`, `__main__`등 키워드 파일명이 사용된다. 다양한 오픈 소스들이 모두 패키지로 관리가 되고 있다.

## Package 만들기

### 1. 기능들을 세부적으로 나누어 폴더로 만들기
📁game폴더 하위에 📁image, 📁sound, 📁stage

### 2. 각 폴더별로 필요한 모듈을 구현
- 📁game폴더 내 모듈 1개 생성: 📄 \_\_init__.py
- 📁image폴더 내 모듈 3개 생성: 📄 \_\_init__.py, 📄 character.py, 📄 object_type.py
- 📁sound폴더 내 모듈 3개 생성: 📄 \_\_init__.py, 📄 echo.py, 📄 bgm.py
- 📁stage폴더 내 모듈 3개 생성: 📄 \_\_init__.py, 📄 main.py, 📄 sub.py

```py
# echo.py
def echo_play(echo_number):
    print("echo {} number start".format(echo_number))
```
```py
from sound import echo # 폴더로 되어 있더라도 모듈처럼 지정하여 사용할 수 있다.
echo.echo_play(10)

>> Result: echo 10 number start
```

### 3. 폴더별로 \_\_init\_\_.py 구성
**파이썬 3.3이상부터는 하지 않아도 상관이 없는 부분**이다. 현재 폴더가 패키지임을 알리는 초기화 스크립트가 필요하며 스크립트가 없다면, 패키지로 간주하지 않는다. 하위폴더와 모듈을 모두 포함해 있어야하며, `import`와 `__all__` 키워드를 사용하여 생성한다.
- 📁game/📄\_\_init\_\_.py
```py
__all__ = ["image", "sound", "stage"]
from . import image
from . import sound
from . import stage
```

- 📁game/📁image/📄\_\_init\_\_.py
```py
__all__ = ["character", "object_type"]
from . import character
from . import object_type
```

- 📁game/📁sound/📄\_\_init\_\_.py
```py
__all__ = ["echo", "bgm"]
from . import echo
from . import bgm
```

- 📁game/📁stage/📄\_\_init\_\_.py
```py
__all__ = ["main", "sub"]
from . import main
from . import sub
```

### 4. \_\_main\_\_.py 파일 작성
\_\_main\_\_.py 파일은 📁game 폴더 내에 생성하게 되며 코드는 예시로 다음과 같이 작성할 수 있다.
```py
from stage import main
from stage import sub
from image import character
from sound.bgm import bgm_play # 모듈까지 지정해서 함수만 불러오는 것도 가능

if __name__ == "__main__":
    main.game_start()
    sub.set_stage_level(5)
    bgm_play(10)
    character.show_character()
```

### 5. [참고] package namespace
패키지 내에서 다른 폴더의 모듈을 부를 때 상대 참조로 호출하는 방법

1. 절대 참조
```py
from game.graphic.render import render_test()
```

2. 현재 디렉토리 기준
```py
from .render import render_test()
```

3. 부모 디렉토리 기준
```py
from ..sound.echo import echo_test()
```
<br>

# <span style = "color: #00adb5">Python 가상환경: Virtualenv, Conda</span>

## Python Virtual Environment Overview
두 프로젝트의 충돌을 방지하기 위해서 가상환경을 만들어준다. 가상환경은 내 컴퓨터에서 다른 파이썬 환경을 만들어 주고, 프로젝트 진행시 필요한 패키지만 설치하는 환경이다. 대표적으로 `virtualenv`와 `conda`가 존재한다.
- virtualenv: 가장 대표적인 가상환경 관리 도구. **레퍼런스와 패키지의 개수로 장점이 좋음**
- conda: 상용 가상환경 도구. miniconda기본 도구 이며 설치가 쉽고 간편하다. **Windows에서 더 장점이 많다**

## Conda 가상환경 만들기
```
conda create -n mv project python = 3.8
conda activate my_project
conda deactivate

# conda install <package_name>
conda install matplotlib
conda install tqdm
conda install jupyter
```

## Conda 가상환경 예시
`matplotlib`를 활용한 그래프 표시
> matplotlib는 대표적인 python그래프 관리 패키지로서 엑셀과 같은 그래프들을 화면에 표시해주는 역할을 수행한다. 또한 다양한 데이터 분석 도구(pands)들과 같이 함께 사용될 수 있다.

`tqdm`을 활용해 반복분의 수행시간을 확인한다. 남은 loop의 시간이나 한 번 loop을 돌때 걸리는 시간을 보여준다.

```
code matplotlib_ex.py
```
```py
import matplotlib.pyplot as plt
plt.plot([1,2,3,4])
plt.ylabel('some numbers')
plt.show()
```
```
python matplotlib_ex.py
```

<br>

# References
[📘 Which Python Package Manager Should You Use? - YouTube](https://www.youtube.com/watch?v=3J02sec99RM)

[📘 점프 투 파이썬: 패키지 - Wikidocs](https://wikidocs.net/1418)
