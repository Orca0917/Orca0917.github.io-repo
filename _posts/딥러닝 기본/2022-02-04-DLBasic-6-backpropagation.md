---
title:  "오차역전파 (Back Propagation)"
excerpt: "신경망이 학습할 수 있는 방법인 오차역전파법의 개념과 원리 그리고 전체적인 동작과정을 살펴봅니다."

categories:
  - DLBasic
tags:
  - [AI, Naver, BoostCamp, Math]
toc: true
toc_sticky: true
 
date: 2022-02-07 05:00:00
last_modified_at: 2022-06-18 00:00:00
---

![image](https://user-images.githubusercontent.com/91870042/174443439-4691d9dc-c430-4318-9166-fdea3ac702c1.png){: .align-center width="100%"}

<br>

# 오차역전파

퍼셉트론의 개념부터 시작해서 신경망의 구조 그리고 여기에 필요한 활성함수와 신경망을 평가하는 손실함수에 대해서 알아보았습니다. 이제 손실함수로 구한 오차와 딥러닝 학습방법론인 경사하강법을 결합하여 신경망을 학습하는 `오차역전파(backpropagation)` 에 대해서 살펴봅시다!

## 🧪 순전파

오차 역전파의 역전파는 순전파를 거꾸로 한 것을 의미합니다. 그렇다면 신경망에서 순전파는 무엇을 의미하는 것일까요? 순전파는 퍼셉트론 포스팅에서 언급을 하지는 않았지만 이미 다뤘던 적이 있습니다.

![image](https://user-images.githubusercontent.com/91870042/174476268-e42ab307-7d64-4329-9a63-6ff9e7aa5adc.png){: .align-center width="60%"}

$$
\begin{aligned}
  \mathbf{z} &= \mathbf{W}^{(1)}\mathbf{x} + \mathbf{b}^{(1)}\\
  \mathbf{h} &= \sigma (\mathbf{z})\\
  \mathbf{o} &= \mathbf{W}^{(2)}\mathbf{h} + \mathbf{b}^{(2)}\\
\end{aligned}
$$

- $\mathbf{W}^{(k)}$ : $k$ 번째 레이어의 가중치 행렬
- $\mathbf{b}^{(k)}$ : $k$ 번째 레이어의 편향 (bias)

그림에서 빨간색 선으로 진행되는 과정이 `순전파`라고 할 수 있습니다. 입력값에 대해 파라미터인 가중치 $\mathbf{W}^{(1)}, \mathbf{W}^{(2)}$ 가 곱해지고, 활성함수를 거쳐 출력으로 보내는 과정이 순전파입니다. 손실함수는 이 출력값을 사용하여 오차를 계산합니다.

## 🧫 역전파

역전파는 순전파의 반대입니다. 거꾸로 계산된 오차를 출력층, 활성함수층, 순으로 거쳐서 각 파라미터들과 곱해지는 연산과정도 거꾸로 거쳐갑니다. 이 과정에서 각 파라미터는 정답을 잘 맞추도록 학습이 이루어집니다.

이전 게시글에서 손실함수의 값이 줄어드는 방향으로 파라미터를 업데이트 해나가는 경사하강법에 대해서 알아보았었습니다. 경사하강법은 손실함수를 업데이트 하고자하는 파라미터에 대해서 미분하여 그 기울기를 따라서 내려가는 방법론을 말합니다. 따라서 신경망에서도 경사하강법의 원리를 적용시켜 미분을 진행합니다. 예를 들어, 1번째 층의 파라미터에 대해 미분을 하는 과정은 아래와 같이 나타낼 수 있습니다.

$$
\frac{\partial \text{Loss}}{\partial \mathbf{W^{(1)}}}
$$

- $\text{Loss}$ : 손실함수

오차역전파는 단순히 이 편미분을 사용하지 않고, 합성함수 미분법인 `연쇄법칙(Chain Rule)`기반의 자동미분을 사용합니다. 일단 먼저 간단한 예시를 들어서 이해를 해봅시다!

$$
z=(x+y)^2
$$

위의 식을 $x$에 대해서 편미분하는 과정을 미분의 연쇄법칙을 사용하여 나타내면 아래와 같습니다.

$$
t = x+y\\
z = t^2\\
\frac{\partial z}{\partial x} = \frac{\partial z}{\partial w} \frac{\partial w}{\partial x} = 2w \times 1 = 2(x+y)
$$

이 예제와 마찬가지로, 미분 연쇄법칙을 사용하면 최종 오류에 대한 1번째 레이어의 가중치행렬의 미분식은 아래처럼 풀어서 나타낼 수 있습니다.

$$
\nabla_{\mathbf{W^{(1)}}}\mathcal{L} = (\nabla_{\mathbf{W^{(1)}}}\mathbf{z})(\nabla_\mathbf{z}\mathbf{h})(\nabla_\mathbf{h}\mathbf{O})(\nabla_\mathbf{O}\mathcal{L})
$$

$$
\frac{\partial\mathcal{L}}{\partial\mathbf{W}^{(1)}} = \frac{\partial\mathcal{L}}{\partial\mathbf{O}}\frac{\partial\mathbf{O}}{\partial\mathbf{h}}\frac{\partial\mathbf{h}}{\partial\mathbf{z}}\frac{\partial\mathbf{z}}{\partial\mathbf{W}^{(1)}}
$$

- $\mathcal{L}$ : 손실함수 (loss function)



오차역전파는 이런 연쇄법칙을 사용하여 각 파라미터에 알맞은 오차를 전달하는 것이 가능하며, Python의 딥러닝 프레임워크 중 하나인 PyTorch에서는 이 오차역전파 과정을 계산그래프로 표현하여 `자동미분`의 성질을 그대로 가져갔습니다. 오차역전파에 또 다른 성질로는 각 파라미터에 맞는 오차값을 가져간다는 것입니다. 더 많이 잘 못 예측한 파라미터에 대해서는 더 크게 수정을 하도록 하며, 이미 잘 설정되어 있는 파라미터는 덜 수정이 되도록 만들어줍니다.

