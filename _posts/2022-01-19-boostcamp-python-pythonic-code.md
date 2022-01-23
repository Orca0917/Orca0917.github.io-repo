---
title:  "[Python Basics for AI] Pythonic Code"
excerpt: "파이썬의 특유 문법을 의미하는 pythonic code에 대한 이해와 자주 사용하게 되는 lambda, map, reduce, asterisk의 활용"

categories:
  - python
tags:
  - [AI, Naver, BoostCamp]
toc: true
toc_sticky: true
 
date: 2022-01-19 01:00:00
last_modified_at: 2022-01-19 01:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Pythonic Code
파이썬 스타일의 코딩 기법
파이썬 특유의 문법을 활용하여 효율적으로 코드를 표현한다
고급 코드를 작성할 수록 많이 필요해 진다

```py
colors = ['red', 'blue', 'green', 'yellow']

# Method1
result = ''
for s in colors
    result += s

# Method2
result2 = ''.join(colors)
```

<br>

# Split, Join
- `split`: `문자열` 데이터를 특정한 기준값으로 나눠서 list형태로 변환을 도와주는 함수
- `join`: 나눠진 문자열들을 하나의 문자열로 합칠 때, 사이에 특정 값을 추가하여 하나의 문자열을 생성한다.

```py
str = "hello pyhton is good programming lang!"
str_list = str.split()

new_str = '-'.join(str_list)
```

<br>

# List comprehension
- 기존 리스트를 사용하여 다른 리스트를 만드는 방법
- 파이썬에서 가장 많이 사용되는 기법중에 하나이기 때문에, 반드시 알아두는 것이 좋다.

```py
arr = []
for i in range(10):
    arr.append(i)

arr2 = [i for i in range(10)]
arr_even = [i for i in range(10) if i % 2 == 0]
```

✔ Nested loop과 조건문을 이용한 리스트의 생성
```py
str1 = "abc"
str2 = "de"

new_str = [w1 + w2 for w1 in str1 for w2 in str2]
# [ad, ae, bd, be, cd, ce]

new_str = [w1 + w2 for w1 in str1 for w2 in str2 if not (w1 == w2)]
new_str = [w1 + w2 if not(w1 == w2) else w1 for w1 in str1 for w2 in str2]
```

✔ list comprehension을 통한 2D-array의 생성
```py
str1 = "abc"
str2 = "def"

# 여기서 주의해야 하는 것은, 뒤에 있는 반복문 for문이
# 앞에 있는 for문 보다 상위에 존재한다. 
# 따라서 뒤에 있는 for문이 먼저 실행이 된다.
result = [[c1 + c2 for c1 in str1] for c2 in str2]

result = [[(w1 + w2).upper(), (w1 + w2).lower(), len(w1 + w2)] for w1 in str1 for w2 in str2 if not (w1 == w2)]
```

<br>

# Enumerate, Zip
- `enumerate`: list의 원소에 있는 값을 추출할 때, index를 붙여서 추출하게 도와주는 함수이다.
```py
str1 = ['nice', 'python', 'programming']
for i, v in str:
    print(i, v)
```

- `zip`: 두개의 리스트에서 값을 병렬적으로 추출할 때 사용한다. 여기서 병렬적이라는 것은 서로 다른 두개의 리스트의 동일한 index에 위치한 원소를 추출하는 것을 말한다.

    ```py
    math = (100, 90, 80)
    kor = (90, 90, 70)
    eng = (80, 50, 100)

    [sum(value) / 3 for value in zip(math, kor, eng)]
    ```

<br>

# lambda, map, reduce
이 `lambda`, `map`, `reduce`는 간단한 코드로도 다양한 기능을 제공하지만, 코드의 직관성이 떨어지기 때문에 python3에서 사용을 권장하고 있지 않다.

그럼에도 불구하고, 실제 머신러닝을 하는 다양한 프로젝트에서 많이 사용중이다.

