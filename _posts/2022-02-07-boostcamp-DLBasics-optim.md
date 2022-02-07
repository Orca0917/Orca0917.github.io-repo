---
title:  "[Deep Learning Basic] Optimization"
excerpt: "최적화와 관련된 주요한 용어와 다양한 Gradient Descent기법"

categories:
  - DLBasic
tags:
  - [AI, Naver, BoostCamp, Python, Math]
toc: true
toc_sticky: true
 
date: 2022-02-07 02:00:00
last_modified_at: 2022-02-07 02:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Gradient Descent

손실함수의 1차적인 미분값을 사용하여 국소적인 최솟값을 찾기 위해 반복적으로 최적화 시키는 방법을 말한다. 이번에는 최적화라는 문제에서 중요하게 다뤄지는 개념에 대해서 살펴본다.

# Generalization
우리의 목적은 `일반화(Generalization)`성능을 높이는 것이다. 그렇다면 일반화의 성능을 높인다는 것은 무엇을 의미할까?

먼저 데이터는 크게 2종류로 나눌 수 있다. 
- `학습 데이터(Training Data)`: 신경망 모델을 학습시키기 위한 데이터를 말한다.
- `테스트 데이터(Test Data)`: 신경망 모델의 성능을 판단하기 위한 데이터를 말한다.

주어진 학습데이터를 사용하여 신경망을 반복적으로 학습하게 되면, 해당 학습 데이터에 대한 에러는 계속 줄어든다. 하지만 그렇다고 학습데이터에 대한 에러(Training Error)가 0에 가까워진다고 해서 좋은 것은 아니다.

![image](https://user-images.githubusercontent.com/91870042/145222399-075013f7-5baa-4fb7-bd3b-33e73faddadd.png){: .align-center width="70%"}

위의 그림을 보게되면, 학습데이터로 학습을 많이 할수록, 테스트 데이터에 대한 오차는 커지는 것을 알 수 있다. 여기서 일반화가 좋다는 것은 테스트데이터와 학습데이터로 나오는 결과의 일관성이 있다는 것이다.

> 일반화의 성능이 높다는 것은 Train Data와 Test Data의 차이가 적어, 모두 일관성 있는 데이터를 출력하는 것을 말한다.

## Overfitting & Underfitting
학습 데이터에 대해서는 정확하게 맞추지만, 테스트 데이터에 대해서는 잘 동작하지 않는 것을 `overfitting`이라고 한다. 그와 반대로, Neural Network가 너무 간단하거나, 학습을 너무 조금시켜서 잘 동작하지 않는 것을 `underfitting`이라고 한다. 그렇기 때문에 우리는 적절한 Neural Network를 생성하여 과하지 않게 학습시켜야 한다.

![image](https://user-images.githubusercontent.com/91870042/152759600-f9082138-8084-407d-baef-2dbe9d7580aa.png){: .align-center width="70%"}


## cross-validation
학습 데이터만을 이용해서 학습을 시켜 좋은 Nerual Network를 만드는데에는 한계가 있다. 그렇기에 우리는 여러개의 데이터를 사용하는 것과 같은 효과를 줄 수 있는 `cross-validation`기법을 사용해야 한다.

Cross validation은 `k-fold validation`이라고도 하며, 학습데이터를 \\(k\\)개의 묶음으로 나눈다. 이 묶음중에서 번갈아가면서 \\(k-1\\)개의 묶음을 선택하여 Neural Network를 학습시킨다. 남은 1묶음의 학습데이터로 테스트 데이터처럼 평가를 하는 것이다. 이렇게 남은 1묶음의 학습데이터를 `validate data`라고 표현한다.

주의해야할 것은, 반드시 테스트데이터를 사용해서 학습시켜서는 안된다.

![image](https://user-images.githubusercontent.com/91870042/145223764-c6b40538-45b7-45c7-a1fc-4a69f6f3ecab.png){: .align-center width="70%"}

## Bias and Variance
- `Variance`: 데이터를 넣었을 때, 결과값이 얼마나 일관성있게 나오는지를 의미한다. (탄착군)
- `Bias`: 데이터의 결과가 평균적으로 정답(True Target)에 접근하고 있는지를 의미한다.

![image](https://user-images.githubusercontent.com/91870042/145223996-04aa2213-3651-43b9-9749-b6c431db646d.png){: .align-center width="70%"}

### Tradeoff
나의 학습데이터에 Noise가 있다고 가정할 때, 이 Noise가 있는 데이터의 Cost를 최소화시키는 것은 3가지 구성의 합으로 나타낼 수 있다. 아래에서 \\(t\\)는 target data를 의미하고, \\(\hat{f}\\)는 neural network로 추론한 결과를 말한다. \\(f\\)는 실제 데이터의 값이다.

![image](https://user-images.githubusercontent.com/91870042/145224668-d9d69a72-33c6-43d1-b74a-89e61c055334.png){: .align-center width="70%"}

내가 Noise를 최소화 하려고 하는 것은 1가지의 값인데, 이 값은 다시 3가지 (*bias, variance, noise*)로 구성이 되어 있어서 하나가 줄어들어도 나머지가 커질 수 밖에 없는 상황을 말한다.

## Bootstrapping
`Bootstrapping`은 여러개의 데이터중 임의의 데이터를 추출하여 학습 데이터를 생성하는 것을 말한다. 모든 학습데이터를 이용해서 학습하는 것이 좋을 것 같지만, 좋지 않은 경우가 많다.

1. **Bagging**(Bootstrapping aggregating)  
추출된 임의의 학습데이터들로 여러개의 Neural Network Model을 생성한다. 이후, 하나의 데이터에 대해서 각각의 Model이 얼마나 일관성있는 출력을 갖는지 비교한다. 이는 `앙상블(Ensenble)`이라고도 말한다.

2. **Boosting**  
  추출된 임의의 학습데이터들로 Neural Network를 학습시킨다. 나머지 데이터들에 대해서 예측을 하지 못하는 경우, 나머지 데이터에 대해서 잘 학습된 Nerual Network Model을 생성한다.

  예를 들어, 학습데이터가 100개가 있고 해당 데이터에서 예측을 진행하는 모델을 만들었다고 하자. 모델이 80개의 데이터에 대해서는 잘 예측하지만 나머지 20개의 데이터에 대해서는 예측을 잘 하지 못한다고 할 때, 나머지 20개에 대해서만 잘 작동하는 모델을 다시 만드는 것을 말한다.


![image](https://user-images.githubusercontent.com/91870042/145226008-fddb29cc-7740-4796-a652-07cb81923691.png){: .align-center width="70%"}

<br>

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

$$ m_t = \beta_1 m_{t=1} + (1-\beta_1)g_t $$

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