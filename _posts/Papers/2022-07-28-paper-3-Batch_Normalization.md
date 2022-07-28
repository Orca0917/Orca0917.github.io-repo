---
title: "[논문 리뷰] Batch Normalization"
excerpt: "Batch Normalization: Accelerating Deep Network Training by Reducing Internal Covariate Shift"

categories:
  - paper
tags:
  - [AI, Papers]
toc: true
toc_sticky: true
use_math: true

date: 2022-07-28
last_modified_at: 2022-07-28
---

## Abstract

논문의 시작은 학습을 진행할 수록, 각 레이어가 수정되면서 입력에 대한 분포가 바뀌는 것을 지적하면서 시작합니다. 이런 입력 데이터의 분포 변화가 제대로 학습하는 것을 방해하고 매번 레이어가 새로운 입력분포에 적응해야하기 때문에 속도가 느리다는 것을 밝힙니다.

저자는 이런 분포에 대한 변화를 "Internal Covariate Shift" 라고 부르며 이 문제를 해결하기 위한 정규화 방법에 대해서 소개합니다. 여기서 소개하는 정규화 방법은 Batch Normalization이라 하며 뒤에서는 줄여서 BN 으로 종종 표시되고 있습니다.

Batch Normalization을 진행하면 더 큰 학습률(learning rate)의 선언이 가능하고, 파라미터의 초기화를 하는데 덜 주의를 기울여도 되며 Dropout과 같은 효과를 만들어주기도 해서 굳이 따로 Dropout을 사용하지 않아도 된다고 합니다. 저자는 실제로 기존의 이미지 분류에 사용된 SOTA 모델을 학습시키는데 14번 더 적은 학습횟수에도 비슷하거나 더 높은 성능을 보였다고 밝혔습니다.

<br>

## Introduction

딥러닝이 다양한 분야에서 두곽을 나타내며 딥러닝을 최적화 시키고 개선시키기 위한 다양한 노력이 등장했습니다. 예를 들어, Stochastic Gradient Descent, Adagrad, momentum을 적용한 경사하강법 등이 있습니다. SGD는 파라미터 $\Theta$ 를 최적화하며 loss 값을 최소로 만들도록 학습합니다.

$$
\Theta = \underset{\Theta}{\text{argmin}} \frac{1}{N} \sum_{i=1}^{N} \frac{\partial \ell (x_i, \Theta)}{\partial \Theta}
$$

- $\Theta$ : 학습 파라미터
- $N$ : 데이터의 수
- $\ell$ : loss
- $x_i$ : $i$ 번쩨 데이터

여기서 SGD는 하나의 데이터만 사용하는 것이 아니라 보통 여러개의 단위를 묶어 배치단위로 학습을 진행합니다. 예로, $m$ 개의 데이터를 묶어서 하나의 배치로 사용하고 입력 데이터 $x_i$ 가 $x_{1 \dots m}$ 을 나타낸다면 다음 식을 최소화 시키는 파라미터 $\Theta$ 를 찾는 것과 같습니다.

$$
\frac{1}{m} \frac{\partial \ell (x_i, \Theta)}{\partial \Theta}
$$

수식은 별것 없는게 사실 처음 등장한 수식을 배치 단위로 바꾸어 준 것이 전부입니다. 이번에는 배치단위를 사용하는 것의 장점에 대해서 논문은 다음 2가지를 소개합니다.

1. 미니배치를 사용한 loss의 그래디언트는 전체 학습데이터에 대한 그래디언트를 추정할 수 있으며, 배치의 크기가 커질수록 결과물이 좋아집니다.

2. 현재 컴퓨터는 병렬처리가 가능하기 때문에, 하나의 연산을 $m$ 번 반복하는 것 보다 $m$ 개의 데이터를 한 번에 처리하는 것이 속도측면서에서 이점이 있습니다.

이런 SGD에도 조심해야할 점은 있습니다. 대표적으로 학습할 때 필요한 하이퍼 파라미터인 `학습률`과 `파라미터 초기화`가 적절하게 설정되어야 합니다. 또한 각 레이어의 입력은 이전 레이어들의 파라미터에 의해 영향을 많이 받습니다. 바로 이 부분을 저자들은 지적합니다.

