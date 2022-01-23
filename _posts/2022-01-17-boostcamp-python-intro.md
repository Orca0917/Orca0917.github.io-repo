---
title:  "[Python Basics for AI] 파이썬 개요: Python Overview"
excerpt: "파이썬의 기초적인 내용에 대한 전반적인 소개"

categories:
  - python
tags:
  - [AI, Naver, BoostCamp]
toc: true
toc_sticky: true
 
date: 2022-01-17 01:00:00
last_modified_at: 2022-01-17 01:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Python
Python(파이썬)은 1991년 귀도 반 로섬(Guido van Rossum)이 발표한 언어이며, 크리스마스날 할 일이 없어 개발된 언어로도 유명하다. 먼저 언어적 특성을 살펴보면, 사용하는 운영체제를 가리지 않기 때문에 플랫폼에 독립적이다. C/C++과 달리 컴파일러가 필요없는 인터프리터 언어이다.


파이썬은 객체지향언어로 절차지향과 달리 프로그램이 단위 모듈을 중심으로 작성이 된다. 다른언어와의 가장 큰 차이점은 데이터 타입을 직접선언하지 않아도 되는 **동적 타이핑 언어**이다. 하지만, 실제로 파이썬은 개발 당시 C언어로 개발이 되었다.

## Python 이름의 유래
- 그리스 신화속의 괴물 뱀 피톤(Python)을 이용해 로고를 디자인

![image](https://user-images.githubusercontent.com/91870042/149774521-b307774c-8e8e-4449-9960-9ccc39a10c7f.png){: .align-center}

- 귀도 반 로섬이 좋아하는 코미디 프로그램 몬티 파이썬(Monty Python)으로 부터 유래

![image](https://user-images.githubusercontent.com/91870042/149774602-0a7e2879-380d-401e-8d3d-aa6d7380d9ea.png){: .align-center width="50%"}

<br>

# Interpreter와 Compiler
인터프리터
- 별도의 번역이 필요하지 않고, 소스를 실행시점에 해석한다.
- 프로그램을 간단하게 작성할 수 있고, 메모리가 적게 필요하지만 이에 수행되는 <u>시간은 오래걸리는 것이 특징</u>이다.
- 대표적인 언어로 Python과 Scalar가 존재한다.

컴파일러
- 소스코드를 어셈블리어와 기계어의 형태로 먼저 번역을 하고 실행이 이루어진다.
- <u>실행속도가 빠른것이 장점이며, 한 번 실행될때 메모리가 많이 필요하다는 것이 단점</u>이다.
- 대표적인 언어로 C, C++, C#, Java와 같은 언어가 있다.

<br>

# Python을 사용하는 이유
## 간편성과 쉬운 문법
예를 들어, 하나의 문장을 출력하는 것을 다른 언어(Java, C++)와 비교해보자

```java
class Print {
    public static void main(String ... args) {
        System.out.println("Hello World!!");
    }
}
```
```cpp
#include <iostream>
using namespace std;
int main() {
    cout << "Hello World!!" << endl;
    return 0;
}
```
```py
print("Hello World!!")
```

## 다양한 라이브러리
사람들이 많이 사용하고 찾는 기능들은 이미 라이브러리로 구현이 되어있다. 대표적인 예로, Numpy, Pandas와 같은 통계와 데이터분석, 시각화 도구들이 있다. Keras, Tensorflow, PyTorch 도 하나의 예로 들 수 있다.

## 종합
이러한 이점들 때문에 현재도 파이썬은 정말많은 사람들이 이용하고 있고, 이미 유명한 프로젝트에 파이썬이 많이 사용되었다.
- 알파고와 이세돌의 대결
- 아인슈타인의 중력파 확인
- 블랙홀 시뮬레이션

위와 같은 프로젝트에서는 이미 파이썬이 일부 사용되었고, Youtube와 Instagram도 파이썬으로 개발이 이루어지고 있다. 이렇기 때문에 Stack overflow의 자료에 의하면 갈수록 그 사용자의 수는 급증하는 중이며, 앞으로도 그 **성장 가능성이 큰** 프로그래밍 언어이다.

<br>

# References
[📘 Python Wikipedia](https://ko.wikipedia.org/wiki/%ED%8C%8C%EC%9D%B4%EC%8D%AC)

[🔍 Monty Python](https://ko.wikipedia.org/wiki/%EB%AA%AC%ED%8B%B0_%ED%8C%8C%EC%9D%B4%ED%8A%BC)

[🔍 귀도 반 로섬](https://ko.wikipedia.org/wiki/%EA%B7%80%EB%8F%84_%EB%B0%98_%EB%A1%9C%EC%84%AC)
