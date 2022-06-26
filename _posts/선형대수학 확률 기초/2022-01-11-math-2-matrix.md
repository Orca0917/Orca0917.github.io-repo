---
title: "행렬"
excerpt: "AI분야의 딥러닝에서 가장 기본이 되는 행렬에 대한 개념과 기본 연산, 행렬의 내적에 대해서 알아봅니다. 추가로, 행렬이 딥러닝에서 어떤 의미를 갖는지 이해하고, 역행렬과 무어펜로즈 역행렬에 대해서도 소개합니다."

categories:
  - aimath
tags:
  - [AI, BoostCamp, Math]
toc: true
toc_sticky: true
 
date: 2022-01-18 11:00:00
last_modified_at: 2022-06-26
---

![image](https://user-images.githubusercontent.com/91870042/175780862-0b2778ab-3603-4776-a3ee-5da3a093c613.png)

지난 벡터에 이어서 이번에는 행렬에 대해서 알아봅시다! 행렬은 인공지능에 있어서 정말 기본이 되는 개념으로 반드시 이해가 필요합니다. 딥러닝의 신경망 연산에서도 행렬이 사용되며, 대부분의 데이터도 행렬의 형태로 이루어져 있습니다. 따라서 이번에는 행렬의 연산과 역행렬의 이해와 행렬을 사용하는 2개의 예시를 통해 행렬에 대해서 알아보고자 합니다.

<br>

# 행렬

행렬은 가로 벡터인 행 벡터를 원소를 갖는 2차원 배열입니다. 행렬은 일반적으로 대문자 볼드체로 표기하며, 원소인 행 벡터는 소문자 볼드로 표현합니다. 아래는 하나의 3 x 3 크기의 행렬 예시입니다.

$$
\mathbf{X} = \begin{bmatrix}
1 & 2 & 3 \\
-5 & 2 & 7 \\
0 & 1 & -9 \\
\end{bmatrix}
$$

위의 행렬을 행 벡터를 원소로 갖는 2차원 배열이라는 것을 일반화하여 수식으로 나타내면 다음과 같습니다. 다음은 $n$ 개의 행 벡터와 각 벡터에는 $m$ 개의 원소가 있다는 것을 가정하였습니다. 행렬은 행과 열이라는 인덱스를 가지며, 각 원소를 표기할 때는 행과 열을 아래첨자로 표기합니다. 예로 $i$ 번째 행, $j$ 번째 열의 원소는 $x_{ij}$ 로 표현합니다.

$$
\mathbf{X} = \begin{bmatrix}
\mathbf{x}_1 \\
\mathbf{x}_2 \\
\vdots \\
\mathbf{x}_n
\end{bmatrix}
=
\begin{bmatrix}
x_{11} & x_{12} & \cdots & x_{1m} \\
x_{21} & x_{22} & \cdots & x_{2m}  \\
\vdots & \vdots & \ddots & \vdots \\
x_{n1} & x_{n2} & \cdots & x_{nm} \\
\end{bmatrix} 
$$

<br>

## ⚽ 전치 행렬

`전치 행렬(Transpose matrix)`은 행과 열의 인덱스가 바뀐 행렬을 말하고 $\mathbf{X}^\intercal = (x_{ji})$ 라고 표현할 수 있습니다. 간단히 말하여, 왼쪽 위에서 오른쪽 아래를 향하는 직선을 긋고 이를 기준으로 서로 대칭시키면 그 행렬이 전치 행렬이 됩니다.

$$
\mathbf{X}^\intercal = \begin{bmatrix}
x_{11} & x_{21} & \cdots & x_{n1} \\
x_{12} & x_{22} & \cdots & x_{n2}  \\
\vdots & \vdots & \ddots & \vdots \\
x_{1m} & x_{2m} & \cdots & x_{nm} \\
\end{bmatrix} 
$$

<br>

## ⚾ 행렬을 이해하는 방법1

이전 글에서 벡터는 공간상에서 하나의 점을 나타낸다고 했었습니다. 행렬은 여러 벡터의 집합이기 때문에 여러 점을 나타낸다고 이해할 수 있습니다. 이때 $i$ 번째 행 벡터는 $i$ 번째 데이터를 나타내는 것이며, 각 행 벡터의 원소들은 그 데이터를 표현하는 속성이라고 볼 수 있습니다.

예를 들어, 사람을 표현한다고 하였을 때 그 사람의 키, 몸무게, 나이, 거주지 등 정보를 입력해볼 수 있습니다. 여기서 사람 1명의 데이터가 행 벡터가 되는 것이고, 키, 몸무게와 같은 속성이 행 벡터의 원소가 됩니다.

<br>

## 🥎 행렬의 연산

행렬의 기본 연산은 벡터의 기본연산과 같습니다. 벡터가 같은 모양을 가지면 덧셈과 뺄셈 연산을 할 수 있었던 것처럼, 행렬도 같은 모양을 가지면 서로 다른 두 행렬 간의 덧셈과 뺄셈 연산을 할 수 있습니다. 실제로, 내부 연산 자체도 벡터의 덧셈과 뺄셈과 같게 이루어집니다.

$$
\mathbf{X} =
\begin{bmatrix}
x_{11} & x_{12} & \cdots & x_{1m} \\
x_{21} & x_{22} & \cdots & x_{2m}  \\
\vdots & \vdots & \ddots & \vdots \\
x_{n1} & x_{n2} & \cdots & x_{nm} \\
\end{bmatrix}
\quad
\mathbf{Y} = 
\begin{bmatrix}
y_{11} & y_{12} & \cdots & y_{1m} \\
y_{21} & y_{22} & \cdots & y_{2m}  \\
\vdots & \vdots & \ddots & \vdots \\
y_{n1} & y_{n2} & \cdots & y_{nm} \\
\end{bmatrix} 
\quad
\mathbf{X\pm Y} = 
\begin{bmatrix}
x_{11} \pm y_{11} & x_{12} \pm y_{12} & \cdots & x_{1m} \pm y_{1m} \\
x_{21} \pm y_{21} & x_{22} \pm y_{22} & \cdots & x_{2m} \pm y_{2m}  \\
\vdots & \vdots & \ddots & \vdots \\
x_{n1} \pm y_{n1} & x_{n2} \pm y_{n2} & \cdots & x_{nm} \pm y_{nm} \\
\end{bmatrix}  
$$

마찬가지로 성분 곱과 스칼라 곱 역시 같습니다. 각 행렬의 동일한 위치의 곱을 해주거나, 모든 원소의 숫자에 똑같은 스칼라만큼 곱해주면 됩니다.

$$
\mathbf{X} =
\begin{bmatrix}
x_{11} & x_{12} & \cdots & x_{1m} \\
x_{21} & x_{22} & \cdots & x_{2m}  \\
\vdots & \vdots & \ddots & \vdots \\
x_{n1} & x_{n2} & \cdots & x_{nm} \\
\end{bmatrix}
\quad
\mathbf{Y} = 
\begin{bmatrix}
y_{11} & y_{12} & \cdots & y_{1m} \\
y_{21} & y_{22} & \cdots & y_{2m}  \\
\vdots & \vdots & \ddots & \vdots \\
y_{n1} & y_{n2} & \cdots & y_{nm} \\
\end{bmatrix} 
\quad
\mathbf{X\odot Y} = 
\begin{bmatrix}
x_{11}y_{11} & x_{12}y_{12} & \cdots & x_{1m}y_{1m} \\
x_{21}y_{21} & x_{22}y_{22} & \cdots & x_{2m}y_{2m}  \\
\vdots & \vdots & \ddots & \vdots \\
x_{n1}y_{n1} & x_{n2}y_{n2} & \cdots & x_{nm}y_{nm} \\
\end{bmatrix}  
$$

`행렬 곱셈(matrix multiplication)`은 **$i$ 번째 행 벡터와 $j$ 번째 열벡터 사이의 내적**을 성분으로 가지는 행렬을 계산합니다. 만약 두 벡터의 순서가 달라진다면, 행렬의 곱셈결과는 서로 다르게 나오기 때문에 `교환 법칙`은 성립하지 않습니다. 또한, 행렬의 곱은 행 벡터의 열의 개수와 열벡터의 행 개수가 같아야만 연산할 수 있습니다.

$$
\mathbf{X} =
\begin{bmatrix}
x_{11} & x_{12} & \cdots & x_{1m} \\
x_{21} & x_{22} & \cdots & x_{2m}  \\
\vdots & \vdots & \ddots & \vdots \\
x_{n1} & x_{n2} & \cdots & x_{nm} \\
\end{bmatrix}
\quad
\mathbf{Y} = 
\begin{bmatrix}
y_{11} & y_{12} & \cdots & y_{1m} \\
y_{21} & y_{22} & \cdots & y_{2m}  \\
\vdots & \vdots & \ddots & \vdots \\
y_{n1} & y_{n2} & \cdots & y_{nm} \\
\end{bmatrix} 
\quad
\mathbf{XY} = 
\sum_{k}x_{ik}\,y_{kj}
$$

Python에서 두 행렬간의 곱셈의 연산은 `@` 기호를 통해 해볼 수 있습니다!

```py
import numpy as np

X = np.array([[1, -2, 3], 
              [7, 5, 0],
              [-2, -1, 2]])

Y = np.array([[0, 1],
              [1, -1],
              [-2, 1]])

print(X @ Y)

# array([[-8, 6],
#        [ 5, 2],
#        [-5, 1]])
```

## 🏐 행렬의 내적

위에서 `@` 기호를 통해서 두 행렬의 내적을 계산할 수 있었습니다. 이와 같은 연산을 `numpy.inner()`를 통해서 계산해줄 수 있습니다. 하지만 이 2가지 연산은 아래와 같은 중요한 차이가 존재합니다.

- @ : 행 벡터와 열벡터 사이의 내적을 계산
- np.inner : 행 벡터와 행 벡터 사이의 내적을 계산

수학에서 내적을 계산할 때는 행 벡터와 열벡터를 사용하기 때문에 `np.inner()`를 사용할 때는 뒤에 오는 행렬을 전치 시켜 행 벡터와 열벡터의 내적을 계산하도록 바꾸어줘야 합니다. 따라서, numpy의 행렬 내적 연산을 수행할 때는, 두 행렬의 열의 개수가 일치해야만 합니다.

```py
import numpy as np

X = np.array([[1, -2, 3], 
              [7, 5, 0],
              [-2, -1, 2]])

Y = np.array([[0, 1],
              [1, -1],
              [-2, 1]])

np.inner(X, Y.T)
```

<br>

## 🏀 행렬을 이해하는 방법2

행렬의 곱은 서로 다른 두 벡터를 연결해주는 `연산자`의 역할로서 이해할 수 있으며 행렬 곱을 통해 벡터를 다른 차원의 공간으로 보내는 것이 가능합니다. 행렬의 내적 연산을 통해, 기존에 $m$ 차원에서 점을 표현하는 벡터를 $n$ 차원 위에서 점을 표현하는 벡터로 바꿀 수 있습니다. 이렇게 행렬은 서로 다른 두 벡터를 연결해줄 수 있습니다.

$$
z_i = \sum_{j} a_{ij}x_j\\
\begin{bmatrix}
z_1 \\
z_2 \\
\vdots \\
z_n
\end{bmatrix}
=
\begin{bmatrix}
a_{11} & a_{12} & \cdots & a_{1m} \\
a_{21} & a_{22} & \cdots & a_{2m} \\
\vdots & \vdots & \ddots & \vdots \\
a_{n1} & a_{n2} & \cdots & a_{nm} \\
\end{bmatrix} 
\begin{bmatrix}
x_1 \\
x_2 \\
\vdots \\
x_m
\end{bmatrix} 
$$

이러한 행렬 곱을 이용해서 주어진 데이터에서 `패턴을 추출`할 수도 있으며, 주어진 `데이터를 압축`할 수 있습니다! 행렬을 사용하는 연산, `선형변환(linear transform)`은 행렬 곱으로 계산할 수 있으며 딥러닝은 이 선형변환에 비선형 변환을 추가하여 학습하게 됩니다.

<br>

# 역행렬

위에서 행렬의 내적연산은 $n$ 차원의 벡터를 $m$ 차원의 벡터로 변환할 수 있는 연산이라고 이해할 수 있었습니다. 이번에는 반대로 변환된 벡터를 다시 원래의 공간(차원)으로 되돌려 놓는 작업을 하고 싶다고 합시다. 이럴 때 사용되는 것이 바로 역행렬입니다. 

어떤 행렬 $\mathbf{A}$ 의 연산을 거꾸로 되돌리는 행렬을 역행렬이라고 부르고 $\mathbf{A}^{-1}$ 로 표기합니다. 중요한 점은 **역행렬은 행과 열의 숫자가 같고 행렬식이 0이 아닌 경우에만 계산할 수 있습니다.**

> ❗ 역행렬의 조건
> 1. 행과 열의 숫자가 동일해야 한다.
> 2. 행렬식이 0이 아니어야 한다.

역행렬 $\mathbf{A}^{-1}$의 특징으로 원래의 행렬 $\mathbf{A}$ 과 곱해지게 되면, 대각선이 1이고 나머지 값이 0인 항등행렬 $\mathbf{I}$ 를 얻을 수 있습니다. 일반적인 행렬의 곱셈연산과 달리 이때 곱해지는(내적) 순서는 상관이 없게 되며 반드시 항등행렬의 결과를 내게 됩니다. Python에서는 numpy의 linalg 패키지 내에 있는 `np.linalg.inv()`를 통해서 역행렬을 아주 간단하게 구할 수 있습니다!

```py
import numpy as np

X = np.array([[1, -2, 3], 
              [7, 5, 0],
              [-2, -1, 2]])

np.linalg.inv(X)      # 행렬의 역행렬 구하기
X @ np.linalg.inv(X)  # 항등행렬의 값이 나온다.
```

<br>

## 🎱 유사 역행렬

역행렬을 구하기 위해서 numpy 패키지를 사용한다면 정말 간단하게 답을 구해낼 수 있었습니다. 하지만 실제 상황 속에서 행과 열의 수가 같으며 행렬식이 0이 아닌 경우는 드물 것입니다. 이때도 역행렬을 구하고 싶다면 다른 방법을 찾아야 합니다. 다른 방법을 통해서 찾은 역행렬을 우리는 `유사 역행렬(pseudo-inverse)` 또는 `무어-펜로즈(Moore-Penrose)` 역행렬이라고 합니다. 기호로는 $\mathbf{A}^+$로 표현합니다. 무어-펜로즈 역행렬을 사용하면 행과 열의 숫자가 달라도, 역행렬과 같지는 않지만 유사한 결과가 나오게 계산할 수 있습니다!!😃

유사 역행렬을 계산할 때, 주어진 행렬에서 행의 개수와 열의 개수에 따라서 연산되는 식이 달라진다는 점에 유의해야 합니다. 행과 열의 수 중 어떤 것이 더 많은 지에 따라서 계산순서는 다음과 같이 정의됩니다. 항등행렬을 계산하기 위한 행렬곱셈의 순서 역시 행의 개수와 열의 개수에 따라 달라지니 주의해야 합니다!

- $n \ge m$ : $\mathbf{A}^+ = (\mathbf{A}^\intercal \mathbf{A})^{-1}\mathbf{A}^\intercal$, $\quad\mathbf{A}^+\mathbf{A} = \mathbf{I}$
- $n \le m$ : $\mathbf{A}^+ = \mathbf{A}^\intercal(\mathbf{A} \mathbf{A}^\intercal)^{-1}$, $\quad\mathbf{A}\mathbf{A}^+ = \mathbf{I}$


다행히 위의 연산은 Python에서 `numpy.linalg.pinv`를 통해서 간단히 연산을 수행할 수 있습니다.

```py
import numpy as np

A = np.array([[0, 1],
              [1, -1],
              [-2, 1]])

np.linalg.pinv(A)     # 유사 역행렬 계산
A @ np.linalg.pinv(A) # 항등행렬 계산
```
<br>

# 행렬의 응용

## 🏉 연립방정식

고등학생 때 까지는 연립방정식의 해를 구하기 위해서는 미지수의 개수만큼, 식의 수가 있어야 했고, 그렇지 않으면 해가 무한히 많거나 부정이라고 표현을 했었습니다. 바로 이런 상황에서 식을 만족하는 하나의 해를 구할 때, 유사 역행렬을 이용해 구하고자 하는 해 중 하나를 구해낼 수 있습니다.

먼저 예로, $m$개의 변수와 $n$ 개의 식이 주어진 상황을 가정해봅시다! 이 때는 변수의 개수 $m$이 식의 개수 $n$보다 더 많은 상황입니다.

$$
a_{11}x_1 + a_{12}x_2 + \cdots + a_{1m}x_m = b_1\\
a_{21}x_1 + a_{22}x_2 + \cdots + a_{2m}x_m = b_2\\
\vdots\\
a_{n1}x_1 + a_{n2}x_2 + \cdots + a_{nm}x_m = b_n\\
$$

위의 연립방정식을 행렬을 사용하여 나타내봅시다.

$$
\mathbf{Ax} = \mathbf{b}, \qquad
\mathbf{A} = \begin{bmatrix}
a_{11} & a_{12} & \cdots & a_{1m} \\
a_{21} & a_{22} & \cdots & a_{2m} \\
\vdots & \vdots & \ddots & \vdots \\
a_{n1} & a_{n2} & \cdots & a_{nm} \\
\end{bmatrix} 
\mathbf{x} = \begin{bmatrix}
x_1 \\
x_2 \\
\vdots \\
x_m
\end{bmatrix} 
\mathbf{b} = \begin{bmatrix}
b_1 \\
b_2 \\
\vdots \\
b_m
\end{bmatrix} 
$$

이어서 구하고자 하는 변수인 $x$만 남기고 역행렬을 이용하여 식을 정리해봅시다. 식이 정리된 이후에는 무어-펜로즈 유사역행렬 수식을 사용하여 문제를 해결할 수 있습니다.

$$
\begin{aligned}
\mathbf{Ax} &= \mathbf{b}\\
\Rightarrow \mathbf{x} &= \mathbf{A}^+\mathbf{b}\\
&= \mathbf{A}^\intercal(\mathbf{A}\mathbf{A}^\intercal)^{-1}\mathbf{b}
\end{aligned}
$$


## 🏈 선형회귀분석

이번에는 변수의 수보다 식(데이터)이 더 많은 경우인 선형회귀 분석에 대해서 살펴봅시다. 마찬가지로 유사 역행렬을 사용하여 데이터를 선형모델로 해석하는 선형회귀식을 찾아볼 수 있습니다. 하나 주의해야 하는 것은 주어진 데이터를 가장 잘 나타내는 선을 찾는 것이지, 모든 데이터를 만족하는 하나의 식을 찾는 것은 아닙니다.

![image](https://user-images.githubusercontent.com/91870042/175783640-b59da61b-b7bc-4a57-9e75-5bbfcaab2976.png){: .align-center width="70%"}

여러 개의 점을 하나의 행렬 $\mathbf{X}$ 로 표현을 하고, 기울기 벡터 $\beta$ 를 곱해주게 되면, 초록색 선에 해당하는 선형회귀식을 찾을 수 있습니다. 이때, 어떤 $\beta$ 를 써야 빨간 점들을 가장 잘 표현하는 선형회귀식을 찾을 수 있는지 해결하는 문제가 선형회귀 분석입니다.

선형회귀분석 과정을 거쳐 데이터를 가장 잘 표현하는 선형회귀식을 찾았다고 했을 때, 우리가 예측한 값은 $\hat{\mathbf{y}}$ 로 표현하고, 실제 데이터를 $\mathbf{y}$ 라고 표현해봅시다. 이때, 실제 값과의 차이 $\hat{\mathbf{y}} - \mathbf{y}$ 가 가장 적도록 해야 하는 것이 우리의 목표입니다. 따라서 앞에서 배웠던 L2-노름을 최소화하는 기울기 벡터 $\beta$ 를 찾으면, 선형모델을 사용해 데이터를 잘 표현했다고 말할 수 있습니다! Python에서는 `Scikit Learn`의 `LinearRegression`을 사용하여 결과를 얻어낼 수 있다.

$$
\begin{aligned}
\mathbf{X}\beta &= \hat{\mathbf{y}} \approx \mathbf{y}\\
\Rightarrow \beta &= \mathbf{X}^+\mathbf{y}\\
&= (\mathbf{X}^\intercal \mathbf{X})^{-1}\mathbf{X}^\intercal\mathbf{y}
\end{aligned}
$$

$$
\text{Objective} : \min_\beta \|\mathbf{y} - \hat{\mathbf{y}}\|_2
$$

- $\mathbf{X}$ : 빨간 점들의 $x$좌표
- $\mathbf{y}$ : 빨간 점들의 $y$좌표
- $\beta$ : 기울기

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

> 하지만 위와 같이 코드를 작성해 실행시키면, 두 결과는 서로 다르게 나온다😥

선형회귀분석을 할 때, `Y 절편값도 같이 고려`를 해줘야 합니다. Scikit Learn은 Y 절편의 값을 자동으로 추정해주지만, 직접 구현한 Moore-Penrose의 역행렬은 고려해주지 않았기 때문에 해당 부분을 추가해주면, Scikit Learn의 결과와 같은 모습을 볼 수 있습니다.

```py
# Moore-Penrose 역행렬 - Y절편 추정 추가
X_ = np.array([np.append(x, [1]) for x in X]) # intercept항 추가
beta = np.linalg.pinv(X_) @ y
y_test = np.append(x_test) @ beta
```

<br>

# References

[🎨 이미지 소스](https://unsplash.com/photos/XMFZqrGyV-Q)