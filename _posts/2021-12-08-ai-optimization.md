---
title:  "[부스트캠프 Pre-Course] 딥러닝 기초: Optimization"
excerpt: "최적화와 관련된 주요한 용어와 다양한 Gradient Descent기법"

categories:
  - boostcamp
tags:
  - [AI, Naver, BoostCamp, Python, Math]
toc: true
toc_sticky: true
 
date: 2021-12-08
last_modified_at: 2021-12-08
---

# <span style = "color: #00adb5">introduction</span>

> Language is the source of misunderstanings

# <span style = "color: #00adb5">Gradient Descent</span>

loss function을 편미분 하여 국소적인 최소값을 찾기 위해서 계속 반본적으로 최적화시킨다.

# <span style = "color: #00adb5">Generalization</span>
우리의 목적은 `일반화(Generalization)`성능을 높이는 것이다. 그렇다면 일반화의 성능을 높인다는 것은 무엇을 의미할까?

![image](https://user-images.githubusercontent.com/91870042/145222399-075013f7-5baa-4fb7-bd3b-33e73faddadd.png){: .align-center}

먼저 데이터는 크게 2종류로 나눌 수 있다. 
- `학습 데이터(Training Data)`: 신경망 모델을 학습시키기 위한 데이터를 말한다.
- `테스트 데이터(Test Data)`: 신경망 모델의 성능을 판단하기 위한 데이터를 말한다.

주어진 학습데이터를 사용하여 신경망을 반복적으로 학습하게 되면, 해당 학습 데이터에 대한 에러는 계속 줄어든다. 하지만 그렇다고 학습데이터에 대한 에러(Training Error)가 0에 가까워진다고 해서 좋은 것은 아니다.

![image](https://user-images.githubusercontent.com/91870042/145222399-075013f7-5baa-4fb7-bd3b-33e73faddadd.png){: .align-center}

위의 그림을 보게되면, 학습데이터로 학습을 많이 할수록, 테스트 데이터에 대한 오차는 커지는 것을 알 수 있다. 여기서 일반화가 좋다는 것은 테스트데이터와 학습데이터로 나오는 결과의 일관성이 적다는 것이다.

> 일반화의 성능이 높다는 것은 Train Data와 Test Data의 차이가 적어, 모두 일관성 있는 데이터를 출력하는 것을 말한다.

## Overfitting & Underfitting
학습 데이터에 대해서는 정확하게 맞추지만, 테스트 데이터에 대해서는 잘 동작하지 않는 것을 `overfitting`이라고 한다. 그와 반대로, Neural Network가 너무 간단하거나, 학습을 너무 조금시켜서 잘 동작하지 않는 것을 `underfitting`이라고 한다. 그렇기 때문에 우리는 적절한 Neural Network를 생성하여 과하지 않게 학습시켜야 한다.

## cross-validation
학습 데이터만을 이용해서 학습을 시켜 좋은 Nerual Network를 만드는데에는 한계가 있다. 그렇기에 우리는 여러개의 데이터를 사용하는 것과 같은 효과를 줄 수 있는 `cross-validation`기법을 사용해야 한다.

Cross validation은 학습데이터를 \\(k\\)개의 묶음으로 나눈다. 이 묶음중에서 번갈아가면서 \\(k-1\\)개의 묶음을 선택하여 Neural Network를 학습시킨다. 남은 1묶음의 학습데이터로 테스트 데이터처럼 평가를 하는 것이다. 이렇게 남은 1묶음의 학습데이터를 `validate data`라고 표현한다.

Cross-Validation은 `K-fold validation`이라 표현하기도 한다.

![image](https://user-images.githubusercontent.com/91870042/145223764-c6b40538-45b7-45c7-a1fc-4a69f6f3ecab.png){: .align-center}

## Bias and Variance
- `Variance`: 데이터를 넣었을 때, 결과값이 얼마나 일관성있게 나오는지를 의미한다. (탄착군)
- `Bias`: 데이터의 결과가 평균적으로 정답(True Target)에 접근하고 있는지를 의미한다.

![image](https://user-images.githubusercontent.com/91870042/145223996-04aa2213-3651-43b9-9749-b6c431db646d.png){: .align-center}

### Tradeoff
나의 학습데이터에 Noise가 있다고 가정할 때, 이 Noise가 있는 데이터를 최소화시키는 것은 3가지 구성의 합으로 나타낼 수 있다. 아래에서 \\(t\\)는 target data를 의미하고, \\(f\\)는 neural network로 추론한 결과를 말한다.

![image](https://user-images.githubusercontent.com/91870042/145224668-d9d69a72-33c6-43d1-b74a-89e61c055334.png){: .align-center}

내가 Noise를 최소화 하려고 하는 것은 1가지의 값인데, 이 값은 다시 3가지 (*bias, variance, noise*)로 구성이 되어 있어서 하나가 줄어들어도 나머지가 커질 수 밖에 없는 상황을 말한다.

## Bootstrapping
`Bootstrapping`은 여러개의 데이터중 임의의 데이터를 추출하여 학습 데이터를 생성하는 것을 말한다. 모든 학습데이터를 이용해서 학습하는 것이 좋을 것 같지만, 좋지 않은 경우가 많다.

1. Bagging(Bootstrapping aggregating)
추출된 임의의 학습데이터들로 여러개의 Neural Network Model을 생성한다. 이후, 하나의 데이터에 대해서 각각의 Model이 얼마나 일관성있는 출력을 갖는지 비교한다.

2. Boosting
추출된 임의의 학습데이터들로 Neural Network를 학습시킨다. 나머지 데이터들에 대해서 예측을 하지 못하는 경우, 나머지 데이터에 대해서 잘 학습된 Nerual Network Model을 생성한다.

![image](https://user-images.githubusercontent.com/91870042/145226008-fddb29cc-7740-4796-a652-07cb81923691.png){: .align-center}

# <span style = "color: #00adb5">Practical Gradient Descent method : 고전적 경사하강법</span>

- Stochastic Gradient Descent(SGD)  
한 번에 하나의 데이터만을 입력하여 출력값을 업데이트 하면서 빠르게 전진한다.

![image](https://user-images.githubusercontent.com/91870042/145226572-03ea8edf-900a-4cd9-b7e7-81272db9388b.png){: .align-center}

- Mini-batch gradient descent
batch-size의 샘플을 이용해서 gradient를 계산해서 업데이트

- Batch gradient descent
한번에 모든 데이터를 이용해서 gradient의 평균을 이용해서 업데이트

## Batch-size  Matters
> It has been observed in practice that when using a larger batch there is a degradation in the quality in the quality of the model, as measured by its ability to generalize.

> We ... present numerical evidence that supports the view that large batch methods tend to converge sharp minimizers of the training and testing functions. In contrase, small-batch methods consistently converge to flat minimizers... this is due to the inherent noise in the gradient estimation. - On large-batch Training for Deep Learning, 2017

위의 말을 정리하면 다음 2가지로 나눌 수 있다.
- `Batch크기를 크게하는 경우`: Sharp minimizers가 된다.
- `Batch크기를 작게하는 경우`: Flat minimizers가 된다.

minimizers는 Sharp보다는 Flat한 것이 Generalized가 더 잘되어 있기 때문에 `flat`을 사용하는 것이 좋다. 따라서 Batch크기를 작게하는 것이 테스트 데이터에 대한 결과값에 유사하다.

![image](https://user-images.githubusercontent.com/91870042/145227566-7d826e05-7d59-4c7b-8af9-ebcee2e67d5c.png){: .align-center}

위의 그림을 봤을 때, Batch크기를 크게 하여 sharp minimum에 도달하였을 경우에는 테스트 데이터에 대한 결과값과의 차이가 커진다. (y절편의 차이값)

# <span style = "color: #00adb5">Gradient Descent Methods</span>
## stochastic gradient descent
![image](https://user-images.githubusercontent.com/91870042/145227804-22b83532-64d4-4ba4-9a31-4d1ba56791e1.png){: .align-center}

계산된 기울기에 대해서 최소값을 찾기 위해서 다가가는 수식이다. 하지만, gradient에 대해 학습률(\\(\eta\\))을 설정하기 어렵다는 단점과 local minimum에 수렴하는 단점이 존재한다.

## momentum
`momentum`은 경사하강법을 통해 \\(W\\)가 이동하는 과정에서 일종의 관성을 부여하는 것이다. \\(W\\) 값을 업데이트 할때, 이전의 방향을 반영하여 그 쪽 방향으로 진행한다. 장점으로 `local minimum`을 피하고 `global minimum`의 값에 도달하게 한다.

![image](https://user-images.githubusercontent.com/91870042/145228766-1e722bd2-0e00-451f-bc84-df86f4665c47.png){: .align-center}

## nesterov accelerated gradient
NAG는 momentum과 비슷하게, 관성을 부여해서 결과값을 이동한다. 다시 그 위치에서 gradient값을 계산해서 다음 \\(W\\)값을 업데이트 한다. **한 번 이동한 지점에서 다시 Gradient연산 수행**

![image](https://user-images.githubusercontent.com/91870042/145229297-774c8b4f-adbd-4441-9246-5929aa7e00f1.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/145229445-dea1fa3c-0ca7-4210-824f-43f326c9e8da.png){: .align-center}

## Adagrad
경사하강법에서 데이터의 학습률\\(\eta\\)를 설정하는 것은 중요하다. 학습률을 너무 높거나 낮게하면 학습이 안되기 때문이다. 이런점을 보완해서 만든 것이 `Adagrad(Adaptive Gradient)`이다.

학습률의 조정은 지금까지 변화한 변수들은 최저치에 근접했을 확률이 높을 것으로 보고 학습률을 적게 한다. 적게 변화한 변수들은 최저치 값에 도달하기 위해서 많이 이동해야할 확률이 높기 때문에 학습률을 크게 한다. 그러기 위해서는 지금까지의 변화한 수치를 저장하는 값이 필요한데 \\(G_t\\)에 저장되어 있다.

![image](https://user-images.githubusercontent.com/91870042/145229991-6e1e8b01-5fed-4226-bfc4-b14023a1a532.png){: .align-center}

위의 식에서 \\(\epsilon\\)는 분모가 0이되는 것을 방지하는 값이다. 때에 따라서는 \\(\epsilon\\)값을 얼만큼 설정하느냐에 따라서 학습의 결과가 크게 변동된다. 하지만 \\(G_t\\)는 시간이 지날수록 그 값은 매우 커지는데, 그렇게 되면 학습률 \\(\epsilon\\)는 0에 가까워져 학습이 안되는 문제가 발생한다.

## Adadelta
`Adadelta`는 `Adagrad`의 문제점을 보완하기 위해서 나왔다. 이를 보완하기 위해서 Adadelta는 `자동이동평균`과 `이차근사법`을 사용한다. Adadelta는 윈도우 사이즈를 지정하기 위한 변수가 필요한데, 이 값이 매우커져 오류가 생기는 문제점이 있다. Adadelta의 큰 특징은 학습률을 지정하지 않는다는 점이다.

![image](https://user-images.githubusercontent.com/91870042/145230780-a69d18f3-6477-467c-af96-ca89240e624b.png){: .align-center}

## RMSprop
Adadelta에 대해서 learning rate를 추가한 개념이다.

![image](https://user-images.githubusercontent.com/91870042/145231339-db6125db-5459-4cf5-9cd2-405678973315.png){: .align-center}

## Adam
Gradient 함수중에서 가장 무난하게 사용이 된다. 앞에서 말한 `Momentum`과 `Adagrad`를 섞은 함수이다.

![image](https://user-images.githubusercontent.com/91870042/145231620-4ca7cf30-5240-4981-9fb9-df3228f2f140.png){: .align-center}

<br>

# <span style = "color: #00adb5">Regularization</span>
위에서 알아본 `overfitting`이 일어나지 않도록, 적절하게 학습을 하게하는 역할을 한다.

## Early stopping
학습 데이터를 이용해 학습을 하다가 일정 시점이 되면 학습을 멈추는 것이다. validate data를 통해 모델 성능을 평가하고, 그 차이가 증가하는 경향이 있다면, 학습을 종료시킨다.

![image](https://user-images.githubusercontent.com/91870042/145232468-ba86e053-3748-4ede-bf0d-7b74c31a0a10.png){: .align-center}

## parameter norm penalty
parameter가 너무 커지지 않도록 만드는 것이다. Neural Network의 \\(W\\)를 작게 만드는것이다. 학습과정에서 큰 가중치에 대해서는 그에 상응하는 큰 페널티를 부과하여 `overfitting`을 억제한다.

![image](https://user-images.githubusercontent.com/91870042/145232696-baf725db-8076-40cb-8908-ca24a0bb7686.png){: .align-center}

## data augmentation
데이터가 무한히 많으면, 왠만하면 잘된다. 어떤식으로든 데이터를 이용해서 추가적인 데이터를 생성하는 것이다. 라벨이 변환되지 않는 한에서 추가적으로 데이터를 생산하는 것이 좋다.

![image](https://user-images.githubusercontent.com/91870042/145233009-3bb3b08e-39e1-4cf5-b5ba-549b85b8ff30.png){: .align-center}

## Noise Robustness
입력과 weight에 노이즈를 섞어서 테스트를 더 강하게 한다. 최종적으로 이상치나 noise가 섞여 있어도 좋은 결과를 낼 수 있게 만드는 것이다.

![image](https://user-images.githubusercontent.com/91870042/145233233-8b107b17-b729-4b21-8fbe-ae0b2c06bf26.png){: .align-center}


## label smoothing
학습데이터들 중 2개를 뽑아서 두 데이터를 섞는 것이다. 섞는 방식은 아래와 같은 방법들이 있다.

![image](https://user-images.githubusercontent.com/91870042/145233354-3e8e56e1-9b58-4fa6-b431-c8358d628dad.png){: .align-center}


## drop out
`drop out`은 neural network의 일부 레이어를 사용하지 않으면서 학습을 진행하는 것이다. 이를 통해 매번 다른 노드가 학습되기 때문에 전체 가중치가 overfitting되는 것을 방지할 수 있다.

![image](https://user-images.githubusercontent.com/91870042/145233570-0636de00-8996-4cad-baf6-f5caba23bc42.png){: .align-center}

## batch normalization
배치 정규화는 평균과 분산을 조정하는 과정이 별도의 과정으로 떼어지지 않고 신경망 안에서 포함되어 학습되어 평균과 분산을 조정하는 방법이다. Neural Network의 각 레이어마다 정규화하는 레이어를 두어서 변형된 분포가 나오지 않도록 조절하는 방법이다.

아래 수식을 보면, 미니 배치의 평균과 분산을 이용해서 정규화를 한다. 이 후, 학습가능한 변수 \\(\beta\\)값을 이용해서 실행된다. (Back propagation을 통한 학습)

![image](https://user-images.githubusercontent.com/91870042/145234280-75aea743-d742-4f76-af21-44161d47c5b3.png){: .align-center}

<br>

# <span style = "color: #00adb5">References</span>

[📘 부스트캠프 AI Tech 3기 Pre-Course: Optimization](https://www.boostcourse.org/onlyboostcampaitech3/lecture/1203307?isDesc=false)

[📘 [Deep Learning] 배치 정규화](https://eehoeskrap.tistory.com/430)

[📘 인공신경망 - Parameter Norm Penalies](https://munjeongkang.github.io/ANN4/)

[📘 신경망에서 과적합을 방지하기 위한 방법](https://velog.io/@yuns_u/%EA%B3%BC%EC%A0%81%ED%95%A9%EC%9D%84-%EB%B0%A9%EC%A7%80%ED%95%98%EA%B8%B0-%EC%9C%84%ED%95%9C-%EB%B0%A9%EB%B2%95Regularization-Strategies)

[📘 딥러닝을 위한 경사하강법 비교](https://www.koreascience.or.kr/article/JAKO202013261023095.pdf)
