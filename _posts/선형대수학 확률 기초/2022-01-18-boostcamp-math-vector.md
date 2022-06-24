---
title: "벡터"
excerpt: "딥러닝에서 사용되는 벡터의 개념과 덧셈, 뺄셈, 곱셈 연산 그리고 L1-Norm, L2-Norm, 내적에 대해서 설명합니다."

categories:
  - aimath
tags:
  - [AI, Naver, BoostCamp, Math]
toc: true
toc_sticky: true
 
date: 2022-01-18 10:00:00
last_modified_at: 2022-01-18 10:00:00
---

![image](https://user-images.githubusercontent.com/91870042/175080135-6fcfcfac-1425-488d-83dd-e3eb09c646f8.png){: .align-center width="100%"}

AI와 관련된 수학의 기초가 되는 벡터에 대한 설명입니다. 벡터의 기본개념과 벡터간의 연산, N 차원의 공간에서 정의되는 노름이라는 거리의 개념과 벡터간의 내적 연산이 무엇을 의미하는지 한 번 알아보겠습니다.

# 벡터

벡터는 숫자를 원소로 가지는 리스트 또는 배열입니다. 벡터는 숫자가 어느 방향으로 나열되어 있는지에 따라 `열 벡터`와 `행 벡터`로 나눌 수 있습니다. 먼저 세로로 보여지는 것이 열벡터이며, 가로로 나열되는 것이 행벡터입니다. Python의 Numpy 패키지에서는 기본적으로 행을 기준으로 벡터가 생성됩니다.

$$
\mathbf{x} = \begin{bmatrix}
x_1 \\
x_2 \\
\vdots \\
x_d
\end{bmatrix}
\quad
\mathbf{x}^\intercal = \begin{bmatrix}
x_1 & x_2 & \dots & x_d \\
\end{bmatrix}
$$

여기서 벡터의 차원은 벡터에 존재하는 수의 개수를 말합니다. 또한 3차원 이상이 되면 좌표 공간상에서 표현하기 어렵지만, 벡터를 통해서 훨씬 큰 차원 위의 점을 표현하는 것이 가능합니다. 그리고 그 점이 바로 공간에서의 벡터입니다. 한 번 1차원, 2차원, 3차원의 벡터를 공간상에 나타내보겠습니다.

![image](https://user-images.githubusercontent.com/91870042/175086401-3299b557-c2fa-4ea1-a8c5-5c1f5f7c2db7.png){: .align-center width="100%"}

실제로 인공지능에서 다루는 벡터의 차원은 훨씬 큰 공간에서 표현됩니다. 따라서 n차원의 벡터는 n개의 축이 있는 좌표공간상의 하나의 점이라고 생각해볼 수 있습니다.

벡터의 또 다른 표현 방식은 원점으로 부터 그 점의 상대적인 위치를 표현할 수 있습니다. 위와 동일한 공간에서 상대적인 위치를 화살표로 표현하면 다음과 같습니다.

![image](https://user-images.githubusercontent.com/91870042/175088143-a96c5a28-dd48-4882-a5ea-c277e9c29b8a.png){: .align-center width="100%"}

이제 상대적 위치를 표현하는 벡터를 사용하여 덧셈, 뺄셈, 곱셈, 내적과 같은 다양한 연산을 해볼 수 있으며 바로 아래에서 하나씩 살펴보겠습니다.

<br>

## 📚 벡터의 연산

위에서 표현한 벡터의 특정 상수 값을 곱하게 되면 그 벡터의 방향을 유지한채 길이만 변화를 줄 수 있습니다. 이 곱을 스칼라 곱이라고 하며 0보다 크고 1보다 작으면 길이가 줄어들고, 1보다 크다면 길이가 늘어납니다. 만약 곱해지는 스칼라 값이 0보다 작은 음수라면, 벡터의 방향은 원점을 기준으로 반대방향을 가리키게 됩니다.

![image](https://user-images.githubusercontent.com/91870042/175092061-97b51046-9146-4866-ab45-c537654c62a1.png){: .align-center width="70%"}

이번에는 벡터의 덧셈과 뺄셈에 대해서 살펴보겠습니다. 두 벡터의 **차원이 동일**할 때 연산이 가능하며, 각 원소별로 덧셈과 뺄셈이 이루어지게 됩니다.

$$
\mathbf{x} = \begin{bmatrix}
x_1 \\
x_2 \\
\vdots \\
x_d
\end{bmatrix}
\quad
\mathbf{y} = \begin{bmatrix}
y_1 \\
y_2 \\
\vdots \\
y_d
\end{bmatrix}
\quad
\mathbf{x+y} = \begin{bmatrix}
x_1 + y_1 \\
x_2 + y_2 \\
\vdots \\
x_d + y_d
\end{bmatrix}
$$

벡터의 곱셈에는 많은 종류가 있습니다. 그 중 기본적인 `성분곱 (Hadamard product, Element-wise product)` 에 대해서 알아보겠습니다. 벡터의 성분곱은 동일한 차원의 값끼리 곱해주는 연산으로 위의 덧셈 뺄셈과 동일하지만, 곱셈으로 연산이 이루어지는 것이 차이입니다. 성분곱의 기호는 $\odot$ 으로 표현합니다.

---

방금 살펴본 벡터의 덧셈을 공간상에서 표현하면, **2개의 벡터의 움직임을 따라서 이동한 점을 가리키는 벡터**와 같습니다. 덧셈의 연산에서 뒤에오는 벡터의 시작점을 원점이 아닌 첫 번째 벡터의 끝 지점으로 이동시켜서 따라간다고 생각할 수 있습니다.

![image](https://user-images.githubusercontent.com/91870042/175183966-c1521f29-9739-44e5-9481-fb2a24ee4fe4.png){: .align-center width="30%"}

벡터의 뺄셈은 벡터의 덧셈을 이용하여 해결할 수 있습니다. 앞에서 벡터에 음수 값이 곱해지면 원점을 기준으로 반대방향을 가리킨다고 했었습니다. 이 성질을 이용해서 벡터가 반대방향을 가리키도록 하고, 덧셈과 같은 연산을 수행해서 좌표평면 상에 나타내면 다음과 같습니다.

![image](https://user-images.githubusercontent.com/91870042/175184259-04b37fe0-7700-42f8-bfa4-729bad81bf3a.png){: .align-center width="25%"}

<br>

## 📔 노름

벡터의 노름은 **원점에서부터 점까지의 거리**를 말합니다. 이 거리를 정의하는 기준은 총 2가지가 있고 각각 `L1-노름, L2-노름`이라고 합니다. 기호로는 $\|\| \cdot \|\|_1$ 또는 $\|\| \cdot \|\|_2$ 라고 표현합니다. 아래첨자는 노름을 구분하는 숫자가 됩니다.

![image](https://user-images.githubusercontent.com/91870042/175185139-0e8ad9a2-6e04-4d93-a54a-9354c617b339.png){: .align-center width="30%"}

먼저 L1-노름은 **벡터에 존재하는 원소들의 절대값 합**입니다. 이 거리는 원점에서부터 벡터가 가리키는 점까지 좌표축을 따라서 이동할 때, 이동한 거리들의 합을 말합니다. 위의 사진에서는 $\lvert x_1\rvert + \lvert x_2\rvert$ 값이 L1-노름이 됩니다. 다시 한 줄로 말하자면, 각 성분의 변화량의 절대값을 모두 더한 값입니다.

$$ \|\mathbf{x}\|_1 = \sum_{i=1}^{d}|x_i| $$

다음 L2-노름은 우리가 흔히 사용하는 **유클리드 거리**와 같습니다. 벡터의 원소 각각 제곱을 취한 후 더하여 제곱근을 씌운 값이 유클리드 거리입니다. 위의 그림에서는 빨간선이 유클리드 거리이자 벡터의 L2-노름입니다.

$$ \|\mathbf{x}\|_2 = \sqrt{\sum_{i=1}^{d}\lvert x_i \rvert^2} $$

거리를 정의하는 서로 다른 2가지 기법이 있기 때문에 각 노름을 사용하여 원을 그리게 되면 서로 다른 모습을 보여주게 됩니다. 원이라는 것은, 어떤 한 점으로부터 거리가 동일한 점들의 집합을 말하는데, 이 거리의 정의가 달라지면 보여지는 것도 서로 다르게 됩니다.

![image](https://user-images.githubusercontent.com/91870042/175186341-563551a0-3049-478c-b027-d2fe9d9dee58.png){: .align-center width="60%"}

주로 L1노름을 사용하는 머신러닝에는 Robust 학습, Lasso회귀가 있고 L2 노름을 사용하는 학습에는 Laplace 근사, Ridge 회귀가 있습니다. 또한, 두 벡터 사이의 거리를 계산하기 위해서 벡터의 뺄셈을 사용한 후, 각 L1-노름 또는 L2-노름을 사용하여 거리를 계산할 수 있습니다. 이때, 벡터의 뺄셈은 순서가 뒤바뀌어도 동일한 결과를 보여줍니다.

- Lasso 회귀 : 선형회귀에 L1 Regularization 을 적용한 모델
- Ridge 회귀 : 선형회귀에 L2 Regularization 을 적용한 모델

<br>

## 📕 각도

위에서 두 벡터 사이의 거리를 계산하였기 때문에, 각도도 구해볼 수 있습니다. 각도를 구할 때는 `제2코사인 법칙`을 사용합니다. 제2 코사인 법칙은 삼각형의 3변의 길이를 알고 있을 때, 하나의 각을 구할 수 있는 방법입니다. 주의할 점은 벡터의 각을 구할 때는 **L2-노름만 사용가능**합니다. 먼저 제2코사인 법칙의 식은 삼각형 ABC가 있을 때 다음과 같습니다.

![image](https://user-images.githubusercontent.com/91870042/175188036-ef296867-8e68-4f38-8adb-d71132f1974e.png){: .align-center width="40%"}

$$ a^2 = b^2 + c^2 - 2bc \cos A $$

$$ \cos A = \frac{b^2 + c^2 - a^2}{2bc} $$

이번에는 두 벡터의 각도를 구한다고 가정해보겠습니다. 두 벡터사이의 거리를 노름으로 정의를 한 적이 있으므로 삼각형과 동일하게 대입하여 문제를 해결할 수 있습니다. 두 벡터사이의 각을 구하는 수식은 다음과 같습니다!

![image](https://user-images.githubusercontent.com/91870042/175188566-d2870874-2137-4303-94c5-221157aa0ace.png){: .align-center width="30%"}

$$ \cos \theta  = \frac{\|\mathbf{x}\|_2^2 + \|\mathbf{y}\|_2^2 - \|\mathbf{x-y}\|_2^2}{2\|\mathbf{x}\|_2\|\mathbf{y}\|_2}$$

이때, 분자는 `내적`의 개념을 사용하여 더 깔끔하게 정리할 수 있습니다.

$$ \left<\mathbf{x, y}\right> = \sum_{i=1}^d x_iy_i $$

$$ \cos \theta  = \frac{2\left<\mathbf{x, y}\right>}{2\|\mathbf{x}\|_2\|\mathbf{y}\|_2} = \frac{\left<\mathbf{x, y}\right>}{\|\mathbf{x}\|_2\|\mathbf{y}\|_2}$$

<br>

## 📗 내적
내적은 정사영된 벡터의 길이와 연산에 관련이 있습니다. 정사영은 하나의 벡터를 다른 벡터에 일직선으로 투영시켜서 생긴 그림자라고 생각하면 쉽습니다. 아래의 그림에서는 $\text{Proj}(\mathbf{x})$ 값이 벡터 $\mathbf{x}$ 의 정사영된 형태입니다. 이렇게 정사영된 벡터를 알았을 때, 두 벡터의 내적은 정사영된 벡터에 다른 하나의 벡터를 곱해준 값이 됩니다.

$$ \vec{a} \cdot \vec{b} = |\vec{a}| |\vec{b}| \cos \theta $$

![image](https://user-images.githubusercontent.com/91870042/175190455-1bce070a-ee1f-4a3c-91d8-3cf4714602dc.png){: .align-center width="30%"}

$$ \left< \mathbf{x, y}\right> = \|\mathbf{x}\|_2\|\mathbf{y}\|_2\cos \theta$$

이때 얻게된 내적값은 AI분야에서 주로 유사도를 측정하는데 자주 사용됩니다. 얼마나 비슷한 곳을 가리키고 있는지 알아낼 수 있는 `코사인 유사도(Cosine Similarity)` 개념이 후에 자주 등장하게 됩니다.

코사인 유사도를 사용하면 각도가 0° 일 때, 코사인 값은 1이며, 90° 일 때는 0, 180 0° 일 때는 -1로 동일한 방향을 가리킬 때 가장 큰 값을 알려주게 됩니다. 벡터에 이를 적용하면 두 벡터가 얼마나 비슷한 곳을 가리키고 있는지 알 수 있게 됩니다.

<br>

# Reference

[🎨 이미지 소스](https://unsplash.com/photos/OyCl7Y4y0Bk)