## lambda
람다함수는 함수의 이름없이 함수처럼 쓸 수 있는 익명의 함수이며 람다를 사용하게 되면 함수를 더 간단한 형식으로 작성할 수 있다.  람다함수의 유래는 수학의 람다 대수로 부터 따와 사용중이다.

다음 예시를 보면, 덧셈을 수행하는 함수를 따로 정의하지 않고, `lambda`를 이용해서 표기하면 더 간단하게 사용할 수 있는것을 알 수 있다.
```py
def add_func(x, y):
    return x + y
print(add_func(3, 5))

add_lambda = lambda x, y: x + y
print(add_lambda(3, 5))
```
위와 같은 간편함에도 불구하고, python3부터는 권장되지 않는 방법인데 그 이유는 다음과 같다.
- 어려운 문법
- 테스트의 어려움
- 문서화 docstring 지원하지 않음
- 코드 해석의 어려움
- 이름이 존재하지 않는 함수의 출현

## map
`map`은 리스트의 요소를 지정된 함수로 처리해주는 함수인데, 원본 리스트를 변경하지 않고 하나의 새로운 리스트를 생성한다.

map에는 리스트 뿐만 아니라 다양한 반복 가능한 객체인 `range`를 넣어서 만드는 것도 가능하다. `map`의 내부중 가장 처음에는 변환하는 함수를 넣을 수 있는데, 직접 만든 함수(람다함수)나, 특정 데이터 타입으로 변경하기를 원하면 자료형을 넣으면 원하는 자료형으로 변경할 수 있다.
```py
func = lambda x : x + 10
nums = [1, 2, 3, 4, 5]
print(list(map(func, nums))) # [11, 12, 13, 14, 15]

nums_str = list(map(str, nums))
```

## reduce
`reduce()`함수는 **functools**의 내장 모듈로서, 주로 누적 집계를 하기 위해서 사용이 된다. 기본적인 문법은 아래와 같이 구성이 되어 잇다

```py
from functools import reduce
reduce(집계 함수, 순회 가능한 데이터[, 초기값])
```
위에서 집계함수는 2개의 인자를 전달받게 생성을 해야하며, 처음으로 전달되는 값은 누적값, 두번째 전달되는 값은 현재값을 의미한다.

```py
from functools import reduce
print(reduce(lambda x, y: x + y, [1, 2, 3, 4, 5])) # [3, 6, 10, 15]
```

<br>

# Iterable Object
지금까지 `for i in range(10)`을 사용하게 되면, 0부터 9까지의 원소가 순서대로 출력되는 것을 알 수 있었다. 이는 파이썬에서 내부에 이미 구현된 것으로 그 내부를 들여다 보면 **\_\_iter\_\_**, **\_\_next\_\_**가 사용이 되고 있다. 그 예시로 다음 코드를 보자.

```py
cities = ['Seoul', 'Busan', 'jeju']

it = iter(cities)
print(next(it)) # Seoul
print(next(it)) # Busan
print(next(it)) # jeju
```

처음에 `iter()`가 가리키는 곳은 리스트의 가장 첫번째 원소의 위치이며, `next()`를 할 때마다 그 다음 장소로 이동하는 것을 알 수 있다. 이는 `linked-list`와 유사한 형태이다.

<br>

# Generator
`getnerator`는 위에서 살펴본 **iterator**을 생성해주는 함수로, 함수내에 `yield`라는 키워드를 통해서 생성할 수 있다.

해당 값이 가리키는 것은 실제 해당 변수의 값이 아닌 메모리상의 주소를 가리키고 있다. 일반적으로 `yield`가 호출되면 암시적으로 `return`이 호출된다고 생각하면 쉽게 이해할 수 있는데, 만약 `yield`문이 여러개가 구현이 되어 있다면 하나가 호출되면 그 다음줄에 위치한 코드를 실행시킨다.

```py
def generator_list(val):
    for i in range(val):
        yield i

for i in generator_list(5):
    print(i)

# 0 1 2 3 4
```

