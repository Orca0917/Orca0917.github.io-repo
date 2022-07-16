---
title:  "[Recommender System] Wide & Deep 모델과 DeepFM 모델"
excerpt: "선형과 비선형 모델의 조합으로 만든 CTR 예측 모델인 Wide & Deep과 Deep FM에 대해 알아보자"

categories:
  - RecSys
tags:
  - [AI, Naver, BoostCamp, Recommender System]
toc: true
toc_sticky: true
use_math: true

date: 2022-03-18 03:00:00
last_modified_at: 2022-03-18 03:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Wide & Deep

![image](https://user-images.githubusercontent.com/91870042/158940280-a33dac9f-621a-4665-87ff-6284eac3c9e5.png){: .align-center}

위 논문은 선형적인 모델(wide)과 비선형적이 모델(deep)을 결합하여 기존 모델들의 장점을 모두 취하고자 했다. 또한 이 논문은 구글에서 발표한 논문으로 구글 플레이스토어의 추천시스템에 사용되었다고 한다.

## 등장배경

Wide & Deep 의 논문에서는 추천시스템에서 해결해야할 2가지 과제를 소개하였다. 하는 Memorization, 다른 하나는 Generalization 이다.

Memorization은 함께 빈번히 등장하는 아이템 혹 특성 관계를 과거의 데이터로 부터 학습하는 것을 말한다. 주로 Logistic Regression 과 같은 선형 모델이 memorization을 하는데 있어서 탁월한 성능을 낸다. 이때, 아이템 간의 cross-product를 사용해서 아이템간의 interaction을 표현한다. 이 선형모델은 단순하고 확장 및 해석이 용이하지만, 학습 데이터에 없는 feature 조합에 있어서는 취약하다는 단점이 있다.

Generalization은 드물게 발생하거나 전혀 발생한적이 없는 아이템이나 특성 조합을 기존 관계로부터 발견하는 것을 말한다. 주로 Factorization Machine이나 Deep Neural Network 같은 임베딩 기반의 모델이 좋은 성능을 보인다. 하지만 고차원의 sparse한 데이터로부터 저차원의 임베딩을 만들기가 어렵다는 단점이 있다.

Wide & Deep은 이 각각 선형모델과 임베딩 기반의 신경망모델의 장점들을 조합해서 더 좋은 성능을 내는 모델을 만들고자 하였다. 그래서 Wide & Deep의 이름이 붙여진 것도 선형모델과 비선형적 모델의 결합때문이다.

이제 Wide & Deep의 각 구성요소에 대해서 알아보자

## Wide & Deep 구조

![image](https://user-images.githubusercontent.com/91870042/158940472-ac5e69a4-cd6a-4c32-8098-ce5fd74c5950.png){: .align-center}

**Wide Component**

Wide Component는 선형모델을 기반으로 구성되어 있다. 먼저 가장 간단한 선형 모델의 수식을 살펴보자

$$ y=W^TX+b\quad(W=[w_1,\dots,w_n], X=[x_1,\dots,x_n],b\in\mathbb{r}) $$

이 선형모델의 문제점은 서로 다른 feature간의 상호작용을 할 수 없다는 것이다. 그렇기 때문에 두 피처간의 상호작용이 이루어질 수 있도록 형태를 바꾸어야 한다. 그래서 나온 형태가 Cross-Product Transformation이다.

$$ \phi_k(x) = \prod_{i=1}^{d}x_i^{c_{ki}}\quad c_{ki}\in\{0,1\} $$

여기서 발생할 수 있는 문제점은 모든 피처간의 pair에 대해 계산을 진행하면 학습해야 하는 파라미터의 수가 매우 많아지게 된다. 그래서 이 논문에서는 주요 feature 2개(아래 2개)에 대해서만 cross-product 연산을 진행했다.

- User Installed App
- Impression App

**Deep Component**

Deep Component는 비선형 모델을 기반으로 구성되어 있다. 그래서 신경망을 사용하였으며, 그 신경망은 3개의 레이어와 활성함수로는 ReLU를 사용하였다. 이 신경망의 입력이 연속형 변수이면 그대로 전달하고, 카테고리형 변수인 경우에는 feature 임베딩을 한 뒤에 전달하였다.

최종 결과로 나온 임베딩은 concatenate 연산을 취해 하나의 임베딩 벡터로 만들어주었다. 이제 Wide Component와 Deep Component를 한 번에 나타낸 전체구조와 손실함수에 대해 살펴보자

**전체 구조 및 손실함수**

![image](https://user-images.githubusercontent.com/91870042/158940530-c6e1c0ab-8749-40a5-9cbb-c58255b2ffeb.png){: .align-center}

Wide & Deep 모델의 학습과정을 먼저 수식으로 나타내면 다음과 같다.

$$ P(Y=1\mid x)=\sigma(w_{wide}^T[x,\phi(x)] + w_{deep}^Ta^{(lf)}+b) $$

- $x$ : 입력변수
- $\phi(x)$ : 주요 feature 2개 사이의 cross-product 연산
  
모델 구조를 보면, 하단에 `User Installed App` 과 `Impression App` 만 cross-product 연산을 한 것을 알 수 있다. 또한 카테고리형 피처는 별도의 임베딩 과정을 거쳐서 학습에 사용하였다.

## 모델의 성능

![image](https://user-images.githubusercontent.com/91870042/158940573-69f2ed17-b38d-45c0-b952-e143de206f0f.png){: .align-center}

Baseline으로 사용한 Wide모델과 Deep모델은 각각 offline, online에서 서로 다른 양상을 보이지만, 두 개의 모델을 결합하여 만든 wide&deep 모델은 모두 좋은 성능을 보여주었다.

<br/>

# Deep FM

![image](https://user-images.githubusercontent.com/91870042/158940623-4efcdd31-fe1a-485c-a7ac-429f53157421.png){: .align-center}

Deep FM논문은 Wide & Deep 모델과 달리 wide component와 deep component가 동일한 입력값을 사용하도록 한 모델이다. Wide & Deep에서 Wide component는 별도의 연산인 cross-product를 하여 feature 간의 상호작용을 학습하도록 만드는 과정이 필요했다.

Deep FM은 상호작용 정보를 애초에 입력으로 받을 수 있도록 하는 Factorization Machine을 사용하여 입력값을 공유하도록 만들었다. 그래서 Deep FM이라는 말은 Deep Neural Network와 Factorization Machine을 합하여 만들어지게 된 것이다.

## Deep FM의 구조

**FM Component**

Factorizaiton Machine은 이전 포스팅에서 다루었던 FM과 완전히 동일한 모습을 띄고 있다. 다시 한 번 FM 모델의 수식을 가져와보자.

$$
\hat{y}(x)=w_0+\sum_{i=1}^{n}w_ix_i+\sum_{i=1}^{n}\sum_{j=i+1}^{n}\left<\mathbf{v}_i, \mathbf{v}_j \right> x_ix_j, \quad w_0\in\mathbb{R}, w_i\in\mathbb{R}, \mathbf{v}_i\in\mathbb{R}^k
$$

그리고 논문에서 나타낸 FM을 그림으로 나타낸 모습을 살펴보자

![image](https://user-images.githubusercontent.com/91870042/158940700-af74e91c-b3e0-408e-967a-9dc7fc1ff266.png){: .align-center}

위의 구조에서 +(Addition)이 말하는 것은 FM의 수식 중 2번째 Term과 같고, Inner Product를 하는 것은 수식의 3번째 Term과 동일하다. 이후 모든 입베딩을 concatenate해서 sigmoid를 거치게 된다.

**Deep Component**

![image](https://user-images.githubusercontent.com/91870042/158940755-e8d1df2d-7e7d-44ab-9d88-86c1bc6cea00.png){: .align-center}

이 신경망에서 입력으로 사용하는 임베딩은 FM Component에서 사용한 임베딩과 동일하다. (두 구성요소가 임베딩을 생성하는 가중치를 공유한다) 첫번째 히든 레이어로 들어가는 임베딩들은 각각 concatentate 연산을 진행하고 입력된다.

**Deep FM의 전체구조**

$$ \hat{y} = sigmoid(y_{FM} + y_{DNN}) $$

![image](https://user-images.githubusercontent.com/91870042/158940794-3ae2bb1e-b16b-4fcc-98c7-61c081232760.png){: .align-center}

## 모델 성능

![image](https://user-images.githubusercontent.com/91870042/158940872-83af0cec-452d-434c-86e3-b25e79c8e41e.png){: .align-center}

- AUC : binary classification 에서 사용되는 성능 지표로 값이 높을 수록 좋다.
- LogLoss : CTR 예측모델의 binary crossentropy 손실함수

위의 결과를 보면, Deep FM이 다른 CTR예측 모델에 비해서 가장 우수한 성능을 보여주고 있다.

