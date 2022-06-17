---
title: "[AI Math] 행렬"
excerpt: "행렬의 개념과 연산, 그리고 벡터공간에서 가지는 의미"

categories:
  - aimath
tags:
  - [AI, Naver, BoostCamp, Math]
toc: true
toc_sticky: true
 
date: 2022-01-18 11:00:00
last_modified_at: 2022-01-18 11:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# 행렬
`행렬`은 벡터를 원소로 가지는 **2차원 배열**이다. **행렬은 행벡터를 가지는 2차원 배열이다.**

![image](https://user-images.githubusercontent.com/91870042/145036244-e4ec75b5-e992-4821-8dde-f50aac49a815.png){: .align-center}

위의 그림에서는 3개의 벡터가 행을 이루고 있는 것으로 이해할 수 있다. (3개의 `행 벡터`를 하나의 array안에 담는 코드가 numpy의 코드이다.) 

```py
X = np.array([[1, -2, 3], 
              [7, 5, 0],
              [-2, -1, 2]])
```

![image](https://user-images.githubusercontent.com/91870042/145036584-90971a98-3152-4c19-bc2d-fe1f46d326c5.png){: .align-center}

위의 사진에서 \\(X_1\\)은 \\(x_{11}, x_{12}, ... , x_{1m}\\)의 원소로 이루어진 벡터라고 볼 수 있다. 이런식으로 \\(n\\)개의 행으로 이루어진 벡터를 행렬이라고 한다. 각각의 행벡터는 \\(m\\)개의 원소를 가지고 있다고 생각할 수 있다.

행렬은 `행(row)`와 `열(column)`이라는 인덱스를 가지고 표기할 때, \\(X_{ij}\\)라고 표기하기도 한다. 행렬의 특정 행(열)을 고정하면 열(행)벡터라고 부른다.

![image](https://user-images.githubusercontent.com/91870042/145037639-3654dabe-20a7-426c-a7e0-83fc37973e13.png){: .align-center}

## 전치 행렬
`전치행렬(Transpose matrix)`은 행과 열의 인덱스가 바뀐 행렬을 말하고 \\(X^T = (x_{ji})\\)라고 표현할 수 있다.

![image](https://user-images.githubusercontent.com/91870042/145037833-66cc2463-bcc3-4578-b79c-6b7143d4e01d.png){: .align-center}

## 행렬의 이해(1)
벡터가 공간에서 한 점을 의미한다면 행렬은 공간상에서의 여러 점들을 나타낸다고 이해하면 된다. 행렬의 행 벡터 \\(x_i\\)는 *i* 번째 데이터를 의미한다. 다시 그 행벡터의 *j* 번째 변수의 값은 \\(x_{ij}\\)로 표현한다.

## 행렬의 덧셈, 뺄셈, 성분곱, 스칼라곱
> 벡터의 덧셈, 뺄셈, 성분곱, 스칼라곱과 동일하다

벡터가 같은 모양을 가지면 덧셈과 뺄셈 연산을 할 수 있었던 것 처럼, 행렬도 같은 모양을 가지면 서로 다른 두 행렬간의 덧셈과 뺄셈 연산을 할 수 있다. 실제로, 내부 연산자체도 벡터의 덧셈과 뺄셈과 동일하게 이루어진다. 

마찬가지로 성분곱과 스칼라곱 역시 동일하다. 각 행렬의 동일한 위치의 곱을 나타내거나, 모든 원소의 숫자에 똑같은 스칼라만큼 곱해주면 된다.

<br>

# <span style = "color: #00adb5">행렬 곱셈</span>
행렬 곱셈(matrix multiplication)은 *i* 번째 `행 벡터`와 *j* 번째 `열 벡터` 사이의 내적을 성분으로 가지는 행렬을 계산한다. 만약 두 벡터의 순서가 달라진다면, 행렬의 곱셈결과는 서로 다르게 나온다. 그렇기 때문에 `교환 법칙`은 성립하지 않는다. 

> **행렬의 곱은 행벡터의 열의 개수와 열벡터의 행의 개수가 동일해야만 이루어질 수 있다.**

Python에서 두 행렬간의 곱셈의 연산은 `@`기호를 통해서 나타낸다.

![image](https://user-images.githubusercontent.com/91870042/145038887-1b796943-2b5d-4260-85d1-fedff3868e70.png){: .align-center}

```py
X = np.array([[1, -2, 3], 
              [7, 5, 0],
              [-2, -1, 2]])

Y = np.array([[0, 1],
              [1, -1],
              [-2, 1]])

print(X @ Y)
```
```
array([[-8, 6],
       [ 5, 2],
       [-5, 1]])
```

<br>

# <span style = "color: #00adb5">행렬 내적</span>
numpy의 `np.inner`는 *i* 번째 `행 벡터`와 *j* 번째 `행 벡터` 사이의 내적을 계산해준다. 우리가 원하는 계산은, *i* 번째 행벡터와 *j* 번째 열벡터 사이의 내적이므로 \\(Y\\)의 행렬값을 전치한 값과 해주어야 한다. 그렇기 때문에, numpy에서 두 행벡터 사이의 내적을 계산 하기 위해서는 **두 행렬의 행벡터의 열의 개수가 일치**해야 한다.

```py
X = np.array([[1, -2, 3], 
              [7, 5, 0],
              [-2, -1, 2]])

Y = np.array([[0, 1, -1],
              [1, -1, 0]])

np.inner(X, Y)
```

<br>

# <span style = "color: #00adb5">행렬의 이해(2)</span>
행렬의 곱은 서로 다른 두 벡터를 연결시켜주는 `연산자`의 역할로서 이해할 수 있으며 행렬곱을 통해 벡터를 다른 차원의 공간으로 보낼 수 있다.

![image](https://user-images.githubusercontent.com/91870042/145040505-d8d9fa52-5e9d-48d4-a3a4-a6ba9b03d9c6.png){: .align-center}

이러한 행렬 곱을 이용해서 주어진 데이터에서 `패턴을 추출`할 수도 있으며, 주어진 `데이터를 압축`할 수 있다. 기계 학습에서는 패턴추출과 데이터의 압축의 이점을 이용하여 많이 사용된다. 행렬을 사용하는 연산, `선형변환(linear transform)`은 행렬곱으로 계산할 수 있다.

<br>

# <span style = "color: #00adb5">역행렬</span>
어떤 행렬 \\(A\\)의 연산을 거꾸로 되돌리는 행렬을 역행렬이라고 부르고 \\(A^{-1}\\)로 표기한다. 역행렬은 행과 열의 숫자가 같고 행렬식이 0이 아닌경우에만 계산할 수 있다.

❗ 역행렬의 조건
1. 행과 열의 숫자가 동일해야 한다.
2. 행렬식이 0이 아니어야 한다.


![image](https://user-images.githubusercontent.com/91870042/145041030-6a60a8f9-a91d-4332-87e7-1d275b217757.png){: .align-center}

```py
X = np.array([[1, -2, 3], 
              [7, 5, 0],
              [-2, -1, 2]])

np.linalg.inv(X) # 행렬의 역행렬 구하기
X @ np.linalg.inv(X) # 항등행렬의 값이 나온다.
```

## 유사 역행렬
역행렬을 하기 위한 조건 2가지를 만족하지 못한다면 `유사 역행렬(pseudo-inverse)` 또는 `무어-펜로즈(Moore-Penrose)` 역행렬 \\(A^+\\)를 이용한다. 행과 열의 숫자가 달라도 역행렬과 동일하지는 않지만, 유사한 결과가 나오게 계산할 수 있다.

주어진 행렬에서 행의 개수와 열의 개수에 따라서 연산되는 식이 달라진다는 점에 유의해야한다. Python에서는 `numpy.linalg.pinv`를 통해서 연산을 수행할 수 있다. 항등행렬을 계산하기 위한 행렬곱셈의 순서 역시, 행의 개수와 열의 개수에 따라서 다르니 조심해야 한다.

![image](https://user-images.githubusercontent.com/91870042/145041990-53bafa52-4382-4b2f-a776-3b483391a913.png){: .align-center}

```py
Y = np.array([[0, 1],
              [1, -1],
              [-2, 1]])

np.linalg.pinv(Y)
Y @ np.linalg.pinv(Y)
```
<br>

# <span style = "color: #00adb5">응용</span>
## 연립방정식
연립방정식의 해를 구하기 위해서는 미지수의 개수만큼, 식의 수가 있어야 하는데, 그렇지 않은 경우 해가 무한이 많거나 부정이라고 표현을 했었다. 그럴 경우 식을 만족하는 하나의 해를 구할때, 유사 역행렬을 이용해 해 중 하나를 구해낼 수 있다.

![image](https://user-images.githubusercontent.com/91870042/145042869-41b6a998-b241-48ca-9a7b-df78e4310539.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/145043053-f5f7cd9d-28f7-423f-b83e-31796fa5514f.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/145043120-543ea043-4c90-4a07-ad48-7c64feef6b74.png){: .align-center}


## 선형회귀분석
선형회귀분석은 변수의 개수보다 데이터의 개수가 더 많이 있을 때 사용할 수 있다. 유사 역행렬을 이용해서 데이터를 선형모델로 해석하는 선형회귀식을 찾을 수 있다.

여러개의 점을 하나의 행렬 \\(X\\)로 표현을 하고, 계수 벡터 \\(\beta\\)를 곱해주게 되면, 초록색 선에 해당하는 선형회귀식을 찾을 수 있다. 이때, 어떤 \\(\beta\\)를 써야 빨간 점들을 가장 잘 표현하는 선형회귀식을 찾을 수 있는지가 선형회귀 분석이다.

선형회귀 분석은 연립방정식과 달리 행이 더 많으므로, 방정식을 푸는 것은 불가능하다. 우리가 할 수 있는 것은, \\(X\beta\\)로 표현되는 선이 데이터를 잘 표현하는 선을 찾는게 목표이다. 

우리가 제일 잘 찾을 수 있는 선형회귀식 중 하나는, 우리가 찾은 선이 데이터의 \\(y\\)값을 예측할 때 그 값을 \\(\hat{y}\\)이라고 표현한다. 이때, 실제 값과의 차이(\\(\hat{y} - y\\))가 가장 적도록 해야하는 것이다. 따라서 앞에서 배웠던 \\(L_2\\)-노름을 최소화 하는 계수 벡터 \\(\beta\\)를 찾으면, 선형모델을 사용해 데이터를 잘 표현했다고 말할 수 있다.

Python에서는 `Scikit Learn`의 `LinearRegression`을 사용하여 결과를 얻어낼 수 있다.

![image](https://user-images.githubusercontent.com/91870042/145044188-c6b570d8-6303-4ccd-9970-73f2c4b90ed3.png){: .align-center}

```py
# Scikit Learn을 활용한 회귀분석
from sklearn.linear_mode import LinearRegression
model = LinearRegression()
model.fit(X, y)
y_test = model.predict(x_test)

# Moore-Penrose 역행렬
beta = np.linalg.pinv(x) @ y
y_test = np.append(x_test) @ beta
```
> 하지만 위와 같이 코드를 작성해 실행시키면, 두 결과는 서로 다르게 나온다.

선형회귀분석을 할때, `Y절편값도 같이 고려`를 해줘야한다. Scikit Learn의 경우 Y절편의 값을 자동으로 추정해주지만, 직접 구현한 Moore-Penrose의 역행렬의 경우 고려해주지 않았기 때문에 해당 부분을 추가해주면, Scikit Learn의 결과와 동일한 모습을 볼 수 있다.

```py
# Moore-Penrose 역행렬 - Y절편 추정 추가
X_ = np.array([np.append(x, [1]) for x in X]) # intercept항 추가
beta = np.linalg.pinv(X_) @ y
y_test = np.append(x_test) @ beta
```