신경망 구조에 의해서 입력 데이터의 변화가 생기는 것을 `covariate shift` 라고 하며, 기존에는 이 문제를 `domain adaptation`을 통해서 해결하였습니다. 하지만 이런 분포의 변화는 신경망 구조 전체에 영향을 주게 되며 각 부분에도 영향을 주게 되어 있습니다. 이 부분을 예를 들어 살펴봅시다.

<br>

$$
\ell = F_2(F_1(u, \Theta_1), \Theta_2)
$$

- $F_1, F_2$ : 임의의 변형 함수
- $\Theta_1, \Theta_2$ : $\ell$ 을 줄이기 위해 학습되는 파라미터

위의 신경망 구조에서 파라미터 $\Theta_2$ 를 학습하는 것은 이전 레이어의 출력인 $x = F_1 (u, \Theta_1)$ 을 입력으로 하는 하나의 신경망 부분과 같습니다. 이해를 위해 식을 단순화 하면 다음과 같습니다.

$$
\ell = F_2(x, \Theta_2)
$$

이제 현재 네트워크에서 존재하는 파라미터 $\Theta_2$ 를 학습시키기 위해 경사하강법을 적용시켜보겠습니다.

$$
\Theta_2 \leftarrow \Theta_2 - \frac{\alpha}{m} \sum_{i=1}^{m} \frac{\partial F_2 (x_i, \Theta_2)}{\partial \Theta_2}
$$

- $m$ : 배치 크기
- $\alpha$ : learning rate(학습률)

이전 레이어의 출력이 그대로 다시 사용되기 때문에 논문은 입력과 출력 데이터 분포가 동일해야 파라미터들이 새로운 분포에 대해 다시 적응할 필요가 없어져 학습 효율이 더 높아진다고 설명합니다. 그리고 데이터 분포의 일관성은 네트워크 구조 내부 뿐만 아니라, 외부에 있는 활성함수에도 적용했을 때도 장점이 있음을 보여줍니다.

저자는 시그모이드 함수를 예로 들며, 네트워크의 출력값이 커질 수록, 시그모이드 함수에서 미분값은 0에 가까워지기 때문에 **기울기 소실**문제가 발생할 수 있다고 설명합니다. 하지만 이 문제는 현재 다른 활성함수은 ReLU를 도입하여 어느정도 해결하였습니다.

여기에 ReLU 입력 분포를 모두 통일시키면 높은 학습률과 파라미터 초기화에 너무 신경쓰지 않아도 훨씬 높은 성능의 모델 출력결과와 빠른 학습속도를 기대할 수 있다고 합니다.

본론으로, 저자는 입력과 출력분포를 통일시키기 위해 정규화 방법을 소개하며 데이터 분포에 대해 평균과 분산 값을 통일시킴으로서 높은 학습효과를 보이는 것을 설명합니다. 다음 파트 부터는 Batch Normalization에 대한 더 자세한 설명을 시작하겠습니다.

<br>

# Towards Reducing Internal Covariate Shift

저자는 Internal Covariate Shift를 신경망 구조가 학습함에 따라 분포가 변화하는 것이라고 정의합니다. 따라서 이 문제를 해결하기 위해 Normalize 기법을 도입할 것을 말하며, 이미 이전 논문에서 정규화가 이루어졌을 때의 장점이 증명되었음을 한 번 언급합니다.

그러면 이번에는 정규화를 어떻게 적용시킬지 고민해봐야 합니다. 매 학습 단계마다 또는 일정 시간을 기준으로 적용시켜볼 수 있는데 최적화 알고리즘의 파라미터를 수정하거나  네트워크 구조를 수정해서 적용시킬 수 있습니다.

하지만, 최적화 알고리즘의 파라미터를 수정했을 때 문제가 발생할 수 있습니다. 파라미터들이 정규화하는 과정 자체를 업데이트 하는 방식으로 파라미터를 수정할 수 있으며 이렇게 되면 학습의 성능을 크게 감소시키게 됩니다.

예를 들어 어떤 레이어의 입력이 $u$ 이며 bias가 $b$ 라고 가정하고 정규화를 진행해보겠습니다.

