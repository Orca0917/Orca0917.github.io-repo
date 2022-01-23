---
title:  "[Python Basics for AI] Python Numpy"
excerpt: "Numpy: 데이터분석과 인공지능 학습에 있어 필수적으로 필요한 도구"

categories:
  - python
tags:
  - [AI, Naver, BoostCamp, Python, Numpy]
toc: true
toc_sticky: true
 
date: 2022-01-21 00:00:00
last_modified_at: 2022-01-21 00:00:00
---

# numpy introduction
numpy는 `Numerical Python`의 약자이다. numpy는 고성능 과학 계산용 패키지로서, Matrix와 Vector같은 **Array연산의 표준**이라고 볼 수 있다.

## 특징
- 일반 List에 비해서 빠르고, 메모리 효율적이다.
- 반복문 없이 데이터 배열에 대한 처리를 지원한다.
- 선형대수와 관련된 다양한 기능을 제공한다.
- C/C++, 포트란 등의 언어와 통합이 가능하다.

<br>

# ndarray
```py
import numpy as np
```
numpy의 호출 방법은 위와 같다. 일반적으로 numpy는 `np`라는 alias를 활용해 호출한다.

```py
import numpy as np

test_array = np.array([1, 4, 5, 8], float)
print(test_array)
type(test_array[3])
```
numpy의 array는 `np.array`함수를 활용하여 배열을 생성한다. numpy는 C/C++과 비슷하게 하나의 데이터 type만 배열에 넣을 수 있다. List와의 차이점은 **dynamic typing을 지원하지 않는 것**이다.

## numpy-array와 list의 차이점
numpy의 array는 메모리 상에서 연속된 공간에 위치하게 되지만, list는 메모리 주소를 참조하였다가, 다시 그 메모리 주소가 가리키는 공간에 값이 저장되어 있다. 따라서 list는 연속된 메모리 공간에 값이 존재하지는 않는다.

