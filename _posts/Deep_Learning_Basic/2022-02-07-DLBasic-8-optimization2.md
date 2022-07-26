---
title:  "Optimization PART 2 : 학습에서 사용되는 Optimizer"
excerpt: "머신러닝을 학습할 때 사용되는 Optimizer인 Momentum, Adam, Adadelta 등 주요 Optimizer에 대해서 소개합니다"

categories:
  - DLBasic
tags:
  - [AI, BoostCamp]
toc: true
toc_sticky: true
 
date: 2022-02-08
last_modified_at: 2022-07-25
---

![image](https://user-images.githubusercontent.com/91870042/180658653-9df2033f-5e9e-4178-8283-2ad17f08c4c7.png){: .align-center width="100%"}

# Introduction

Optimizer의 2번째 파트입니다. 만약 첫 번째 Optimizer파트에 대해서 아직 보지 못했다면 [여기](https://killerwhale0917.github.io/dlbasic/DLBasic-7-optimization1/)서 읽고 진행하는 것을 추천드립니다. 2번째 파트에서는 딥러닝의 핵심이 될 수 있는 옵티마이저들에 대해 소개합니다. 옵티마이저의 기본인 SGD 에서 옵티마이저를 잘 모른다면 기본 이상은 하니 사용하는 Adam 까지 그 원리와 수식을 한 번 이해해 보겠습니다.

<br>

# 경사하강법을 이용한 방법

이전까지 딥러닝의 모델을 학습시킬 때, 경사하강법을 사용하여 학습시킨다고 했었습니다. 이 때 학습시간을 줄이고 학습효과는 크게 하기 위해 여러가지 학습방법들이 고안되었습니다. 먼저 큰 틀에서 보았을 때 학습방법은 3가지로 분류해볼 수 있습니다.

첫 번째 방법은 `SGD`(Stochastic Gradient Descent) 입니다. 학습을 하면서 가중치 파라미터를 업데이트 할 때 한 번에 모든 데이터를 사용하는 것이 아니라 랜덤 샘플링을 통해 얻은 하나의 데이터를 사용하여 학습하는 방법입니다. 확률적으로 이렇게 랜덤샘플링을 통해 학습을 진행하면 최적해에 근사할 수 있음이 증명되어 있습니다.

두 번째 방법은 `Mini-batch` Gradient Descent 입니다. 위에서는 하나의 데이터만 사용해서 업데이트 했다면, 이번에는 여러개를 사용하여 학습합니다. 여러 개의 단위를 우리는 `배치`라고 정의하며 배치의 크기에 따라 학습의 성능에도 큰 영향을 주게 됩니다. 

마지막 방법은 모든 학습데이터를 사용하여 업데이트 하는 방법입니다. 앞서 말한 Mini-batch Gradient Descent에서는 배치의 크기를 결정하는 것이 중요합니다. 

<br>

## 배치를 설정하는 방법

배치사이즈를 크게 설정하는 것이 좋은지, 적게 설정하는 것이 좋은지 어떤 한 논문에서 발표한 자료가 존재합니다. 논문은 *On large-batch Training for Deep Learning, 2017* 으로 sharp minimizer와 flat minimizer에 대해서 소개합니다. 각각이 무엇인지 다음 그림을 통해서 살펴보겠습니다.

![image](https://user-images.githubusercontent.com/91870042/180816625-a8cfbf00-82df-4323-bbf3-f56be982d3c9.png){: .align-center width="70%"}

위의 그림에서 파란색 선이 학습으로 얻어낸 결과, 초록색 선이 실제 정답값을 의미합니다. Flat minimum의 경우 학습으로 얻은 결과와 실제 정답간의 차이가 크지 않습니다. 반면에 Sharp minimum의 경우 학습의 결과와 실제 결과값의 차이가 큰 것을 확인할 수 있습니다. 이것을 보아 우리는 flat minimum에 도달하는 것이 더 좋다는 것을 알 수 있습니다.

논문에서는 작은 크기의 배치크기를 사용할 수록 Flat minimum에 도달하기 쉽다고 발표합니다. 하지만 모든 상황에 대해서 이렇다고 단정짓기는 힘들기 때문에 여러 주어진 상황속에서 실험을 통해 가장 높은 효과를 내는 배치사이즈를 설정하는 것이 중요합니다.

<br>

# Optimizer

옵티마이저는 경사하강법을 통해서 얻어낸 그래디언트를 바탕으로 실제 파라미터를 업데이트 하는 과정을 최적화 시켜줍니다. 적은 시간에 더 효과적인 값의 업데이트를 이루어 내는 것이 중요하기 때문에 여러가지 옵티마이저가 존재합니다. 이번에는 그 중에서도 기본이 되는 것들과 많이 사용되는 옵티마이저에 대해서 소개하겠습니다.

<br>

## Gradient Descent

먼저 가장 일반적인 경사하강법을 이용한 파라미터의 업데이트 수식을 살펴봅시다. 가중치 행렬을 $\mathbf{W}$ 라고 했을 때, 업데이트 식은 다음과 같습니다.

$$
\mathbf{W}_{t+1} \leftarrow \mathbf{W}_t - \eta g_t
$$

- $\mathbf{W}$ : 학습 파라미터
- $\eta$ : 학습률
- $g_t$ : 그래디언트 값

![image](https://user-images.githubusercontent.com/91870042/180821355-d98f516e-3d42-4b9d-9708-8dcfad949d68.png){: .align-center width="50%"}

하지만 여기서 학습률인 $\eta$ 는 하이퍼파라미터로 사용자가 직접 설정을 통해 최적의 값으로 만들어줘야 합니다. 다음은 여기에 "관성"의 개념을 추가한 Momentum에 대해서 살펴보겠습니다.

<br>

## Momentum

Momentum은 이전의 Gradient들의 정보들을 사용해서 관성의 개념을 도입하였습니다. 이것의 장점은 현재 진행방향을 어느정도 유지시켜줄 수 있는데 딥러닝의 Global minimum 값을 찾는데 큰 도움이 됩니다. Local minimum에서 갖혀 빠져나오지 못하는 문제점을 해결해 줄 수 있어서 올바른 최적해를 찾을 수 있게 도와줍니다. Momentum을 사용한 경사하강법 수식은 다음과 같습니다.

$$
a_{t+1} \leftarrow \beta a_t + g_t\\

$$

- $a_t$ : timestep $t$ 의 관성 그래디언트 누적 값
- $\beta$ : 관성의 크기

![image](https://user-images.githubusercontent.com/91870042/180821629-d8da9cfb-63b9-4578-9bb8-a14548aa83cb.png){: .align-center width="50%"}

위의 그림에서 알 수 있듯이 단순한 경사하강법보다는 부드럽게 최적해를 찾아나가는 것을 볼 수 있는데, 바로 관성의 효과를 적용했기 때문입니다. 다음은 위의 식에서 관성을 유지한 채, 다음 업데이트 되어서 이동한 부분의 그래디언트를 계산하고 현재의 업데이트의 계산에 적용시키는 NAG 에 대해서 소개하겠습니다.

<br>

## NAG

NAG (Nesterov Accelerated Gradient)는 현재 그래디언트를 업데이트 할 때, 다음 업데이트 되는 그래디언트의 정보도 포함시키는 것을 말합니다. 수식으로 살펴봅시다.

$$
a_{t+1} \leftarrow \beta a_t + \nabla \mathcal{L}(\mathbf{W}_t-\eta\beta a_t)\\
\mathbf{W}_{t+1} \leftarrow \mathbf{W}_t - \eta a_{t+1}
$$

위의 식에서 나블라 $\nabla \mathcal{L}$ 가 포함된 부분이 바로 다음 지점의 그래디언트를 계산하는 부분입니다. 이렇게 되면 관성방향으로 움직이고 다시 움직인 자리의 그래디언트를 계산하기 때문에 최적의 지점을 더 빠르게 찾아 내려갈 수 있습니다.

![image](https://user-images.githubusercontent.com/91870042/180823899-1977441b-aa7e-4e9c-802f-d5bfcb3c1f1d.png){: .align-center width="80%"}

<br>

## Adagrad

Adagrad는 파라미터를 업데이트 할 때, 모두 동일하게 업데이트 하는 것이 아니라 많이 업데이트된 파라미터는 적게 업데이트하고, 적게 업데이트된 파라미터는 많이 업데이트해서 더 빠르게 최적 지점을 찾아낼 수 있습니다.

따라서 현재 파라미터가 지금까지 얼마나 업데이트 되었는지 저장하는 변수하나가 필요하며 수식에서는 이를 $G_t$라고 표현했습니다.

$$
\mathbf{W}_{t+1} = \mathbf{W}_t - \frac{\eta}{\sqrt{G_t + \epsilon}}g_t
$$

- $G_t$ : 지금까지 계산된 그래디언트를 제곱하여 모두 더한 값
- $g_t$ : 현재 계산된 그래디언트
- $\epsilon$ : 분모가 0이 되는 것을 방지하기 위한 매우 작은 값

![image](https://user-images.githubusercontent.com/91870042/180913863-6a488511-4f58-4d34-adc6-25206d8b9f01.png){: .align-center width="50%"}

이런 Adagrad에는 큰 단점이 존재합니다. 학습이 진행되면 진행될 수록 $G_t$ 의 값은 커질 수 밖에 없는데, 너무 커지게 되면 학습률에 해당하는 값이 0에 가까워져 더 이상 값의 업데이트가 원활하게 이루어지지 않습니다. 이 문제는 다음 소개할 RMSprop 에서 해결하였습니다.

<br>

## RMSprop

RMSprop은 논문으로 소개된 개념은 아닙니다. 제프리 힌튼의 강의 중에서 소개된 내용으로 지수이동평균(Exponential Moving Average, EMA)의 개념을 사용하였습니다. 지수 이동평균으로 $G_t$ 의 값이 매우 커지는 것을 방지할 수 있었는데, 바로 매번 새로운 그래디언트가 계산되고 더해질 때 1보다 작은 가중치를 설정하여 크기를 줄여나갔습니다.

$$
\begin{aligned}
G_t &= \gamma G_{t-1} + (1-\gamma)g_t^2\\
\mathbf{W}_{t+1} &= \mathbf{W}_t - \frac{\eta}{\sqrt{G_t + \epsilon}}g_t
\end{aligned}
$$

- $\gamma$ : 지수이동평균 가중치

<br>

## Adadelta

Adadelta는 학습률 파라미터인 $\eta$ 를 직접 설정하지 않아도 알아서 최적의 학습률을 계산하여 업데이트 합니다. 기본적인 틀은 Adagrad, RMSprop과 유사하지만 분자에 해당하는 식이 학습률 $\eta$ 대신 $H_t$ 라는 새로운 변수가 사용됩니다.

$$
\begin{aligned}
G_t &= \gamma G_{t-1} + (1-\gamma)g_t^2\\
H_t &= \gamma H_{t-1} + (1-\gamma)(\Delta \mathbf{W}_t)^2\\\\
\mathbf{W}_{t+1} &= \mathbf{W}_t - \frac{\sqrt{H_{t-1} + \epsilon}}{\sqrt{G_t + \epsilon}}g_t
\end{aligned}
$$

- $G_t$ : 그래디언트 제곱합의 지수이동평균(EMA)
- $H_t$ : 파라미터의 변화량 제곱합의 지수이동평균(EMA)

<br>

## Adam

Adam은 RMSprop과 Momentum 기법을 합친 옵티마이저로 파라미터마다 업데이트 되는 양이 다릅니다. Adam에서는 사용되는 하이퍼파라미터가 여러개 등장합니다. 첫번째로 학습률 $\eta$ 와 이동가중평균의 $\beta_1, \beta_2$ 가 있습니다.

$$
\begin{aligned}
m_t &= \beta_1 m_{t-1} + (1-\beta_1)g_t\\
v_t &= \beta_2 v_{t-1} + (1-\beta_2)g_t^2\\\\
\mathbf{W}_{t+1} &= \mathbf{W}_t - \frac{\eta}{\sqrt{v_t + \epsilon}}\frac{\sqrt{1-\beta_2^t}}{1-\beta_1^t}m_t
\end{aligned}
$$

Adam은 현재 진행방향에 관성을 부여하고, 최근까지의 경로의 변화량에 따라서 업데이트 정도를 조절하는 적응 학습률을 부여합니다. 따라서 Adaptive Moment Estimation 이라고 불리며 줄여서 Adam이 된 것입니다.

여러 신경망에서 잘 작동하기 때문에 가장 흔하게 사용되고 있으며 저자가 추천하는 하이퍼 파라미터 값은 다음과 같습니다.

- $\beta_1$ = 0.9
- $\beta_2$ = 0.999
- $\epsilon$ = 1e-8

<br>

지금까지 딥러닝에 사용되는 옵티마이저에 대해서 알아보았습니다. 여러 옵티마이저들이 어떤 것들을 바탕으로 발전되었는지 한 눈에 볼 수 있게 정리한 계보가 있어서 마지막으로 소개하면서 마무리하겠습니다. 아래 그래프는 하용호님의 강의자료 중 일부입니다.

![image](https://user-images.githubusercontent.com/91870042/180918419-046e643e-0746-4930-94c0-5901d25d6141.png){: .align-center width="100%"}

<br>

# References

[🌏 자습해도 모르겠던 딥러닝, 머리속에 인스톨 시켜드립니다.](https://www.slideshare.net/yongho/ss-79607172/)

[🌏 [딥러닝] 옵티마이저(Optimizer)](https://velog.io/@freesky/Optimizer#optimizer-%EC%A2%85%EB%A5%98)

[🌏 딥러닝 튜토리얼 6-1강. SGD, 모멘텀, AdaGrad, Adam](https://koreanfoodie.me/178)

[🎨 Cover Image Source : Unsplash](https://unsplash.com/photos/L3d2KZJoiKk)


<!--

# Regularization
위에서 알아본 `overfitting`이 일어나지 않도록, 적절하게 학습을 하게하는 역할을 한다.

## Early stopping
학습 데이터를 이용해 학습을 하다가 일정 시점이 되면 학습을 멈추는 것이다. validate data를 통해 모델 성능을 평가하고, 그 차이가 증가하는 경향이 있다면, 학습을 종료시킨다.

![image](https://user-images.githubusercontent.com/91870042/145232468-ba86e053-3748-4ede-bf0d-7b74c31a0a10.png){: .align-center width="70%"}

**Early Stopping**을 하기 위해서는 추가적인 validation data가 필요하다. 이를 생성하기 위해서 cross-validation기법이나, k-fold validation기법을 사용해볼 수 있다.

## parameter norm penalty
parameter가 너무 커지지 않도록 만드는 것으로 Neural Network의 가중치행렬 \\(W\\)를 작게 만드는것이다. 학습과정에서 큰 가중치에 대해서는 그에 상응하는 큰 페널티를 부과하여 `overfitting`을 억제한다.

\\(L_2\\) 파라미터는 ridge회귀분석 또는 tikhonov 정규화로도 알려져 있다.

$$ \text{total cost} = \text{loss}(D; W) + \frac{\alpha}{2}\|W\|_2^2 $$

## data augmentation
데이터가 무한히 많으면, 왠만하면 잘된다. 어떤식으로든 데이터를 이용해서 추가적인 데이터를 생성하는 것이다. 라벨이 변환되지 않는 한에서 추가적으로 데이터를 생산하는 것이 좋다.

![image](https://user-images.githubusercontent.com/91870042/145233009-3bb3b08e-39e1-4cf5-b5ba-549b85b8ff30.png){: .align-center width="70%"}

`MNIST 데이터`를 예로 들자면, 4와 같은 숫자를 뒤집거나 살짝 회전시킨 학습데이터를 추가할 수 있지만, 6의 경우 뒤집으면 9가 되기 때문에 라벨이 바뀌어버린다. 따라서 이런 경우 뒤집는 행위는 할 수 없다.

## Noise Robustness
입력과 weight에 노이즈를 섞어서 테스트를 더 강하게 한다. 최종적으로 이상치나 noise가 섞여 있어도 좋은 결과를 낼 수 있게 만드는 것이다.

![image](https://user-images.githubusercontent.com/91870042/145233233-8b107b17-b729-4b21-8fbe-ae0b2c06bf26.png){: .align-center width="70%"}


## label smoothing
학습데이터들 중 2개를 뽑아서 두 데이터를 섞는 것이다. 섞는 방식은 아래와 같은 방법들이 있다.

![image](https://user-images.githubusercontent.com/91870042/145233354-3e8e56e1-9b58-4fa6-b431-c8358d628dad.png){: .align-center width="70%"}

위의 방법을 들인 노력대비 성능이 크게 향상될 수 있어서 알아두는 것이 좋다.

## drop out
`drop out`은 neural network의 일부 레이어를 사용하지 않으면서 학습을 진행하는 것이다. 이를 통해 매번 다른 노드가 학습되기 때문에 전체 가중치가 overfitting되는 것을 방지할 수 있다.

![image](https://user-images.githubusercontent.com/91870042/145233570-0636de00-8996-4cad-baf6-f5caba23bc42.png){: .align-center width="70%"}

## batch normalization
배치 정규화는 평균과 분산을 조정하는 과정이 별도의 과정으로 떼어지지 않고 신경망 안에서 포함되어 학습되어 평균과 분산을 조정하는 방법이다. Neural Network의 각 레이어마다 정규화하는 레이어를 두어서 변형된 분포가 나오지 않도록 조절하는 방법이다.

![image](https://user-images.githubusercontent.com/91870042/145234280-75aea743-d742-4f76-af21-44161d47c5b3.png){: .align-center width="70%"}

<br>

# References

[📘 [Deep Learning] 배치 정규화](https://eehoeskrap.tistory.com/430)

[📘 인공신경망 - Parameter Norm Penalies](https://munjeongkang.github.io/ANN4/)

[📘 신경망에서 과적합을 방지하기 위한 방법](https://velog.io/@yuns_u/%EA%B3%BC%EC%A0%81%ED%95%A9%EC%9D%84-%EB%B0%A9%EC%A7%80%ED%95%98%EA%B8%B0-%EC%9C%84%ED%95%9C-%EB%B0%A9%EB%B2%95Regularization-Strategies)

[📘 딥러닝을 위한 경사하강법 비교](https://www.koreascience.or.kr/article/JAKO202013261023095.pdf)

-->