$$
\hat{x} = x - \mathbb{E}[x] \quad \text{where, } \quad x = u + b \quad \text{and} \quad \mathcal{X} = \{ x_{1 \dots N} \}\\
\mathbb{E}[x] = \frac{1}{N} \sum_{i=1}^{N} x_i
$$

여기서 만약, 경사강법에 $b$ 에 적용된 $\mathbb{E}[x]$ 의 의존성을 무시한다면 다음과 같이 업데이트 될 것입니다.

$$
b \leftarrow b + \Delta b \quad \text{where, } \; \Delta b \propto \frac{\partial \ell}{\partial \hat{x}}
$$

$$
u + (b + \Delta b) - \mathbb{E}[u + (b + \Delta b)] = u + b - \mathbb{E}[u + b]
$$

위 식에 따라, $b$ 의 업데이트와 Normalization의 조합은 파라미터중 하나인 bias가 제거되어 레이어의 출력에 어떠한 영향도 주지 못했으며, 결과적으로 손실값에도 변화를 주지 못했습니다. 현재 나타나는 문제를 해결하기 위해서는 네트워크의 출력이 항상 특정한 분포를 따르도록 만들어야만 합니다.

이렇게 되면 모델 파라미터에 대한 손실함수의 미분값이 정규화와 $\Theta$ 에 대한 의존성을 설명할 수 있습니다. 그렇다면 이번에는 레이어의 입력이 $x$ 이고 전체 데이터가 $\mathcal{X}$ 라고 할 때의 정규화를 다음과 같이 정의해보겠습니다.

$$
\hat{x} = \text{Norm}(x, \mathcal{X})
$$

현재 입력에 대해서만 정규화하는 것이 아니라, 전체데이터를 통해서 정규화를 진행하는 것입니다. 이렇게 함으로써 정규화가 현재의 입력에만 의존성을 갖게하는 것이 아니라, 전체데이터에 의존성을 갖게 만들 수 있습니다.

역전파를 진행할 때는 야코비 행렬식(Jacobians)을 계산해야합니다. 이렇게 되면 각 레이어를 정규화하는데 소모되는 계산이 너무 복잡해져 시간이 오래걸릴 수도 있습니다. 따라서 저자는 더 나은 정규화 방법을 찾기로 했습니다.

<br>

## Normalization via Mini-Batch Statistics

모든 레이어의 입력을 정규화하는 것은 계산이 너무 많고, 모든 구간에서 미분이 불가능할 수 있습니다. 따라서 2개의 필요한 단순화 과정을 거치도록 만들었습니다.

첫 번째는 **레이어 전체를 정규화하는 것이 아닌, 각 스칼라 값에 대해 정규화를 진행**하는 것입니다. 정규화를 할 때는 평균이 0이고 분산이 1인 값을 띄도록 만듭니다. 예를 들어, 어떤 레이어에 $d$ 차원의 입력이 들어온다고 하면 다음과 같이 정규화 합니다.

$$
\hat{x}^{(k)} = \frac{x^{(k)} - \mathbb{E}[x^{(k)}]}{\sqrt{\text{Var}[x^{(k)}]}} \qquad x = (x^{(1)} \dots x^{(d)})
$$

현재의 정규화 방식을 따르게 되면 *LeCun et al.* 의 논문에 의하면 원하는 값에 수렴하기까지 빠른 속도로 진행할 수 있다고 합니다. 심지어 이 방법은 속성들 간의 상관관계가 존재하지 않아도 가능합니다.

하지만, 이렇게 정규화를 진행하는 것은 분명히 좋지만 반대로 문제점도 있습니다. 바로 레이어가 표현할 수 있는 구간을 바꾸는 문제점이 있습니다. 예를 들어, 시그모이드 함수를 생각해봅시다. 시그모이드는 비선형 함수임에도, 특정 구간에서는 성형성을 띄기 때문에 정규화에 의해 선형성을 띄게 만들어질 수 있습니다.

이 문제를 해결하기 위해 저자는 2개의 파라미터 $\gamma$ 와 $\beta$ 를 도입하여 정규화된 분포에 크기를 변형시키거나 이동을 하도록 만들었습니다.