![image](https://user-images.githubusercontent.com/91870042/144879707-21c14afc-6e16-4a76-8c02-2bd42bca34b5.png){: .align-center}

```py
a = [1, 2, 3, 4, 5]
b = [5, 4, 3, 2, 1]
print(a[0] is b[-1]) # True

a = np.array(a)
b = np.array(b)
print(a[0] is b[-1]) # False
```

## dtype과 shape

- `shape`: Numpy array의 dimension구성을 반환한다. 해당 배열이 몇 차원으로 이루어졌는지 반환한다.
- `dtype`: Numpy array의 데이터 타입을 반환한다.

```py
test_array = np.array([1, 4, 5, "8"], float) # String Type의 데이터를 입력해도 변환된다.
print(test_array)
print(type(test_array[3])) # Float Type으로 자동 형변환을 실시
print(test_array.dtype) # Array(배열) 전체의 데이터 Type을 반환함
print(test_array.shape) # Array(배열) 의 shape을 반환함
```
```
array([ 1., 4., 5., 8.])
numpy.float64
dtype('float64')
(4,)
```

추가적으로, array의 shape에 따라서 불리는 이름이 존재한다.

![image](https://user-images.githubusercontent.com/91870042/144880743-ebf09aac-6515-46fb-a8f9-be0befccbaae.png){: .align-center}

## ndim과 size

- `ndim`: 차원의 수(=rank)
- `size`: 데이터의 개수 ex.(4, 3, 4) = 총 48개의 데이터가 존재

Array의 dtype은 C의 데이터 타입과 상당히 유사한 모습을 확인할 수 있다.
<br>

![image](https://user-images.githubusercontent.com/91870042/144881622-c0bf0b64-8a79-4f0c-bcb6-fe7d553d6bbf.png){: .align-center}

## nbytes

- `nbytes`: 해당 np.array가 몇 바이트를 할당하고 있는지 보여준다.

```py
np.array([[1, 2, 3], [4.5, "5", "6"]], dtype=np.float32).nbytes # 24
np.array([[1, 2, 3], [4.5, "5", "6"]], dtype=np.float64).nbytes # 48
np.array([[1, 2, 3], [4.5, "5", "6"]], dtype=np.int8).nbytes # 6
```

<br>

# Handling Shape
딥러닝을 공부하다보면, Array의 shape을 변경하는 경우가 많이 존재한다. 그에 따라서 array의 모양을 바꿔줘야 하는데 그때 사용하는 것이 `reshape`이다.

## reshape
Array의 shape크기를 변경하는 함수이다. element의 개수는 동일하게 유지된다.
<br>

![image](https://user-images.githubusercontent.com/91870042/144883106-d4c8c626-f73a-410a-a4bd-b5549177c34e.png){: .align-center}

```py
matrix = [[1, 2, 3, 4], [5, 6, 7, 8]]
np.array(matrix).shape # (2, 4)
np.array(matrix).reshape(8,) # (8,)
```

> reshape내에 parameter로 -1을 지정하게 되는 경우, 전체 배열의 크기를 바탕으로 자동으로 사이즈를 조절해준다.

```py
np.array(matrix).reshape(2, 4) # (2, 4)
np.array(matrix).reshape(-1, 2) # (4, 2)
```

## flatten
`flatten`은 다차원 array를 1차원의 array로 변환해주는 작업을 수행한다.
<br>

![image](https://user-images.githubusercontent.com/91870042/144883840-e012ad5c-37a1-4b19-af0c-3699afd6d316.png){: .align-center}

```py
matrix = [[[1, 2, 3, 4], [1, 2, 5, 8]], [[1, 2, 3, 4], [1, 2, 5, 8]]]
np.array(matrix).flatten() # 실제 matrix가 변경되는 것은 아니다. 할당을 해줘야 변경된다.
>>> array([1, 2, 3, 4, 1, 2, 5, 8, 1, 2, 3, 4, 1, 2, 5, 8])
```

<br>

# Indexing & Slicing

## Indexing
list와 달리 2차원 배열에서 [0, 0]표기법을 제공한다. matrix인 경우 앞은 `행`, 뒤는 `열`을 의미한다.
```py
a = np.array([[1, 2, 3], [4.5, 5, 6]], int)
print(a)
print(a[0,0]) # Two dimensional array representation #1
print(a[0][0]) # Two dimensional array representation #2

a[0,0] = 12 # Matrix 0,0 에 12 할당
print(a)

a[0][0] = 5 # Matrix 0,0 에 12 할당
print(a)
```

## Slicing
list와 달리 **행과 열 부분을 나눠서** slicing이 가능하다. 따라서 matrix의 부분 집합을 추출할 때 유용하다.
```py
array_name[index1, start:end:step]
```

```py
a = np.array([[1, 2, 3, 4, 5], [6, 7, 8, 9, 10]], int)
a[:,2:] # 전체 Row의 2열 이상
a[1,1:3] # 1 Row의 1열 ~ 2열
a[1:3] # 1 Row ~ 2Row의 전체
```

![image](https://user-images.githubusercontent.com/91870042/144884976-2a48d403-4737-497d-a9ea-4aa5fe78a747.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/144885728-e14aea81-2844-4d7b-8894-60a13ca2f53a.png){: .align-center}

<br>

# creation Function

## arange
`arange`: array의 범위를 지정하여, list의 값을 생성하는 명령어
```py
np.arange(30)
np.arange(0, 5, 0.5) # arange(start, end, step)
np.arange(30).reshape(5, 6)
```
```
array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9, ..., 29, 30])
array([0, 0.5, 1, 1.5, 2, 2.6, 3, 3.5, 4, 4.5])
array([[ 0,  1,  2,  3,  4,  5],
       [ 6,  7,  8,  9, 10, 11],
       [12, 13, 14, 15, 16, 17],
       [18, 19, 20, 21, 22, 23],
       [24, 25, 26, 27, 28, 29]])
```

## ones, zeros and empty
- `zeros`: 0으로 가득찬 ndarray를 생성한다.
```py
np.zeros(shape=(10,), dtype=np.int8)
np.zeros((2, 5))
```

- `ones`: 1로 가득찬 ndarray를 생성한다.
```py
np.ones(shape=(10,), dtype=np.int8)
np.ones((2, 5))
```

- `empty`: shape만 주어지고 비어있는 ndarray를 생성한다. **하지만, 메모리의 초기화가 이루어지지 않았기 때문에, 쓰레기 값이 존재할 수 있다.**
```py
np.empty(shape=(10,), dtype=np.int8)
np.empty((2, 5))
```

## something_like
`something_like`: 기존 ndarray의 shape크기 만큼 1, 0 또는 empty array를 반환한다.
```py
matrix = np.arange(30).reshape(5, 6)
np.ones_like(matrix)
>>> array([[ 1,  1,  1,  1,  1,  1],
           [ 1,  1,  1,  1,  1,  1],
           [ 1,  1,  1,  1,  1,  1],
           [ 1,  1,  1,  1,  1,  1],
           [ 1,  1,  1,  1,  1,  1]])
```

## identity
`identity`: 단위행렬(\\(n \times n\\)크기의 행렬, 행과 열이 일치하는 부분은 1)을 생성한다.
```py
np.identity(n = 3, dtype=np.int8)
np.identity(5)
```

## eye
`eye`: 대각선이 1인 행렬, k값의 시작 index를 직접 지정할 수 있다.
```py
np.eye(3)
np.eye(3, 5, k=2)
np.eye(N=3, M=5, dtype=np.int8) # k값을 지정하지 않으면 0으로 default
```
```
array([[ 1., 0., 0.],
       [ 0., 1., 0.],
       [ 0., 0., 1.]])

array([[ 0., 0., 1., 0., 0.],
       [ 0., 0., 0., 1., 0.],
       [ 0., 0., 0., 0., 1.]])

array([[ 1., 0., 0., 0., 0.],
       [ 0., 1., 0., 0., 0.],
       [ 0., 0., 1., 0., 0.]])
```       

## diag
`diag`: 대각 행렬의 값을 추출한다.
```py
matrix = np.arange(9).reshape(3, 3)
np.diag(matrix)
>>> array([0, 4, 8])
np.diag(matrix, k=1)
>>> array([1, 5])
```

## random sampling
`random sampling`: 데이터 분포에 따른 sampling으로 array를 생성한다.
```py
np.random.uniform(0, 1, 10).reshape(2, 5) # 균등분포
np.random.normal(0, 1, 10).reshape(2, 5) # 정규분포
```

<br>

# Operation Functions

## sum
`sum`: ndarray의 원소들간의 합을 구할 수 있고, python기본 자료형인 list의 sum과 유사하다.
```py
array = np.arange(1, 11);
print(array.sum(dtype=np.float)) # 55.0
```
이렇게 `operation function`을 실행할 때, 내부적으로 어떻게 연산이 수행되는지 알기 위해서는 `axis`(축)에 대한 개념의 이해가 필요하다.

## axis
모든 operation function을 실행할 때 기준이 되는 dimension축이다. `arrar.shape`을 했을 때, 나오는 것이 앞에서부터 axis=0, axis=1, ... 과 같이 매겨진다.
<br>

![image](https://user-images.githubusercontent.com/91870042/144947585-3d902372-e85c-4cd3-9a83-2048a9f58dbd.png){: .align-center}

```py
array = np.arange(1, 13).reshape(3, 4)
print(array.sum(axis=0), array.sum(axis=1))
>>> (array([10, 26, 42]), array([15, 18, 21, 24]))
```
![image](https://user-images.githubusercontent.com/91870042/144947768-e3451916-e559-4c0f-92f1-53f6c3ced17d.png){: .align-center}

## mean & std
- `mean`: ndarray의 원소들 간의 평균값을 반환  
- `std`: ndarray의 원소들 간의 표준 편차를 반환

## ETC: mathematical functions
위에서 언급한 함수 이외에도 다양한 수학 연산자를 제공하고 있다.
- Exponential: exp, expm1, exp2, log, log10, log1p, log2, power, sqrt
- Trigonometric: sin, cos, tan, acsin, arccos, atctan
- Hyberbolic: sinh, cosh, tanh, acsinh, arccosh, atctanh

## concatenate
2개의 ndarray를 붙이는 방법이다. 붙이는 방법은 수평적, 수직적으로 붙이는 두가지 방법이 존재한다,
- `vstack`: vertical로 하여 위, 아래의 array를 서로 연결한다.
<br>

![image](https://user-images.githubusercontent.com/91870042/144948236-6524b9c7-ab1c-426b-9922-30144e737ec0.png){: .align-center}

- `hstack`: horizontal로 하여 왼쪽, 오른쪽의 array를 서로 연결한다.
<br>

![image](https://user-images.githubusercontent.com/91870042/144948201-02c9c1eb-a4e2-4da6-b48d-bc4c20091137.png){: .align-center}

- `concatenate`: axis를 직접지정해서 2개의 ndarray를 서로 연결할 수 있다.

```py
a = np.array([1, 2, 3])
b = np.array([2, 3, 4])
np.vstack((a, b))

a = np.array([[1], [2], [3]])
b = np.array([[2], [3], [4]])
np.hstack((a, b))

a = np.array([[1, 2, 3]])
b = np.array([[2, 3, 4]])
np.concatenate((a, b), axis = 0)

a = np.array([[1, 2], [3, 4]])
b = np.array([5, 6])
b = b[np.newaxis, :] # b.reshape(-1, 2)
np.concatentate((a, b), axis = 1)
```

<br>

# Array Operations

## Element-wise Operations
`Element-wise Operation`은 Array간 shape가 동일할 때 일어나는 연산이라는 의미이다.
<br>

![image](https://user-images.githubusercontent.com/91870042/144948903-e3bc3f5c-4ed8-4db3-9e98-e6026a9dbdd5.png){: .align-center}

```py
a = np.arange(1, 13).reshape(3, 4)
print(a + a)
print(a - a)
print(a * a)
```

## Dot Product
Matrix의 기본연산이며 `dot`라는 함수를 사용한다. Matrix의 기본 연산 수행 과정은 다음과 같다.
<br>

![image](https://user-images.githubusercontent.com/91870042/144949159-a914c7c1-dd75-4a63-8e7b-08a7e3d6b76c.png){: .align-center}
```py
a = np.arange(1, 7).reshape(2, 3)
b = np.arange(7, 13).reshape(3, 2)
priont(a.dot(b))
```

## transpose
행렬의 `row`와 `col`의 전치가 일어난다.
```py
a = np.arange(1, 7).reshpae(2, 3)
print(a)
print(a.transpose()) # a.T
```
```
array([[1, 2, 3].
       [4, 5, 6]])

array([[1, 4],
       [2, 5],
       [3, 6]])
```

## broadcasting
`braodcasting`은 2개의 ndarray의 shape이 서로 다를 때, 연산을 지원해주는 기능이다. 즉, `Element-wise operation`이 아닐 경우 사용할 수 있는 연산이다.
<br>

![image](https://user-images.githubusercontent.com/91870042/144949600-dc54ff50-05d0-4834-91ef-245c26b3f64f.png){: .align-center}

```py
arr = np.arange(1, 7).reshape(2, 3)
arr + 3
```

broadcasting은 Scalar와 Vector간의 연산 이외에도, Vector와 Matrix간의 연산도 지원해준다.
<br>

![image](https://user-images.githubusercontent.com/91870042/144955007-4ced4fe8-14a8-4da2-a555-2c9dc21ac59f.png){: .align-center}


## numpy performance
일반적으로 python의 속도의 순서는 다음과 같다.
> for loop < list comprehension < numpy

Numpy는 C로 구현이 되어 있어, 성능을 확보하는 대신, 파이썬의 가장 큰 특징인 dynmaic Typing을 포기하였다. numpy는 대용량 계산에서 가장 많이 사용된다. 하지만, `concatentate`처럼 계산이 아닌, 할당에서는 연산 속도의 이점이 없다.

```py
def sclar_vector_product(scalar, vector):
       result = []
       for value in vector:
              result.append(scalar * value)
       return result

iternation_max = 100000000

vector = list(range(iternation_max))
scalar = 2

%timeit sclar_vector_product(scalar, vector) # for loop을 이용한 성능
%timeit [scalar * value for value in range(iternation_max)]
# list comprehension을 이용한 성능
%timeit np.arange(iternation_max) * scalar # numpy를 이용한 성능
```

<br>

# Comparisons
numpy의 array간의 비교를 진행하는 것을 말한다.

## All & Any
- `All`: 전체가 True이어야만, True를 반환한다. (`&`)
- `Any`: 하나라도 True이면, True를 반환한다. (`|`)

```py
a = np.arange(10)
np.any(a > 5) # True
np.any(a < 0) # False

np.all(a > 5) # False
np.all(a < 10) # True
```

## comparison operation
numpy는 배열의 크기가 동일할 때, 원소간의 비교결과를 Boolean 데이터 타입으로 반환해준다.
```py
a = np.array([1, 3, 0], float)
b = np.array([5, 2, 1], float)
a > b # False, True, False
a == b # False, False, False
(a > b).any() # True
```
`logical_and`, `logical_not`, `logical_or`의 사용
```py
a = np.array([1, 3, 0], float)
b = np.array([True, False, True], bool)
c = np.array([False, True, False], bool)

print(np.logical_and(a > 0, a < 3)) # True, False, False
print(np.logical_not(b)) # False, True, False
print(np.logical_or(b, c)) # True, True, True
```

## where
`np.where`은 2가지 방식으로 사용이 가능하다.
1. 처음에는 True, False값을 직접 지정하여 True일 경우, True로 지정한 값을, False라면 False로 지정한 값을 출력하게 한다.
```py
a = np.arange(1, 11)
np.where(a > 0, 3, 2)
```

2. 조건만 지정하여 사용하는 경우이다. 배열에서 조건에 만족하는 Index를 반환한다.
```py
a = np.arange(1, 11)
np.where(a > 5)
```

## isnan, isfinite
- `isnan`은 배열 내에, Not a Number인 값을 알려준다.
- `isfinite`은 어떤 수가 `NaN`, `np.Inf`인지 아닌지를 반환한다.

```py
a = np.array([1, np.NaN, np.Inf], float)
np.isnan(a)
>>> array([False, True, False])
np.isfinite(a)
>>> array([True, False, False])
```

## argmax & argmin, argsort
- `argmax`: 배열 내에서 최대값의 index를 반환한다.
- `argmin`: 배열 내에서 최소값의 index를 반환한다.
- `argsort`: 배열 내의 원소를 정렬하여 index를 반환한다.

```py
a = np.array([[1,  2, 4,  7],
              [9, 88, 6, 45],
              [9, 76, 3,  4]])
np.argmax(a, axis = 1)
np.argmin(a, axis = 0)
```
```
array([3, 1, 1])
array([0, 0, 2, 2])
```

<br>

# Boolean & Fancy Index

## Boolean Index
특정 조건에 따른 값을 배열 형태로 추출한다. 위에서 했던, comparison operation의 반환값이 boolean값이 었다면, 이번에는 그 값을 반환해준다. **boolean index를 사용하기 위해서는 조건에 들어가는 shape와 원래 배열의 shape가 동일해야만 사용할 수 있다.**
```py
a = np.array([1, 4, 0, 2, 3, 8, 9, 7], float)

a[a > 3]
>>> array([4., 8., 9., 7.])
```

## Fancy Index
배열의 값을 index값으로 추출하는 것을 `Fancy indexing`이라고 한다. Fancy indexing은 boolean indexing과 다르게, 조건에 들어가는 배열의 shape가 원래 배열의 shape와 동일 하지 않아도 사용이 가능하다.
```py
a = np.array([2, 4, 6, 8])
b = np.array([0, 0, 1, 3, 2, 1], int)

a[b] # a.take(b)로도 표현할 수 있다.
>>> array([2., 2., 4., 8., 6., 4.])

# Matrix형태의 데이터에 Fancy Indexing적용하기
a = np.array([[1, 4], [9, 16]], float)
b = np.array([0, 0, 1, 1, 0], int)
c = np.array([0, 1, 1, 1, 1], int)

a[b, c]
>>> array([1., 4., 16., 16., 4.])
```

<br>

# numpy data I/O
- `loadtxt`: 텍스트 파일을 호출하여 읽어들인다.
- `savetxt`: 텍스트 파일을 원하는 형태로 저장한다.
- `astype`: 데이터 타입을 변환한다.

```py
a = np.loadtxt("./populations.txt")
a_int = a.astype(int)
np.savetxt("int_data.csv", a_int, delimiter=",")
```

> numpy object :npy 파일 사용하기

```py
np.save("npy_test_object", arr = a_int)
a_test = np.load(file = "npy_test_object.npy")
npy_array = np.load(file = "npy_test.npy")
```