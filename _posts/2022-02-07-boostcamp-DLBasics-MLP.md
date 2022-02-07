---
title:  "[부스트캠프 Pre-Course] 딥러닝 기초: 뉴럴 네트워크-MLP"
excerpt: "신경망의 정의와 Deep Neural Networks"

categories:
  - DLBasic
tags:
  - [AI, Naver, BoostCamp, Python, Math]
toc: true
toc_sticky: true
 
date: 2022-02-07 01:00:00
last_modified_at: 2022-02-07 01:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

이번에는 딥러닝의 고전적인 얘기인, **Multi Layer Perceptron, MLP**에 대해서 살펴보자

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