$$
y^{(k)} = \gamma^{(k)}\hat{x}^{(k)} + \beta^{(k)}
$$

참고로 여기서 만약에, $\gamma^{(k)}$ 값을 $\sqrt{\text{Var}[x^{(k)}]}$ 로, $\beta^{(k)}$ 를 $\mathbb{E}[x^{(k)}]$ 로 설정한다면, 원래의 값으로 되돌리는 연산이 됩니다. (위의 수식에 그대로 대입) 실제 학습에서는 배치 단위로 SGD를 진행하기 때문에 다음과 같이 수식을 작성해봅시다.

$$
\mathcal{B} = \{ x_{1 \dots m}^{(k)} \}
$$

그리고 위의 미니 배치가 정규화 된 값을 $\hat{x}_{1 \dots m}^{(k)}$ 라고 정의합시다. 이제 마지막으로 $\gamma$ 와 $\beta$ 를 사용하여 `scale` 과 `shift` 과정을 거치는 것을 아래와 같이 표현합니다.

$$
\text{BN}_{\gamma, \beta} : x_{1 \dots m}^{(k)} \rightarrow y_{1 \dots m}^{(k)}
$$

이렇게 해서 정규화와 scaling, shifting 과정을 거친 모든 연산을 수식으로 살펴보았습니다. 한번 더 미니배치에 대한 전체적인 정규화 과정(알고리즘)을 논문에서는 다시 정리해서 보여줍니다.

$$
\begin{aligned}
\mu_\mathcal{B} &\leftarrow \frac{1}{m} \sum_{i=1}^{m}x_i \\
\sigma_\mathcal{B}^2 &\leftarrow \frac{1}{m} \sum_{i=1}^{m}(x_i - \mu_\mathcal{B})^2\\
\hat{x}_i &\leftarrow \frac{x_i - \mu_\mathcal{B}}{\sqrt{\sigma_\mathcal{B}^2 + \epsilon}}\\
y_i & \leftarrow \gamma \hat{x}_i + \beta \equiv \text{BN}_{\gamma, \beta}(x_i)
\end{aligned}
$$

위의 알고리즘을 전체적으로 보았을 때, 어떤 입력을 정규화시켜주는 과정에 파라미터인 $\gamma$ 와 $\beta$ 도 있는 선형변환이라고 볼 수 있습니다. 이를 새로운 신경망의 서브 네트워크라고 볼 수도 있습니다.

이제는 이들이 실제 오차역전파 과정에서 chain rule에 의해 어떻게 미분이 되는지 살펴보고자 합니다. PyTorch와 같은 딥러닝 프레임워크는 이를 자동으로 계산해주므로 따로 기억할 필요는 없습니다. 하지만, 그 미분과정에 대해 조금더 알아보기 위해 한 번 논문에 있는 내용을 가져오겠습니다.

$$
\begin{aligned}
\frac{\partial \ell}{\partial \hat{x}_i} &= \frac{\partial \ell}{\partial y_i} \cdot \gamma\\

\frac{\partial \ell}{\partial \sigma_\mathcal{B}^2} &= \sum_{i=1}^{m}\frac{\partial \ell}{\partial \hat{x}_i} \cdot (x_i - \mu_\mathcal{B}) \cdot -\frac{1}{2}(\sigma_\mathcal{B}^2 + \epsilon)^{-3/2}\\


\frac{\partial \ell}{\partial \mu_\mathcal{B}} &= \left( \sum_{i=1}^{m}\frac{\partial \ell}{\partial \hat{x}_i} \cdot \frac{-1}{\sqrt{\sigma_\mathcal{B}^2 + \epsilon}} \right) + \frac{\partial \ell}{\partial \sigma_\mathcal{B}^2} \cdot \frac{\sum_{i=1}^m -2(x_i - \mu_\mathcal{B})}{m}\\

\frac{\partial \ell}{\partial x_i} &= \frac{\partial \ell}{\partial \hat{x}_i} \cdot \frac{1}{\sqrt{\sigma_\mathcal{B}^2 + \epsilon}} + \frac{\partial \ell}{\partial \sigma_\mathcal{B}^2} \cdot \frac{2(x_i - \mu_\mathcal{B})}{m} + \frac{\partial \ell}{\partial \mu_\mathcal{B}} \cdot \frac{1}{m}\\

