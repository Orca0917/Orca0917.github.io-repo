---
title:  "[Python Basics for AI] Python의 자료구조"
excerpt: "파이썬에서 많이 사용되는 자료구조: 스택, 큐, 덱, 그 이외의 Collections까지에 대한 이해"

categories:
  - python
tags:
  - [AI, Naver, BoostCamp]
toc: true
toc_sticky: true
 
date: 2022-01-19 00:00:00
last_modified_at: 2022-01-19 00:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Stack
- 나중에 넣은 데이터를 먼저 반환하도록 설계된 메모리 구조 (먼저 들어간 데이터가 나중에 나온다!)
- `LIFO`: Last in First out
- 스택에 데이터를 삽입할 때는 `push`, 다시 뽑아낼때는 `pop`라고 한다.

일반적으로 리스트를 사용하여 쉽게 구현하는 것이 가능하다.  
- `append(x)` : push x, 리스트의 가장 뒤에 x 원소를 삽입
- `pop()` : pop, 가장 위에 있는 (가장 뒤에 있는 원소 출력)

```py
a = [1, 2, 3, 4, 5]
a.append(10) # push 10
a.append(20) # push 20
a.pop() # pop 20

print(a) # [1, 2, 3, 4, 5, 10]
```

<br>

# Queue

- 먼저 넣은 데이터를 먼저 반환하도록 설계된 메모리 구조
- `FIFO`: First In First Out
- 큐에 데이터를 삽입하는 것은 `enqueue`, 뽑아내는 것을 `dqeueue`라고 부른다.

스택과 동일하게 리스트로 간단하게 구현하는 것이 가능하다.
- `append(x)` : push x, 리스트의 가장 뒤에 x 원소를 삽입
- `pop(0)` : 리스트에서 가장 첫 번째에 위치한 원소를 제거

```py
a = [1, 2, 3, 4, 5]
a.append(10) # enqueue 10
a.appned(20) # euqueue 20
a.pop(0) # deque 1

print(a) # [2, 3, 4, 5, 10, 20]
```

<br>

# Tuple
- 값의 변경이 불가능한 리스트
- 선언할 때의 문법은 `[ ]`가 아닌 `( )`를 사용한다.
- 사용하는 이유: 프로그램을 작동하는 동안 변경이 되면 안되는 데이터를 저장하기 위해서 존재한다. 이를 통해 사전에 사용자에 의해 값이 변경되는 것을 방지한다.

```py
tup = (1, 2, 3)
tup + tup # Concatentation
tup * 2 
len(tup) # tup의 길이

tup[0] = 5 # TypeError: 'tuple' object does not support item assignment
```

<br>

# Set
- 값을 순서없이 저장하고, 중복을 허용하지 않는 자료형
- `set`이라는 객체를 통해서 생성할 수 있다
- 원소가 추가되어도, set자료형은 정렬된 상태를 유지한다.

```py
s = set([1, 2, 3, 4, 1, 2])
print(s) # {1, 2, 3, 4}

s.add(1) # 원소 하나를 추가
s.update([3, 5, 7, 9]) # [3, 5, 7, 9]를 set에 추가
s.discard(3) # '3' 이라는 원소를 삭제
s.clear() # 모든 원소를 삭제
``` 

## 집합의 연산
- 합집합: `union`, `|`
- 교집합: `intersection`, `&`
- 차집합: `difference`, `-`

<br>

# Dict
- 데이터를 저장할 때, 구분할 수 있는 값을 함께 저장한다.
- 구분을 위한 데이터 고유의 값을 `Identifier` 또는 `Key`라고 부른다.
Dict에서 데이터를 관리할때, `key`값을 활용하여 데이터 값(value)를 관리한다.

```py
products = {3500: 'snack', 300: 'candy', 18000: 'chicken'}

product[3500] # snack
product[3500] = 'ice-cream'

products.items() # Dict 데이터의 출력, 튜플들의 리스트로 출력한다.
# products([(3500, 'ice-cream'), (300: 'candy'), (18000, 'chicken')])

product.keys() # key값으로 지정된 가격들을 출력한다.
product.values() # value값인 상품들을 출력한다.
```

<br>

# Collections
- 파이선의 확장 모듈, 자료구조
- 편의성 실행 효율 등을 사용자에게 제공한다.

## Deque
- `Double Edged Queue`의 줄임말로 `deque`(덱)이라고 부른다.
- Stack과 Queue를 동시에 지원하며 List에 비해서 빠른 자료 저장 방식을 지원하기 때문에 효율적이다.
- 추가적인 특징으로, `연결리스트`의 특징인 `rotate`와 `reverse`등의 특성을 지원한다.

```py
from collections import deque

dq = deque()
for i in range(5):
    dq.append(i)
print(dq) # [0, 1, 2, 3, 4]
dq.appendleft(10) 
print(dq) # [10, 0, 1, 2, 3, 4]

dq.rotate(1) # [0, 1, 2, 3, 4, 10]
dq.extend(([5, 6, 7])) # [0, 1, 2, 3, 4, 10, 5, 6, 7]
```

## OrderedDict
- Python 3.6 이전에 `Dict`자료형은 데이터를 입력한 순서를 보장하지 못하였는데 이를 보완하기 위해서 만들어진 자료구조이다.

```py
from collections import OrderedDict

od = OrderedDict()
```

## Default Dict
- `Dict` 자료구조를 사용할 대, 기본값을 지정하여 keyError를 방지할 수 있는 자료구조.

```py
from collections import defaultdict

dd = defaultdict(lambda : 0)
dd['first'] # 아직 First를 key로 하는 Dict를 생성하지 않았지만 0이 출력
```

## Counter
- 값이 연속적으로 들어있는 자료형에서 각 원소들이 몇개가 포함되어 있는지 세어주는 모듈이다.
- 값이 연속적으로 들어있는 자료형은 `문자열`, `리스트`와 같으 것들이다.
- 추가로, 두 Counter에 대해서 set에서 사용한 연산들을 지원한다. (`+`, `&`, `|`)

```py
from collections import Counter

c = Counter()
c = Counter('hello! Have a nice Day!')
print(c) # 문자열에 포함된 각 글자가 몇번 등장했는지를 알려준다.

c = Counter({'red': 4, 'blue': 2})
print(list(c.elements()))
# >> ['red', 'red', 'red', 'red', 'blue', 'blue']
```

## NamedTuple
- 튜플 형태로 데이터의 구조체를 저장하는 방법이다
- 저장되는 데이터의 변수를 사전에 지정하여 저장하여 바로 변수에 할당될 수 있게 도와준다.

```py
from collections import namedtuple

point = namedtuple('Point', ['x', 'y'])
p1 = Point(4, 8)
p2 = Point(x = 5, y = 12)

print(p1.x, p1.y) # 4 8
```