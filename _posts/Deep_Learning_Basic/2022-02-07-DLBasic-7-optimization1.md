---
title:  "Optimization PART 1 : Optimization 기본 용어 정리"
excerpt: "Generalization, Under fitting, CV, K-Fold, Bias, Variance, Bootstrap(Bagging, Boosting) 용어 정리"

categories:
  - DLBasic
tags:
  - [AI, BoostCamp]
toc: true
toc_sticky: true
 
date: 2022-02-07 08:00:00
last_modified_at: 2022-07-25
---

![image](https://user-images.githubusercontent.com/91870042/180658653-9df2033f-5e9e-4178-8283-2ad17f08c4c7.png){: .align-center width="100%"}

# Introduction

이번 포스팅 부터 약 3파트에 걸쳐서 생성한 인공지능 모델을 학습하는데 필요한 기본적인 최적화 이론에 대해서 알아보고자 합니다. 특히 가장 첫 파트인 현재 게시글에서는 일반화 성능이 무엇을 의미하는지, 과적합과 K-Fold validation이 무엇인지 등 기본적인 내용에 대해서 다루겠습니다.

<br>

## Generalization

대다수의 인공지능 모델의 학습 목적은 일반화 성능을 올리는 것에 있습니다. 여기서 말하는 일반화 성능은 학습에 사용된 `학습 데이터` 이외에 여러 다른 데이터에 대해서도 동일하거나 유사한 성능을 내는 것을 말합니다. 일반적으로 주어진 학습 데이터에 대해서만 학습을 진행하게 되면, 해당 데이터에 대해서만 잘 예측을 하고 다른 데이터에 대해서는 전혀 엉뚱한 결과를 내는 문제가 발생할 수 있습니다. 이 문제가 과적합 문제이며 바로 아래에서 설명합니다.

인공지능의 모델은 입력으로 들어오는 데이터에 대해서 오차를 줄이를 방향으로 학습을 진행합니다. 다시 말해 학습 횟수가 증가할수록 학습데이터에 대한 오류는 점점 줄어들게 되지만, 너무 학습데이터에만 맞추어져서 다른 데이터셋에 대해서 오류가 증가합니다. 이 모습을 아래와 같이 표현할 수 있습니다.

![image](https://user-images.githubusercontent.com/91870042/180654929-2a02ba3f-7693-4f32-9ced-c272d82c8c5c.png){: .align-center width="70%"}

<br>

## Under & Over fitting

먼저 Under Fitting 은 주어진 학습 데이터 조차도 학습이 제대로 되지 않은 상태를 말합니다. 학습이 덜 된 상태라고 할 수 있으며 비교적 일반화가 되었다고 느낄 수 있지만, 전체적인 정확도가 높지는 않은 양상을 보여줍니다. 반대로 Over Fitting은 학습이 너무 많이 된 상태를 말합니다. 주어진 데이터에 너무 과적합되어 있어서 다른 데이터에 대한 표현이나 예측은 잘 하지 못하는 상황입니다. 한 번 이 2가지 모습을 그림으로 나타내 보겠습니다.

![image](https://user-images.githubusercontent.com/91870042/180655209-79599feb-3a87-41de-b593-5fc4a2de5f0c.png){: .align-center width="90%"}

가장 왼쪽이 Under Fitting 상태입니다. 학습이 제대로 되지 않아서 이미 주어진 데이터 조차도 잘 표현하지 못하고 있습니다. 가장 우측이 Over fitting이 된 상태로 다른 데이터가 새로 들어온다면 제대로 예측하는데 문제가 발생할 수 있습니다. 이 2가지 상태도 아닌 적절한 상태가 중앙의 모습이며 비교적 모든 데이터들을 포괄하는 곡선형의 그래프를 보여줍니다.

<br>

## Cross Validation

대부분의 인공지능 모델을 생성할 때는 데이터셋을 `학습 데이터셋(Train dataset)`과 `테스트 데이터셋(Test dataset)` 2가지로 분류합니다. 그리고 학습데이터로 학습된 모델을 테스트 데이터셋으로 모델의 평가가 이루어지게 됩니다. 먼저 현재 학습이 적절하게 진행되고 있는지 판단하기 위해서 일반적으로 학습 데이터셋을 다시 또 다른 학습 데이터셋과 `검증 데이터셋(Valid dataset)`으로 분리합니다. 지금까지의 설명을 그림으로 표현해보겠습니다.

![image](https://user-images.githubusercontent.com/91870042/180655654-7e2260af-72ba-4109-9b12-a2a45c50d56e.png){: .align-center width="70%"}

학습을 할 때, 검증 데이터셋은 학습에 사용되지 않으며 오로지 현재 모델의 상태를 평가하는데 사용합니다. 하지만, 단순히 이 검증셋 만으로 현재 학습된 모델을 평가하기에는 무리가 있습니다. 만약, 검증셋에 데이터가 우연히 매우 적절하게 분배되어 실제로는 학습이 잘 되지 않았는데 성능이 높다고 평가될 수 있는 경우가 존재할 수 있습니다.

그렇기 때문에 Cross Validation은 처음 주어진 Train Dataset을 여러개로 나눈 후, 검증 데이터로 사용할 데이터셋을 번갈아가면서 사용합니다. 이렇게 하여 나온 결과들의 평균을 통해 모델의 성능을 평가하게 됩니다.

![image](https://user-images.githubusercontent.com/91870042/180655922-948bc0ce-2801-48f6-89e1-fd02de9b11f8.png){: .align-center width="70%"}


<br>

## Bias, Variance

Bias는 해석하면 `편향` 으로 한 쪽으로 치우쳐져 있는 것을 말하며, Variance는 `분산`으로 기댓값으로 부터 얼마나 떨어진 곳에 확률변수가 분포하는지를 의미합니다. 사실 이 한 줄이 bias와 variance의 모든 것입니다. 흔히 이 2가지를 설명할 때 과녁판에 비유하곤 합니다.

![image](https://user-images.githubusercontent.com/91870042/180656078-08fc2c34-3038-46dd-8499-37fffb9e7b66.png){: .align-center width="50%"}

위의 사진을 보았을 때, 인공지능의 학습이 Bias가 적게, Variance도 적게 학습하는 것이 가장 최적의 방법일 것이라고 바로 떠올릴 수 있습니다. 하지만 이 2 값 사이에는 Trade-off 가 존재해서 하나가 줄어들면 다른 하나는 증가하는 성질이 있습니다. 어떤 노이즈가 포함된 데이터가 존재한다고 가정합시다. 그리고 이 데이터를 아래와 같이 표현할 것입니다.

$$
\mathcal{D} = \{ (x_i, t_i)\}^N_{i=1} \quad t = f(x) + \epsilon \quad \epsilon \sim \mathcal{N}(0, \sigma^2)
$$

위의 식에서 나타난 데이터와 노이즈가 없는 실제 값간의 오차를 줄이기 위한 식은 아래와 같이 나타낼 수 있습니다.

$$
\begin{aligned}
\mathbb{E}\left[ (t-\hat{f})^2 \right] &= \mathbb{E}\left[ (t - f + f - \hat{f})^2 \right]\\
&= \dots\\
&= \mathbb{E}\left[ (f - \mathbb{E} [\hat{f}]^2)^2 \right] + \mathbb{E} \left[ (\mathbb{E}[\hat{f}] - \hat{f})^2\right] + \mathbb{E} \left[\epsilon \right]\\
&= \text{bias}^2 + \text{variance} + \text{noise}
\end{aligned}
$$

이 식은 다시 말해 오차를 줄이는 식이 bias 항과 variance 항이 더해진 형태로 이루어져있어 하나를 낮추면 다른 하나가 증가할 수 밖에 없음을 보여줍니다.

<br>

## Bootstrapping

Bootstrapping 은 주어진 학습 데이터를 모두 사용하는 것이 아니라 랜덤 샘플링을 통해서 얻은 데이터셋을 가지고 여러 모델을 학습시키거나 여러 평가를 하는 것을 말합니다. 확률적인 해석으로 무작위 표본 추출에 의존하는 어떤 시험이나 예측 표본 추정치들의 정확도를 할당하게 됩니다.

실제로 주어진 학습데이터를 모두 사용한 1개의 모델보다, 랜덤 샘플링을 통해 만든 여러개의 모델들의 앙상블이 더 좋은 결과를 보여주고 있습니다. Boostrapping에 대한 대표적인 예로 Baggin와 Boosting이 있습니다. 바로 아래에서 살펴봅시다!

<br>

## Bagging & Boosting

`Bagging`은 Bootstrapping aggregating으로 학습데이터에서 랜덤 샘플링을 통해 여러개의 데이터셋을 생성하고 이 여러개의 데이터셋으로 다시 여러개의 학습된 모델을 생성하는 것을 말합니다. 각 모델들의 결과를 평균내어 최종결과로 사용되며 이 기법이 `앙상블`기법이라고 표현합니다.

`Boosting`은 어떤 A 모델이 잘 예측하지 못한 데이터들을 잘 맞추는 모델 B를 생성하는 방식으로 학습하는 것을 말합니다. 우리는 특히 예측하지 못한 데이터들을 weak learner 라고 표현하며 이들을 지속적으로 매꾸어나가는 방식으로 진행합니다. 대표적으로 XGBoost, CatBoost 모델이 있습니다.

![image](https://user-images.githubusercontent.com/91870042/180658559-0fdd929d-bb68-483d-a0d8-9c91e34ff82f.png){: .align-center width="70%"}

<br>

# References

[🎨 Cover Image Source : Unsplash](https://unsplash.com/photos/L3d2KZJoiKk)

[🎨 Over Fitting & Under Fitting Image Source](https://www.researchgate.net/figure/llustration-of-the-underfitting-overfitting-issue-on-a-simple-regression-case-Data_fig2_339680577)

[🎨 Bagging & Boosting Image Source](https://seongjuhong.com/2021-01-17pm-ensemble-bagging-and-boosting/)

<!--

# Practical Gradient Descent method

1. Stochastic Gradient Descent(SGD)  
    한 번에 랜덤 데이터를 여러개 추출하여 입력값으로 사용하는 방법이며, 이에 대한 출력값을 업데이트 하면서 빠르게 전진한다.
  
2. Mini-batch gradient descent  
    batch-size의 샘플을 이용해서 gradient를 계산해서 업데이트

3. Batch gradient descent  
    한번에 모든 데이터를 이용해서 gradient의 평균을 이용해서 업데이트

## Batch-size  Matters
> It has been observed in practice that when using a larger batch there is a degradation in the quality in the quality of the model, as measured by its ability to generalize.

> We ... present numerical evidence that supports the view that large batch methods tend to converge sharp minimizers of the training and testing functions. In contrase, small-batch methods consistently converge to flat minimizers... this is due to the inherent noise in the gradient estimation.  
> -On large-batch Training for Deep Learning, 2017

위의 말을 정리하면 다음 2가지로 나눌 수 있다.
- `Batch크기를 크게하는 경우`: Sharp minimizers가 된다.
- `Batch크기를 작게하는 경우`: Flat minimizers가 된다.

minimizers는 Sharp보다는 Flat한 것이 Generalized가 더 잘되어 있기 때문에 `flat`을 사용하는 것이 좋다. 따라서 Batch크기를 작게하는 것이 테스트 데이터에 대한 결과값에 유사하다.

![image](https://user-images.githubusercontent.com/91870042/145227566-7d826e05-7d59-4c7b-8af9-ebcee2e67d5c.png){: .align-center width="70%"}

위의 그림을 봤을 때, Batch크기를 크게 하여 sharp minimum에 도달하였을 경우에는 테스트 데이터에 대한 결과값과의 차이가 커진다. (y절편의 차이값)

[📝 관련 논문: On Large-Batch Training for Deep Learning: Generalization Gap and Sharp Minima](https://arxiv.org/abs/1609.04836)

<br>

# Gradient Descent Methods
## Gradient Descent

$$ W_{t+1} \leftarrow W_t - \eta g_t $$

- \\(W\\) : 가중치 행렬
- \\(\eta\\) : 학습률
- \\(g_t\\) : 손실함수를 행렬에 대해서로 편미분한 값

계산된 기울기에 대해서 최소값을 찾기 위해서 다가가는 수식이다. 하지만, gradient에 대해 <u>학습률(\\(\eta\\))을 설정하기 어렵다는 단점과 local minimum에 수렴하는 단점</u>이 존재한다.

## momentum

$$ a_{t+1} \leftarrow \beta a_t + g_t $$

$$ W_{t+1} \leftarrow W_t - \eta a_{t+1}$$

- \\(\beta\\) : 가속도의 역학을 하는 hyperparameter.

`momentum`은 경사하강법을 통해 \\(W\\)가 이동하는 과정에서 일종의 관성을 부여하는 것이다. \\(W\\) 값을 업데이트 할때, 이전의 방향을 반영하여 그 쪽 방향으로 진행한다. 장점으로 `local minimum`을 피하고 `global minimum`의 값에 도달하게 한다.

쉽게 말해서, momentum을 적용하면, GD로 이동하는 거리보다 더 많이 이동한다.

## nesterov accelerated gradient

$$ a_{t+1} \leftarrow \beta a_t + \nabla f(W_t-\eta\beta a_t) $$

$$ W_{t+1} \leftarrow W_t-\eta a_{t+1}$$

NAG는 momentum과 비슷하게, 관성을 부여해서 결과값을 이동한다. momentum과의 차이점은 \\(g_t\\)부분인데, 다음 지점의 기울기를 계산한 것이다. 현재 a라는 정보가 있으면, 그 방향으로 가보고 간곳의 기울기를 계산해서 더하는 방식이다. 

![image](https://user-images.githubusercontent.com/91870042/145229445-dea1fa3c-0ca7-4210-824f-43f326c9e8da.png){: .align-center width="70%"}

## Adagrad
경사하강법에서 데이터의 학습률\\(\eta\\)를 설정하는 것은 중요하다. 학습률을 너무 높거나 낮게하면 학습이 안되기 때문이다. 이런점을 보완해서 만든 것이 `Adagrad(Adaptive Gradient)`이다.

$$ W_{t+1} = W_t - \frac{\eta}{\sqrt{G_t + \epsilon}}g_t $$

- \\(G_t\\) : 지금까지 변화한 값을 제곱해서 모두 더한 값
- \\(\epsilon\\) : 분모가 0이 되는 것을 방지하는 값. 일반적으로 \\(1e^{-7}\\)로 설정한다

학습률의 조정은 **지금까지 변화한 변수들은 최저치에 근접했을 확률이 높을 것으로 보고 학습률을 적게** 한다. 적게 변화한 변수들은 최저치 값에 도달하기 위해서 많이 이동해야할 확률이 높기 때문에 학습률을 크게 한다. 그러기 위해서는 지금까지의 변화한 수치를 저장하는 값이 필요한데 \\(G_t\\)에 저장되어 있다.

위의 식에서 \\(\epsilon\\)는 분모가 0이되는 것을 방지하는 값이다. 때에 따라서는 \\(\epsilon\\)값을 얼만큼 설정하느냐에 따라서 학습의 결과가 크게 변동된다. 하지만 \\(G_t\\)는 시간이 지날수록 그 값은 매우 커지는데, 그렇게 되면 학습률 \\(\epsilon\\)는 0에 가까워져 학습이 안되는 문제가 발생한다.

하지만 위에서는 \\(G_t\\) 의 값이 무한대로 커져버리면, 학습이 거의 멈춰진다는 단점이 있다.

## Adadelta

$$ G_t = \gamma G_{t-1} + (1-\gamma)g_t^2 $$

$$ W_{t+1} = W_t - \frac{\sqrt{H_{t-1}+\epsilon}}{\sqrt{G_t+\epsilon}}g_t$$

$$ H_t = \gamma H_{t-1} + (1-\gamma)(\Delta W_t)^2 $$

`Adadelta`는 `Adagrad`의 문제점을 보완하기 위해서 나왔다. 분모를 구할 때 제곱합을 누적하지 않고 지수평균을 사용한다. 단, 여기서는 \\(\eta\\) 대신, 변화값의 제곱을 가지고 지수평균 값을 사용한다. 이는 학습이 끝나지 않았는데, 학습률이 0에 가까워지는 Adagrad의 문제를 해결한다.

조금 더 풀어쓰면, 연속적으로 가중평균을 취하는 것은 과거의 gradient값을 지수적으로 감소시킬 수 있다. 따라서 Adadelta는 분모가 과도하게 작아지는 것을 방지하기 위해서 시점 \\(t\\) 에서의 기울기 변화를 크게 반영시키고 이전, 과거의 기울기 변화는 적게 반영시킨다.

**Adadelta에는 학습률이 존재하지 않는다.**

## RMSprop
**RMSprop**역시 Adagrad의 문제점을 해결하며, Adadelta에 대해서 learning rate를 추가한 개념이다.

$$ G_t = \gamma G_{t-1} + (1-\gamma)g_t^2 $$

$$ W_{t+1} = W_t - \frac{\eta}{\sqrt{G_t+\epsilon}}g_t$$

## Adam

$$ m_t = \beta_1 m_{t-1} + (1-\beta_1)g_t $$

$$ v_t = \beta_2 v_{t-1} + (1-\beta_2)g_t^2 $$

$$ W_{t+1} = W_t - \frac{\eta}{\sqrt{v_t + \epsilon}}\frac{\sqrt{1-\beta_2^t}}{1-\beta_1^t}m_t $$

Gradient 함수중에서 가장 무난하게 사용이 된다. 앞에서 말한 `Momentum`과 `Adagrad`를 섞은 함수이다.

<br>

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