\frac{\partial \ell}{\partial \gamma} &= \sum_{i=1}^{m}\frac{\partial \ell}{\partial y_i} \cdot \hat{x}_i\\

\frac{\partial \ell}{\partial \beta} &= \sum_{i=1}^{m}\frac{\partial \ell}{\partial y_i}

\end{aligned}
$$

위의 식에서 보았듯이 Batch Normalization(BN) 변환은 미분이 가능하기 때문에, 정규화된 값을 레이어에 전달하여 사용된 파라미터들을 학습하는 것이 가능합니다. 이는 학습함에 따라 internal covariate shift 가 적게 발생하고 학습속도를 늘리는 것이 가능함을 말합니다. 다음은 학습과 추론 단계에서 과연 이 Batch Normalization이 어떻게 작용되는지 살펴보겠습니다.

<br>

### Training & Inference with BN Networks

위에서는 훈련과정에서 사용되는 학습 데이터셋을 미니배치로 뽑아서 정규화를 거쳐 입력으로 넣는 과정에 대해서 설명했습니다. 그렇다면 과연 추론과정에서도 정규화 과정이 필요할까요? 결론부터 말하자면, 조금 다른 정규화 과정을 거쳐야 합니다.

테스트 데이터셋을 사용하여 정규화를 진행하게 되면, 기존의 정규화를 진행하면서 추정한 평균값과 분산값의 의미가 사라집니다. 그렇기 때문에 학습 단계에서 추론한 표본평균과 표본분산값을 사용하여 모평균과 모분산을 계산해야 합니다.그리고 이 모평균과 모분산을 사용하여 테스트 데이터셋에 대해 정규화를 진행하여 입력으로 넣으면 올바른 계산값을 구할 수 있습니다.

$$
\hat{x} = \frac{x - \mathbb{E}[x]}{\sqrt{\text{Var}[x] + \epsilon}}
$$

여기서 사용되는 평균과 분산을 계산하는 방법은 아래와 같습니다.

$$
\mathbb{E}[x] = \mathbb{E}[\mu_\mathcal{B}]\\
$$

$$
\text{Var}[x] = \frac{m}{m - 1} \cdot \mathbb{E}_\mathcal{B}[\sigma_\mathcal{B}^2]
$$

추론과정에서 평균과 분산값은 고정되어 있으므로 정규화 과정이 단순한 선형변환으로 생각할 수 있습니다. 또한 여기에 $\gamma$ 와 $\beta$ 값을 사용해서 scaling과 shifting 효과를 줄 수 있습니다.

저자들은 다음으로 하나의 Convolution Network 를 예로 들면서 배치 정규화 과정을 다시 한 번 설명합니다. 하지만, 위와 중복된 내용이기 때문에 다시 언급하지는 않고 넘어가겠습니다.

<br>

### BN enables higher learning rates

일반적으로 딥러닝에서 learning rate가 높게 설정되면, 그래디언트 값이 너무 커지거나 소멸되는 문제가 생기며 local minima에 빠지는 문제가 생겼었습니다. BN을 사용하면 이 문제를 해결할 수 있습니다.

BN은 작은 파라미터의 변화가 레이어를 거치면서 더 큰 변화를 불러오는 것을 방지합니다. 주로 파라미터의 scale이 큰 변화로 불러일으키는 원인이었고, 배치 정규화는 이를 해결함으로서 학습률을 높게 설정할 수 있도록 하였습니다.

<br>

### BN regularized model

배치 정규화는 모델을 일반화시키는 역할을 할 수 있습니다. 저번에 한 번 Dropout에 대해 다룬 적이 있는데 배치 정규화는 학습할 때 사용되는 평균과 분산값이 매번 달라지는 성질을 이용해서 비슷한 효과를 줄 수 있습니다. 논문에서는 Dropout을 대체하여 사용할 수 있다고는 하지만, 실제 대부분은 둘다 적용해서 사용하는 경우가 많습니다.