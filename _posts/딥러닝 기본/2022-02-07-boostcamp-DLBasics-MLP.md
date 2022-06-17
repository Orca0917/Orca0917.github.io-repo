---
title:  "퍼셉트론과 다층퍼셉트론"
excerpt: "현재 딥러닝의 기원이 되는 구조인 퍼셉트론과 다층 퍼셉트론에 대해서 알아보고 단층 퍼셉트론이 갖는 한계점과 활성함수의 필요성에 대해서 설명합니다."

categories:
  - DLBasic
tags:
  - [AI, Naver, BoostCamp, Python, Math]
toc: true
toc_sticky: true
 
date: 2022-02-07 01:00:00
last_modified_at: 2022-06-17 01:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Multi Layer Perceptrion

이번에는 딥러닝의 조상이 되는 다층 퍼셉트론(Multi Layer Perceptron, MLP)에 대해서 알아봅시다. 이 다층 퍼셉트론을 알기전에 퍼셉트론이 무엇인지 먼지 이해해야합니다. 그래서 이번에는 이 퍼셉트론이 무엇이고 어떤 구조를 띄고 있는지 그리고 이 퍼셉트론이 여러층 쌓인 다층 퍼셉트론이 딥러닝과 무슨 연관이 있는지 알아보고자 합니다.

## 퍼셉트론

퍼셉트론은 현재의 딥러닝의 기원이 되는 구조라고 할 수 있습니다. 그래서 이 퍼셉트론이 무엇인지 정확하게 이해하는 것은 딥러닝으로 나아가는데 중요한 첫걸음이라고 할 수 있습니다. 많은 문서에서 정의하는 퍼셉트론은 **여러 신호를 전달받아서 연산과정을 거쳐 하나의 신호를 출력하는 구조**를 말합니다. 예를 들어, 입력이 3개인 퍼셉트론은 아래와 같이 그려볼 수 있습니다😃