굳이 generator를 사용하는 이유는 **일반적인 iterator는 generator에 비해서 더 큰 메모리 용량을 사용하기 때문**이다.

<br>

# Function passing args
## keyword arguments
함수에 인자를 넘길 때, 직접 어떤 변수에게 인자를 전달할지 선택하는 방법이다. 다음 예시를 보면, 함수를 호출하는 코드에 순서가 바뀌어서 들어가도 keyword에 맞추어서 인수가 전달되는 것을 볼 수 있다.
```py
def print_name(my_name, your_name):
    print(f"Hi, {your_name}! my name is {my_name}")

print_name("david", "sora") # Hi, sora! my name is david
print_name(your_name = "david", my_name = "sora") # Hi, david! my name is sora
```

## Default arguments
함수에 인자를 넘길 때, 특정 인자를 넘겨받지 못하였을 때, 기본값을 사용하여 함수가 실행되는 것을 말한다.
예를 들어, 다음 코드를 보면 필요로 하는 인자는 2개이고, 함수에 전달된 인자는 1개인데 함수에서 default args를 설정하면 정상적으로 실행되는 것을 볼 수 있다.
```py
def print_name(my_name, your_name = "default_name"):
    print(f"Hi, {your_name}! my name is {my_name}")

print_name("david") # Hi, default_name! my name is david
```

## variable-length asterisk

### 가변인자 (variable-length)
함수의 정의에 asterisk(`*`)을 사용하게 되면, 전달받는 인자가 가변적이라는 것을 말한다. 다시 말해 개수가 정해지지 않은 변수를 함수의 인자로 사용하는 방법이다. 입력된 값은 튜플의 형태로 사용이 가능하고 가변 인자가 정의되는 위치는 함수에서 가장 마지막 인자에 해당되어야만 한다.

```py
def variable_len_func(a, b, *args):
    print(a + b + sum(args))

variable_len_func(1, 2, 3, 4, 5, 6, 7, 8, 9, 10) # 55
```

### 키워드 가변인자 (Keyword variable-length)
키워드 가변인자는 함수에서 인자의 이름을 따로 지정하지 않고 넘겨받을 수 있게 해준다. 가변인자와 다르게, asterisk(`*`)을 2개 사용하여 함수의 인자를 표시한다. 입력된 값은 Dict타입의 데이터로 사용이 된다.

주의해야할 점은 키워드 가변인자는 위에서 말한 가변인자의 다음에 사용되어야 하고, 마찬가지로 1개만 사용 가능하다.
```py
def kwargs_func1(**kwargs):
    print(kwargs)

def kwargs_func2(**kwargs):
    print(kwargs)
    print("First value {first}".format(**kwargs))
    print("First value {second}".format(**kwargs))
    print("First value {third}".format(**kwargs))
```

### Unpacking Container
asetrisk는 또 다르게 사용될 수 있는데 바로, tuple 이나 dict처럼 특정 자료형에 들어가 있는 값을 unpacking하는데 사용될 수 있다. 이는 함수의 입력이나 zip을 사용할 때 유용하게 사용할 수 있다.

```py
def unpack_func1(a, *args):
    print(a, args)

def unpack_func2(a, args):
    print(a, *args)

unpack_func1(1, *(2, 3, 4, 5)) # 1 (2, 3, 4, 5)
unpack_func1(1, (2, 3, 4, 5)) # 1 ((2, 3, 4, 5),)
```

```py
for data in zip(*([1, 2], [3, 4], [5, 6])):
    print(sum(data))

# 9
# 12
```
<br>

# References

[📘 리스트에 map 사용하기 - 코딩도장](https://dojang.io/mod/page/view.php?id=2286)

[📘 파이썬 reduce 함수 사용법](https://www.daleseo.com/python-functools-reduce/)

[📘 Generator(제네레이터) - Wikidocs](https://wikidocs.net/16069)