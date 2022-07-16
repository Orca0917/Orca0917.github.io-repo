---
title: "딥러닝 구조에서 사용되는 활성함수와 역전파 알고리즘"
excerpt: "선형모델의 한계를 극복한 비선형 모델과 결과를 확률적으로 해석하는 softmax 함수에 대해서 다룹니다. 또한, 학습에 사용되는 순전파 그리고 역전파에 대한 과정을 설명합니다."

categories:
  - aimath
tags:
  - [AI, BoostCamp, Math]
toc: true
toc_sticky: true
 
date: 2022-01-18 14:00:00
last_modified_at: 2022-07-04
---

![image](https://user-images.githubusercontent.com/91870042/177103578-7249d9a9-6419-44db-bd7f-287543f48ad7.png){: .align-center width="100%"}

이전까지는 주로 선형모델이 무엇이고 선형모델이 학습하는 방법이 무엇인지 무어-펜로즈의 역행렬과 경사하강법을 통해서 알아보았었습니다. 사실 실생활에서 많은 문제를 해결하기 위해서는 이런 단순한 선형모델로는 한계가 있습니다. 선형모델은 구조가 너무 단순하기에 복잡한 구조를 모두 표현하기가 어려웠기 때문입니다. 그래서 나온 것이 비선형 구조이며, 분류 문제와 같은 비교적 복잡한 문제를 해결할 수 있게 되었습니다. 이번 글에서는 비선형 모델과 여기에 사용되는 활성함수 그리고 딥러닝의 학습방법에 대해서 소개하고자 합니다.

<br>

# 신경망

## 😺 선형모델

다시 linear regression 문제에서 사용되었던 선형모델 구조를 가져오겠습니다!😂 선형모델 구조는 각 데이터에 특정한 가중치를 곱해주고, y절편에 해당하는 intercept항을 추가해주었습니다. 

$$
\begin{bmatrix}
-\mathbf{o}_1 -  \\
-\mathbf{o}_2 - \\
\vdots \\
-\mathbf{o}_n - \\
\end{bmatrix}
=
\begin{bmatrix}
-\mathbf{x}_1 -  \\
-\mathbf{x}_2 - \\
\vdots \\
-\mathbf{x}_n - \\
\end{bmatrix}
\begin{bmatrix}
w_{11} & w_{12} & \dots & w_{1p} \\
w_{21} & w_{22} & \dots & w_{2p}  \\
\vdots & \vdots & \ddots  & \vdots \\
w_{d1} & w_{d2} & \dots & w_{dp} \\
\end{bmatrix}
+
\begin{bmatrix}
\vert & \vert & \dots & \vert \\
b_1 & b_2 & \dots & b_p \\
\vert & \vert & \dots & \vert \\
\end{bmatrix} 
$$

$$
\mathbf{O} = \mathbf{X} \mathbf{W} + \mathbf{b}
$$

- $\mathbf{O}$ : 출력 행렬 ($n \times p$)
- $\mathbf{X}$ : 입력 데이터 ($n \times d$)
- $\mathbf{W}$ : 가중치 행렬 ($d \times p$)
- $\mathbf{b}$ : y절편 intercept항, bias ($n \times p$)

위에서 학습되는 것은 가중치 행렬과 y절편 intercept 행렬이며 가중치 행렬에 따라서 출력의 크기가 결정되었습니다. 이전에 설명했다시피, 행렬의 또 다른 해석은 서로 다른 차원간의 연결 또는 이동입니다. 여기서 가중치 행렬이 차원을 연결시켜주는 역할을 수행하기 때문에 출력의 크기를 결정하였습니다.

위의 수식에서 행렬연산을 그래프로 표현하여 신경망과 같이 나타내면 아래와 같습니다. 여기서 생성되는 그래프의 간선(화살표)의 수는 총 $p \times d$ 개로 가중치 행렬의 크기와 동일합니다.

![image](https://user-images.githubusercontent.com/91870042/177108548-b808b8a8-ca62-44c8-9cf6-7fafc0ee7f2b.png){: .align-center width="70%"}

현재 이 그림에서 bias 값까지 표현하지는 않았습니다. 여기서 입력 벡터와 출력 벡터가 연결되는 사이에는 가중치 행렬이 존재하여 행렬곱이 이루어진다고 이해할 수 있습니다. 이제 본격적으로 비선형 모델로 넘어가기 전에 출력값을 확률로 해석할 수 있게 도와주는 `softmax` 함수에 대해서 알아보겠습니다.

<br>

## 😹 Softmax

`소프트맥스(softmax)` 함수는 입력값을 확률로 바꾸어주는 함수로 주로 분류 문제를 해결하는 데 사용됩니다. 이 함수로 확률적으로 모델 결과를 해석할 수 있으며 사용되는 함수의 식은 아래와 같습니다.

$$
\text{softmax}(\mathbf{o}) = \left( \frac{\exp{(o_1)}}{\sum_{k=1}^{p}\exp{(o_k)}}, \cdots, \frac{\exp{(o_p)}}{\sum_{k=1}^{p}\exp{(o_k)}} \right)
$$

하나 주목할 점은 softmax 함수의 입력은 **하나의 실숫값이 아닌** 벡터이며, 각 벡터가 특정 클래스에 속할 확률을 보여줍니다. softmax 함수는 numpy로도 간단하게 구현하는 것이 가능합니다. 바로 아래의 코드에서는 softmax의 수식을 그대로 옮겨놓은 것인데, 2번째 줄의 `np.max()`를 사용하여 기존의 벡터에 뺄셈 연산을 해준 것은 지수함수의 overflow를 방지하기 위함입니다.

```python
def softmax(vec):
    denumerator = np.exp(vec - np.max(vec, axis=-1, keepdims=True))
    numerator = np.sum(denumerator, axis=-1,   keepdims=True)
    val = denumerator / numerator
    return val
```

softmax 연산은 결과를 추론할 때 사용하지는 않습니다. 추론을 하는 경우에는 결과 벡터 중 최댓값을 가진 것만 1로 출력하는 원핫(one-hot) 벡터로 연산을 하게 됩니다. 그 이유는 softmax를 취하여도 원래의 크기간의 대소관계는 그대로 유지되기 때문입니다. 하지만 반대로 학습에서는 softmax를 사용합니다.

softmax 함수는 신경망 구조의 출력 결괏값을 모두 **0과 1사이의 값으로 정규화** 하고, **모든 값의 합을 1**로 만들어준다고 볼 수 있습니다. 그렇기 때문에 자연스럽게 확률로 해석이 가능해지게 되었습니다. 물론 추론 단계에서 softmax를 사용하여 결과를 내도 되지만, 사용하지 않는 것은 불필요한 연산을 줄여 속도를 빠르게 하기 위함입니다.
{: .notice--warning}

<br>

## 😸 비선형 모델

비선형 모델의 신경망은 선형모델과 활성함수를 결합해놓은 형태입니다. 그렇다면 이제 활성함수가 무엇인지 알아봐야 합니다. `활성함수(activation function)`은 하나의 실숫값을 입력받아서 다른 실숫값을 출력하는 함수로 비선형성을 가진 함수입니다. 딥러닝을 처음 공부하면서 강조되는 활성함수는 sigmoid, tanh, ReLU 활성함수입니다. 활성함수에 대한 상세한 설명은 다른 포스팅에 작성해놓았습니다. [이 링크](https://killerwhale0917.github.io/dlbasic/DLBasic-3-activation-function/)를 통해서 바로 이동하실 수 있습니다.

맨 위에서 신경망의 구조를 살펴보았을 때, 가중치 행렬과 곱하고 bias 값을 더해준 것으로 구조가 끝났습니다. 하지만, 비선형 모델의 경우, 활성화 함수를 사용하기 때문에 더 많은 층의 신경망을 쌓는 것이 가능해졌습니다.

⛔ 활성함수를 사용하지 않는 신경망은 깊게 쌓을 수 없는 이유<br>
활성함수를 사용하지 않아도 신경망을 깊게 쌓는 것은 가능합니다. 하지만, 신경망을 그렇게 깊게 쌓는 것과 단 하나의 층만 있는 것이 동일한 결과를 출력하도록 만드는 것이 가능합니다. 단순 선형 연산의 경우, 가중치 행렬의 값 조절을 통해서 층을 줄일 수 있으며, 이는 깊게 쌓는 것의 이점을 전혀 가져갈 수 없습니다. 그렇기 때문에 비선형 함수를 사용하여 층을 깊게 쌓고, 복잡한 구조를 표현하려고 한 것입니다.
{: .notice--success}

![image](https://user-images.githubusercontent.com/91870042/177115105-ddfd665b-6737-4b44-8802-276f943eea35.png){: .align-center width="70%"}

$$
\mathbf{H} = \left( \sigma(\mathbf{z}_1), \dots, \sigma(\mathbf{z}_p) \right) \\
\sigma(\mathbf{z}) = \sigma(\mathbf{Wx + b})
$$

- $\sigma$ : 활성함수 (ex. 시그모이드, ReLU)
- $\mathbf{z}$ : 잠재벡터
- $\mathbf{H}$ : 레이어의 출력에 활성함수를 거쳐 나온 잠재벡터

$\mathbf{x}$ 를 입력으로 하여 $\mathbf{z}$ 벡터라는 출력을 얻어 다시 활성함수 $\sigma$ 를 거치면 잠재벡터 $\mathbf{H}$ 를 만들 수 있습니다. 잠재벡터 $\mathbf{H}$ 는 다시 2번째 레이어의 가중치 행렬 $\mathbf{W}^{(2)}$ 과, 절편 벡터 $\mathbf{b}^{(2)}$ 의 선형변환을 통해 2층 신경망을 구상할 수 있습니다. 바로 여기서 **선형모델을 반복적으로 사용**하고, 중간에 **활성함수를 사용**하는 것이 신경망의 핵심입니다. 위에서 2층 신경망이라고 불리는 것은 서로 다른 가중치 행렬 $W$ 가 2번 사용되기 때문입니다.

이렇게 더 많은 층을 쌓게 되면, `다층 신경망(multi-layer)`, `다층 퍼셉트론(MLP)` 이라고 부르며 이것이 오늘날의 사용되는 딥러닝의 기본적인 모형이 됩니다. 다층 신경망의 연산과정을 살펴봅시다.

$$
\begin{aligned}
  \mathbf{Z}^{(1)} &= \mathbf{XW^{(1)} + b^{(1)}}\\
  \mathbf{H}^{(1)} &= \sigma(\mathbf{Z}^{(1)})\\
  &\quad\vdots \\
  \mathbf{Z}^{(l)} &= \mathbf{H^{(l-1)}W^{(l)} + b^{(l)}}\\
  \mathbf{H}^{(l)} &= \sigma(\mathbf{Z}^{(l)})\\
  \mathbf{O} &= \mathbf{Z}^{(l)}
\end{aligned}
$$

- $\mathbf{X}$ : 입력 데이터
- $\mathbf{W}^{(i)}$ : $i$ 번째 레이어의 가중치 행렬
- $\mathbf{b}^{(i)}$ : $i$ 번째 레이어의 bias
- $\sigma$ : 활성함수
- $\mathbf{O}$ : 출력

위의 연산과정을 따라서 쭉 진행되는 것을 우리는 딥러닝의 `순전파 (forward propagation)` 라고 합니다. 여기서 하나 더 궁금증이 생길 수 있는데, 딥러닝에서 왜 여러 층을 사용하여 깊게 나타내려고 하는지 잘 이해가 안 될 수 있습니다.

> 딥러닝에서 학습에 여러 층(multi-layer)을 사용하는 이유  

이론적으로는 2층 신경망으로도 임의의 연속함수를 근사할 수 있습니다. 이것을 수학적으로 `universal approximation theorem`이라고 부르며 이론적으로는 보장하지만, 실제에 그대로 적용하기에는 무리가 있습니다.

층이 깊어지면 목적함수를 근사하는데 필요한 뉴런(노드)의 숫자가 훨씬 빨리 줄어들어 더 효율적으로 학습이 가능합니다. 반대로 층이 얇으면 필요한 뉴런의 숫자가 기하급수적으로 늘어나서 넓은 신경망이 구성되어야 합니다. 

하지만 그렇다고 층이 깊다고 해서 최적화가 잘 이루어졌다! 라고 할 수 없습니다. 층이 너무 깊어서 원하는 답에 최적화가 잘 이루어지지 않는 경우도 있으며, 이를 후에 Residual Block 을 사용하여 해결하고자 했습니다. 지금 당장이 Residuak Block에 대해서 설명하지는 않지만, 매우 중요한 개념이기 때문에 꼭 알고가는 것이 좋습니다.

<br>

# 역전파 알고리즘

딥러닝은 `역전파(Back propagation)` 알고리즘을 이용하여 각 층에 사용된 가중치 행렬과, bias를 학습합니다. 순전파 같은 경우 $\mathbf{X}$ 라는 입력을 받아서 최종 출력까지 보낼 때, 선형모델과 활성함수를 반복적으로 적용한 연산이었습니다.

이를 학습시키기 위한 경사하강법은 각각의 가중치 행렬을 학습시킬 때, 각 가중치에 대한 gradient vector 를 구해야 했었습니다. 딥러닝도 마찬가지로 경사하강법을 적용시키기 위해서 손실 함수를 미분하는데, 여러 층 쌓여있어 합성함수의 미분법을 사용합니다.

합성함수의 미분에서는 각 층에 사용된 파라미터로 미분이 되며, 이를 통해 딥러닝의 각 층에 맞는 오차를 전달하는 것이 가능해졌습니다. 또한 미분을 계산할 때, 저층의 gradient vector를 계산하기 위해서는 그 위층에 있는 gradient vector의 정보가 필요하기 때문에 출력층부터 거꾸로 오차가 전파되어야 합니다. 따라서 출력층 부터 거꾸로 전달되는 오차 때문에 `오차역전파 (back propgation)` 이라는 말을 사용합니다.

<br>

## 😻 합성함수의 미분

오차 역전파에 대해서 이해하기 위해 합성함수의 미분법인 연쇄법칙 기반 자동미분에 대한 개념을 이해해야 합니다. 관련된 자세한 내용은 [여기](https://killerwhale0917.github.io/dlbasic/DLBasic-6-backpropagation/#-%EC%97%AD%EC%A0%84%ED%8C%8C)에 정리되어 있으며 한 번 읽고 다음으로 넘어가는 것을 권장드립니다.

## 😽 2층 신경망 역전파

위에서 살펴본 2층의 신경망 구조에서 손실함수를 이용해 첫 번째 레이어의 가중치 행렬에 전달되는 오차를 계산해봅시다. 먼저 순전파 되는 2층 신경망 구조는 아래와 같습니다.

![image](https://user-images.githubusercontent.com/91870042/177810429-72e7ad59-eee0-4ba9-beba-c65d8e652b27.png){: .align-center width="70%"}

$$
\begin{aligned}
\mathbf{o} &= \mathbf{W}^{(2)}\mathbf{h} + \mathbf{b}^{(2)} \\
\mathbf{h} &= \sigma(\mathbf{z}) \\
\mathbf{z} &= \mathbf{W}^{(1)}\mathbf{x} + \mathbf{b}^{(1)}
\end{aligned}
$$

- $\mathbf{o}$ : 출력층 결과
- $\mathbf{W}^{(k)}$ : $k$ 번째 레이어의 가중치 행렬
- $\mathbf{b}^{(k)}$ : $k$ 번째 레이어의 bias
- $\mathbf{h}$ : hidden layer (hidden vector) 
- $\sigma$ : 활성함수
- $\mathbf{z}$ : 잠재 벡터
- $\mathbf{x}$ : 입력 데이터

이제 이 순전파의 반대로 오차를 전달하고자 할 때, 사용된 손실함수가 $\mathfrak{L}$ 이라면, 오차를 위한 계산은 편미분 기호를 사용하여 나타낼 수 있습니다. 그림에서는 역전파의 과정을 빨간색 화살표로 표현하였습니다.

$$
\frac{\partial\mathfrak{L}}{\partial\mathbf{W}^{(1)}}
$$

여기서 가중치 행렬은 여러 원소로 구성되어 있기 때문에, 편미분을 각 구성원소에 대해서 진행해주어야 합니다. 여기서는 $i$ 번째 행, $j$ 번째 열에 해당하는 가중치에 대해서 미분을 진행하겠습니다. 마찬가지로 출력벡터와 히든벡터에서도 몇 번째 값에 대해서 역전파를 진행할 것인지 선택해야 합니다. 각각 $l$ , $r$, $k$ 로 표현하여 편미분을 진행하였음을 표시하였습니다.

$$
\begin{aligned}
\frac{\partial\mathfrak{L}}{\partial W^{(1)}_{ij}} &= 
\sum_{l,r,k}
\frac{\partial \mathfrak{L}}{\partial o_l}
\frac{\partial o_l}{\partial h_r}
\frac{\partial h_r}{\partial z_k}
\frac{\partial z_k}{\partial W^{(1)}_{ij}}\\\\

\frac{\partial o_l}{\partial h_r} &= W_{rl}^{(2)}\\
\frac{\partial h_r}{\partial z_k} &= \sigma^\prime (z_k)\delta_{rk}\\
\frac{\partial z_k}{\partial W^{(1)}_{ij}} &= \frac{\partial}{\partial W^{(1)}_{ij}}\sum_{t}x_tW_{ik}^{(1)}=x_i\delta_{jk}\\\\

\frac{\partial\mathfrak{L}}{\partial W^{(1)}_{ij}} &= \sum_l \frac{\partial \mathfrak{L}}{\partial o_l}W_{jl}^{(2)}\sigma^\prime(z_j)x_i \qquad (\because r=k=j)
\end{aligned}
$$

중간에 편미분 과정에서 $\delta$ 값이 등장하는데, 이 값은 `크로네커 델타(Kronecker delta)` 라고 하며 $\delta_{jk}$는 $j$ 와 $k$ 의 값이 동일해야 1, 그렇지 않으면 0을 나타내는 값입니다. 이 성질을 이용해서 마지막 줄에 $r, k, j$ 가 모두 동일한 값임을 나타내었고 식을 더 간결하게 정리하였습니다.

수학적으로 일반화하여 정리하게 되어 수식적으로 보았을 때 복잡하게 와닿을 수 있지만, 실제로 오차역전파는 각 레이어에 대해 미분을 진행하여 알맞은 오차만큼 수정하여 학습하는 기법이라고 이해하시면 됩니다.

지금까지 딥러닝의 기초가 되는 부분에 대해서 학습을 하였으며, 다음 글에서는 또 다른 수학적 부분인 확률론에 대해서 알아보겠습니다🔥

<br>

# References

[🎨 이미지 소스](https://unsplash.com/photos/vpOeXr5wmR4)