![image](https://user-images.githubusercontent.com/91870042/174200559-f7f2e6d8-0a46-4e06-8388-495d626b6438.png){: .align-center width="50%"}

이제 이 퍼셉트론의 연산과정에 대해서 알아봅시다. 위의 그림에서 입력에 해당하는 원들은 실제로 고유한 값들을 가지고 있으며, 이 각각의 값들은 파란색 선을 타고 다음 원으로 전달됩니다. 이 파란색 화살표 위에서 이동하는 과정에서 특정 가중치가 곱해집니다. 일반적으로 값들은 $x$ 로 표현하며 가중치는 $w$ 를 사용하여 표현합니다. 마찬가지로 그림으로 다시 나타내보면 다음과 같습니다😃

![image](https://user-images.githubusercontent.com/91870042/174201060-f9f2b6d2-2045-45a8-9e21-2c4f84598be1.png){: .align-center width="50%"}

이 퍼셉트론에서 $y$ 에 전달된 값은 $x_1w_1 + x_2w_2 + x_3w_3$ 라고 할 수 있습니다. 출력층에서는 이 전달된 값을 사용하여 결과물로 0 또는 1을 반환합니다. 그러면 출력층에서는 0과 1을 내보내기 위한 그 기준이 필요합니다. 일반적으로 그 기준이 되는 값을 임계값(Threshold)이라고 하며, 기호로 $\theta$ 를 사용합니다.

이 임계값 $\theta$ 를 사용하여 $y$ 에 전달된 값이 더 큰지 아니면 작은지로 구분하여 신호를 전달하거나 전달하지 않을 수 있습니다. 수식으로 나타내면 다음과 같습니다.

$$
\begin{aligned}
y=\left\{\begin{matrix}
0\,(x_1w_1 + x_2w_2 + x_3w_3 \le \theta) \\
1\,(x_1w_1 + x_2w_2 + x_3w_3 >  \theta)
\end{matrix}\right.
\end{aligned}
$$

이렇게 해서 퍼셉트론에 대한 설명이 끝났습니다🎉. 이 퍼셉트론 구조는 뉴런과 상당히 유사한 모습을 보여주며 퍼셉트론이 여러층 쌓여있는 것은 마치 사람의 신경망과 매우 유사한 형태를 띄게 됩니다. 다음은 바로 다층 퍼셉트론에 대해서 살펴보겠습니다.

## 다층 퍼셉트론

다층 퍼셉트론은 퍼셉트론이 여러겹 쌓인 형태라고 설명했었습니다. 그러면 왜 퍼셉트론만으로 연산을 하지 않고, 다층 퍼셉트론이라는 개념을 도입했는지 궁금해졌습니다. 단층 퍼셉트론은 입력값과 가중치의 곱들의 합으로 이루어진 연산이었습니다. 이런 연산은 2개의 행렬의 곱으로 표현해볼 수 있습니다. 때문에 단층퍼셉트론은 선형적인 모습을 띄게 됩니다.

$$
\begin{aligned}
  \begin{bmatrix}
  x_1& x_2 & x_3 \\
  \end{bmatrix}

  \begin{bmatrix}
  w_1 \\
  w_2 \\
  w_3
  \end{bmatrix} 
\end{aligned}
$$

선형적인 특징을 갖는 단층퍼셉트론은 실생활의 많은 데이터를 표현하고 학습하는데에 너무 많은 한계가 있습니다. 대표적으로 다음 그림을 볼 수 있습니다.

![image](https://user-images.githubusercontent.com/91870042/174202790-36311663-4a52-47a0-9832-14eedd8f2248.png){: .align-center width="70%"}

좌측에 구불구불한 선이 `비선형적`인 모습이며, 우측의 직선이 `선형적`이다 라고 표현합니다. 이번에 우리가 하고싶은 일이 흰색 점들과 검정색 점들을 분리해내는 선을 만드는 것이 목표라고 해봅시다. 우측의 경우 하나의 직선을 그어 두 분류의 점들을 완벽하게 분리해낼 수 있지만, 좌측같은 경우 직선을 이용해서 분리해낼 수 없습니다. 이것이 단층 퍼셉트론의 한계입니다. 선형성의 띄는 직선만으로는 분류를 정확히 해낼 수 없습니다. 이 문제를 해결하기 위해 비선형성을 도입해야하며 다층 퍼셉트론에서 이를 해결하고자 했습니다.

지금까지 단층 퍼셉트론의 한계에 대해서 알아봤으니 다층퍼셉트론의 구조에 대해서 살펴봅시다! 말 그대로 단층 퍼셉트론을 여러겹 쌓은 모습을 표현하면 다음과 같습니다.

![image](https://user-images.githubusercontent.com/91870042/174203692-98b58cb8-e2b1-44f9-8c69-141768e8d688.png){: .align-center width="70%"}

하지만 위에서 하나 의문이 드는 것이 있습니다. 어떻게 선형적인 구조 2개를 이어 붙인것이 비선형적인 구조를 표현할 수 있는지 궁금합니다. 선형적인 구조 2개를 단순히 이어붙이면 비선형적인 구조를 표현할 수 없습니다. 그렇기 때문에 다층 퍼셉트론에서는 `비선형함수`를 추가로 도입합니다. 이 비선형함수를 신경망 또는 딥러닝 분야에서는 `활성함수` 또는 activation function 이라고 합니다.

좀 전에 퍼셉트론은 행렬의 곱으로 표현해볼 수 있다고 하였습니다. 퍼셉트론을 여러개 연결한 다층 퍼셉트론은 행렬 연산을 여러번 한다고 볼 수 있는데, 사실 이 여러번 하는 행렬연산은 다시 2개의 행렬곱으로 축약해서 나타날 수 있습니다. 그렇기 때문에 아무리 많은 층을 쌓게 되더라도 선형성을 유지할 수 밖에 없습니다. 다시 말해 **여러층을 쌓는 효과를 전혀 낼 수 없다**는 의미입니다.
{: .notice--success}

다음은 비선형함수인 활성함수를 추가하여 만든 다층 퍼셉트론의 그림입니다. 하나의 결과물이 다음의 입력으로 전달되는 과정에서 활성함수를 거쳐 비선형성을 만드는 것을 알 수 있습니다!

![image](https://user-images.githubusercontent.com/91870042/174206014-6f93ff1a-23ca-4b40-b1a5-81408f56f960.png){: .align-center width="70%"}

비선형함수인 활성함수에 대해서 말할 내용이 많기 때문에 따로 글을 작성하도록 하겠습니다. 이 다음 게시글에는 활성함수에 대한 설명, 신경망의 구조와 모델을 평가하는데 사용되는 손실함수에 대해서 간단하게 알아보겠습니다!! 🔥

<!--
# Neural Networks
> Neural Networks are computing systems vaguely inspired by the biological neural networks that constitute animal brains. - Wikipedia

Wikipedia에서 정의하는 신경망은 동물의 뇌를 모방하는 생물학적 신경망을 모방한 컴퓨터 시스템이라고 한다. 물론 맞는 말이지만, 신경망이 반드시 인간의 뇌를 모방할 필요는 없다. 지금의 비행기가 새와 완전한 동일한 모습을 갖지 않듯이 신경망도 초기에는 모방할 수 있지만, 반드시 끝까지 그 형태를 똑같이 따라할 필요는 없다.

> Neural networks are function approximator that stack affine transformations followed by nonlinear transformations.

따라서 조금 다르게 신경망을 표현하자면, 비선형 연산이 뒤따르는 선형연산들의 구조가 여러 층 쌓여 함수를 근사하는 모델이라고 말할 수 있다.

<br>

# Linear Neural Networks

![image](https://user-images.githubusercontent.com/91870042/145202345-9b5040d0-4de3-4961-a515-0b98fb06f0ed.png){: .align-center width="70%"}

위의 그림에서 *Data, Model, Loss* 는 아래와 같이 수식으로 나타낼 수 있다.
- Data: $$D=\{(x_i, y_i)\}^N_{i=1}$$
- Model: $$\hat{y}=wx+b$$
- Loss: $$\text{loss}=\frac{1}{N}\sum_{i=1}^{N}(y_i-\hat{y_i})^2$$

찾고자 하는값은 $$w, b$$인데 이 값은  `loss function`을 \\(w, b\\)에 대해서 각각 미분하여 나온 결과값을 이용한다. 이전에 배웠던 `경사하강법`을 이용해 값을 업데이트해 나가면서 가장 잘 예측하는 값을 찾는 것이다.

사용할 전략은 자동미분을 기반으로 한 역전파(backpropagation)를 진행할 것이다. 우리는 loss function값을 줄이는 것이 목적이기 때문에 loss function을 각 parameter에 대해서 편미분을 진행한다.

> 먼저, *loss function* 을 \\(w\\)에 대해 미분한 값에 대해서 보자.

\begin{aligned}
  \frac{\partial \text{loss}}{\partial w} &= \frac{\partial}{\partial w} \frac{1}{N} \sum_{i=1}^{N}(y_i-\hat{y_i})^2\\\\\\
  &= \frac{\partial}{\partial w} \frac{1}{N} \sum_{i=1}^{N}(y_i-wx_i-b)^2\\\\\\
  &= -\frac{1}{N} \sum_{i=1}^{N}-2(y_i-wx_i-b)x_i
\end{aligned}

> 그 다음, *loss function* 을 \\(b\\)에 대해 미분한 값에 대해서 보자.

\begin{aligned}
  \frac{\partial \text{loss}}{\partial b} &= \frac{\partial}{\partial b} \frac{1}{N} \sum_{i=1}^{N}(y_i-\hat{y_i})^2\\\\\\
  &= \frac{\partial}{\partial b} \frac{1}{N} \sum_{i=1}^{N}(y_i-wx_i-b)^2\\\\\\
  &= -\frac{1}{N} \sum_{i=1}^{N}-2(y_i-wx_i-b)
\end{aligned}

위에서 나온 식을 이용해서 \\(w, b\\)의 값을 이용해 특정 step_size(\\(\eta\\)) 만큼을 곱한다음에 빼주는 방식으로 업데이트를 진행한다. \\(\eta\\) 의 설정도 중요한데 이 값이 너무 커지면 학습이 되지 않는다. 또 반대로 너무 적으면 학습이 이루어지지 않는다. `Adaptive Learning Rate`를 사용하면 해당 \\(\eta\\) 값을 자동으로 바꿔가면서 학습을 진행한다.

$$ w \leftarrow w - \eta \frac{\partial \text{loss}}{\partial w} $$

$$ b \leftarrow b - \eta \frac{\partial \text{loss}}{\partial b} $$

## Multi Dimension Neural Networks

하지만, 세상이 방금과 같은 예시처럼 선형으로만 이루어져 있거나, 1차원 입력에서 1차원 출력으로 나오는 경우는 많지 않다. M차원에서 N차원으로 가는 모델을 찾고 싶을 때는 행렬을 사용해야 한다.

![image](https://user-images.githubusercontent.com/91870042/145207505-0478b9ca-a1d5-49f8-98b9-f6b87d537219.png){: .align-center width="70%"}

앞에서의 \\(w, b\\)는 값이었던 반면, 이번에는 \\(W\\)는 행렬을, \\(b\\)는 벡터를 의미한다.

$$ y = W^Tx + b $$

이렇게 곱해지는 행렬(\\(W\\))을 해석하는 하나의 방법은, **두 개의 벡터 공간의 변환**이라고 볼 수 있다.

## Beyond Linear Neural Networks

![image](https://user-images.githubusercontent.com/91870042/145208883-2f6ecfa6-6e05-46e1-8948-73eb26548c90.png){: .align-center width="70%"}

위 사진 처럼, 딥러닝으로 Neural Network를 여러겹 쌓는다고 해보자. Neural Network를 여러개 쌓기 위해서는 하나의 Network가 가중치 행렬 (\\(W_{1}^{T}x\\))을 통해 나오는 결과 Hidden Vector(\\(h\\))를 다시 가중치 행렬에 넣어 결과를 얻어내는 형식으로 사용한다. 이를 수식으로 표현하면 다음과 같다.

$$ y = W_{2}^{T}h = W_{2}^{T}W_{1}^{T}x $$

사실, 위의 수식은 처음의 입력에 대해서 가중치 행렬을 2번 연산한 결과와 동일한데, 이는 1개 layer의 Network Layer와 동일하다. 그렇기 때문에 이 값을 증폭시켜줄 `비선형함수`가 필요한데 우리는 그 함수를 `활성함수(Activation Function)`이라고 부른다. 따라서 활성함수를 \\(\rho\\) 라고 하면 다시 다음과 같아 나타낼 수 있다.

$$ y = W_{2}^{T}h = W_{2}^{T}\rho(W_{1}^{T}x) $$

## 활성 함수
위에서 말한 활성함수의 종류에는 `ReLU`, `Sigmoid`, `Hyperbolic Tangent`함수가 있다. 어떤게 좋은지는 **문제와 상황마다 모두 다르다**. 하지만, Neural Network를 여러개 쌓았을 때, 의미있는 결과를 얻어내기 위해서는 비선형 함수를 사용해야만 한다.

![image](https://user-images.githubusercontent.com/91870042/145209921-f2d2a57f-1ece-4b2f-b793-3b2f057be4cf.png){: .align-center width="70%"}

## Multi-layer Perceptron
지금까지 알아본 이론을 이용해서 Nerual Network를 2층이 아닌 훨씬 더 깊게 설정할 수 있다. 예를 들어, 3층으로 Neural Network를 구성한다고 하면 다음과 같은 수식이 나온다.

![image](https://user-images.githubusercontent.com/91870042/145210135-0df9dd58-6721-4c62-a9b9-cba0c29a3c6c.png){: .align-center width="70%"}

## 손실 함수
loss function을 선택을 해야하는데, 흔히 다음 3가지 문제에 대해서는 각각 `MSE`, `CE`, `MLE` 손실함수를 사용한다. 실제로 손실 함수를 선택할 때, 이 함수가 왜 잘 평가할 수 있는지도 같이 설명을 해야한다.

1. Regression Task  
   회귀문제에서는 예측한 그래프와 실제값의 차이를 제곱하여 더한 **MSE**방법을 사용한다. 때에 따라서 $$L_1$$노름을 적용한 손실함수를 사용할 수 있다.

   $$ \text{MSE} = \frac{1}{N} \sum_{i=1}^{N} \sum_{d=1}^{D}(y_i^{(d)} - \hat{y_i}^{(d)})^2 $$

2. Classification Task  
   CE(Cross Entropy)를 사용하면 오차에 정확도가 고려되어서 최적화 관점에서 어떤 넷이 더 잘 학습되었는지 알 수 있다. 정답라벨이 원핫벡터이면서, 정답만 1이어서 log연산자가 그 역할을 수행한다.

   $$ \text{CE} = -\frac{1}{N} \sum_{i=1}^{N} \sum_{d=1}^{D}y_i^{(d)}\text{log}\hat{y_i}^{(d)} $$

3. Probabilistic Task

  $$ \text{MLE} = \frac{1}{N} \sum_{i=1}^{N} \sum_{d=1}^{D} \text{log} N(y_i^{(d)}; \hat{y_i}^{(d)}, 1) $$

<br>

# References

[📘 분류 오차에 Cross entropy를 사용하는 이유](http://funmv2013.blogspot.com/2017/01/cross-entropy.html)

-->