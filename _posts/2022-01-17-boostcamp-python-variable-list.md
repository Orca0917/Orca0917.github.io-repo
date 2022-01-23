---
title:  "[Python Basics for AI] Variables & List"
excerpt: "프로그래밍에서 기본적으로 알아야 할 변수와 메모리에 대한 설명 그리고 자주 사용되는 리스트 자료형에 대한 소개"

categories:
  - python
tags:
  - [AI, Naver, BoostCamp]
toc: true
toc_sticky: true
 
date: 2022-01-17 02:00:00
last_modified_at: 2022-01-17 02:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# 변수
변수는 프로그래밍에 있어서 데이터를 저장하기 위한 하나의 데이터이다. 이 변수는 메모리 공간에 할당이 되어서 우리가 변수명으로 호출하면 해당 메모리에 접근하여 값을 불러오는 것이 가능하다.
```py
var_a = 5
var_b = 1.2
```

![image](https://user-images.githubusercontent.com/91870042/149783752-c95d3589-be0a-4af1-a8d7-22505551e13d.png){: .align-center}

이렇게 설명한 변수는 메모리 공간에서 위와 같이 존재한다. 메모리가 가리키는 곳은 변수가 저장된 메모리 상의 주소이며, 그 주소에는 변수에 할당된 값이 들어있는것을 볼 수 있다.

## 명명법
명명법은 변수의 이름을 짓는 규칙을 말한다. 파이썬의 명명법은 다음 4가지가 있다.
- 알파벳(a-z, A-Z), 숫자, 언더바(_)로 사용가능
- 변수명은 최대한 연관된 의미있는 단어로 표기한다.
- 변수명은 대소문자가 구분이 된다.
- 파이썬에서 지정된 예약어는 사용이 불가능하다.

<br>

# 연산
## 기본자료형
기본자료형(Primitive data type)에는 다음 4가지 자료형이 존재한다.
- 정수형(Integer)
- 실수형(Float)
- 문자형(String)
- 논리(불린)형(Boolean) // 불린 자료형은 `True`, `False` 와 같이 첫글자는 대문자로 작성되어야 한다.

```py
var_a = 3
var_b = 5
print(var_a, var_b) # 3 5

var_a = 3.3
var_b = 5.5
print(var_a, var_b) # 3.3 5.5

var_a = "abcde"
var_b = "ABCDE"
print(var_a, var_B) # abcde ABCDE

var_a = True
var_b = False
print(var_a, var_b) # True False
```

## 연산자
- 사칙연산: `+`, `-`, `*`, `/`
- 나머지, 몫: `%`, `//`
- 제곱: `**`

> 문자열간의 덧셈, 곱셈은 이어붙이기(Concatentaion), 반복으로 해석이 된다.

## 증감 연산
파이썬에서의 증감 연산은 다음과 같이 사용할 수 있다. 하지만, 다른 언어와는 다르게, `++`, `--`를 이용한 전위, 후위 연산자는 사용이 불가능하다.
```py
a = 100
a = a + 1 # 101
a += 1 # 102

a = a - 1 # 101
a -= 1 # 100

a-- # Error
a++ # Error
```

## 데이터 형 변환
```shell
a = 10
float_a = float(a)
str_a = str(a)
int_a = int(a)

>>> type(float_a)
<class 'float'>

>>> type(str_a)
<class 'str'>

>>> type(int_a)
<class 'int'>
```

<br>

# List 자료형
앞에서 배운 변수를 이용해서 값을 할당하고, 또 2개의 변수를 이용해 사칙연산과, 제곱등 다양한 연산을 수행할 수 있었다. 하지만, 변수의 개수가 100개, 100000개를 넘는다면 일일이 선언하는 것은 매우 힘들어진다. 그때 유용하게 사용할 수 있는 자료형이 `list`이다.

list는 시퀀스 자료형으로 **여러 데이터들의 집합**이라고 말할 수 있다. 다른 언어와 다른 중요한 특징은, list에 다양한 데이터 타입이 포함될 수 있다는 의미이다.
```py
# 예시: 다양한 데이터 타입을 포함한 list
list_ex = [1, 2.345, '6', "7890", False]
```

## Indexing
리스트에 저장된 값에 접근하기 위해서 `index`를 이용해 접근을 한다. 위의 예시에서 3번째 원소인 '6'에 접근하려면 index를 기반으로 다음과 같이 접근한다.
```py
var = list_ex[2]
list_len = len(list_ex) # list_ex의 길이를 반환한다.
```

## Slicing
리스트에 존재하는 여러 값들을 동시에 뽑아낼때 사용하는 기법이다.
- indexA ~ indexB까지 추출하기  
```py
# 2번째 원소부터 4번째 원소까지 출력
print(list_ex[2:5])
# 리스트의 길이를 넘어서 호출하는 경우, 최대 범위로 한정하여 계산된다.
print(list_ex[3:1000])
```
- 모든 값 추출하기  
```py
print(list_ex[:])
```
- 2칸씩 건너뛰며 출력하기  
```py
print(list_ex[::2])
print(list_ex[1:5:2]) # 2.345 "7890"
```
- 원소 거꾸로 출력하기
```py
print(list_ex[::-1])
```

## list 연산
- concatentation

```py
listA = [1, 2, 3]
listB = [4, 5, 6]
print(listA + listB) # 1, 2, 3, 4, 5, 6
print(listA * 2) # 1, 2, 3, 1, 2, 3

print(3 in listA) # True
print(5 in listA) # False
```
- 추가, 제거  

```py
listA.append(7)
listA.extend([8, 9])
listA.insert(1, 1.5)

listA.remove(3)
del listA[1]
```
추가로, 리스트내에 리스트를 갖는 <u>중첩리스트를 사용할 수 있다</u>. 2차원 배열(list)역시 중첩 리스트이다.

## list의 메모리 저장방식 (copy)
파이썬의 리스트 변수에는 다른 변수와 마찬가지로 리스트의 주소값이 저장이 된다. 하지만, **다른 리스트를 할당한다고 해서 그 모든 값이 복사가 되는 것은 아니다**. 하나의 변수에 새로운 리스트를 할당하는 것은, 그 변수가 가리키는 메모리상 주소를 새로 만들어진 리스트의 주소를 가리키게 하는 것이다. 더 자세한 것은 다음 코드를 보면서 설명한다.

```py
a = [9, 8, 7, 6, 5, 4, 3, 2, 1]
b = [10, 11, 12, 13, 14, 15, 16]
b = a # b가 가리키는 메모리상의 주소가 리스트a의 주소가 된다. (참조)

a.sort()
print(b) # [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

b가 가리키는 메모리를 리스트a가 아닌, 실제 값을 할당받고 싶다면 좀전에 살펴본 slicing을 활용해 모든 값을 복사해오는 것이 가능하다. 하지만 주의해야 하는 것은 **1차원 리스트에서만 가능하다**는 것이다.
```py
a = [9, 8, 7, 6, 5, 4, 3, 2, 1]
b = [10, 11, 12, 13, 14, 15, 16]
b = a[:]
```

1차원 그 이상의 모든 리스트 값을 직접 복사하는 `깊은 복사 (Deep copy)`가 이루어지기 위해서는 `copy`모듈을 임포트하여 사용한다.
```py
import copy
copy_list = copy.deepcopy(original_list)
```
