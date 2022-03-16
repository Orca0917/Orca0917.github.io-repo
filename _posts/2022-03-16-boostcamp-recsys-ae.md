---
title:  "[Recommender System] AutoEncoder를 사용한 추천시스템"
excerpt: "오토인코더의 개념을 이해하고 이를 활용한 추천시스템에 대해서 알아보자"

categories:
  - RecSys
tags:
  - [AI, Naver, BoostCamp, Recommender System]
toc: true
toc_sticky: true
use_math: true

date: 2022-03-16 00:00:00
last_modified_at: 2022-03-16 00:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Recommender System with AE

## AutoEncoder

![image](https://user-images.githubusercontent.com/91870042/158577816-8d514bc1-50c5-49c9-a8c7-488ee7e8470d.png){: .align-center}

오토인코더는 입력 데이터를 받아서 그대로 복원시키는 비지도 학습 모델이다. 오토인코더는 중간의 Hidden Layer를 입력 데이터의 feature representation 데이터로 사용한다. 모델은 입력과 출력의 차이를 최소로 하도록 손실함수를 구성한다. 

### Denoising AutoEncoder

![image](https://user-images.githubusercontent.com/91870042/158577918-9ea2c1b5-9e02-4b9a-8b5c-0c0c6a4e9be3.png){: .align-center}

입력 데이터에 random noise 또는 dropout 을 추가하여 학습을 진행한 모델을 말한다. noise가 포함된 입력을 더 잘 복원할 수 있는 모델이 더 강건하게 학습되기 때문에 전체적인 성능이 향상된다.

## AutoRec

![image](https://user-images.githubusercontent.com/91870042/158578012-9e80a56a-65ad-4dc4-a25e-686653707327.png){: .align-center}

AutoRec 논문은 오토인코더를 협업필터링에 적용하여 기존의 협업필터링에 비해서 Representation과 Complexity 측면에서 뛰어난 성능을 보이도록 하였다.

### AutoRec의 아이디어

오토인코더의 입력 데이터를 Rating Vector 로 하여 인코딩 디코딩 과정을 수행한 것이 AutoRec이다. 유저 또는 아이템 벡터를 저차원의 잠재 요인으로 나타내 이를 사용해 평점을 예측한다. MF와 비교했을 때, AutoRec은 비선형 활성함수를 사용하기 때문에 더 복잡한 상호작용을 표현하는데 좋아진다.

### AutoRec의 모델

![image](https://user-images.githubusercontent.com/91870042/158579956-4ecb085c-446e-4647-b410-625538faf851.png){: .align-center}

AutoRec은 아이템과 유저 벡터 중, 한 번에 하나에 대한 임베딩만 진행한다. 위의 사진은 아이템 벡터에 대해서 AutoRec을 적용한 모습을 나타낸다.

- $r^{(i)}:$ 아이템 $i$ 의 Rating 벡터
- $R_{ui}:$ 유저 $u$ 의 아이템 $i$ 에 대한 Rating
- $V:$ 인코더 가중치 행렬
- $W:$ 디코더 가중치 행렬

### 학습과 결과

입력으로 전달된 Rating 벡터와 AutoRec을 통해 생성된 reconstructed rating 벡터간의 RMSE를 최소화 하는 방향으로 학습한다. 학습할 때는, 관측된 데이터에 대해서만 역전파 및 파라미터의 업데이트를 진행한다.

$$ \underset{\theta}{\text{min}}\sum_{r\in S}\|r-h(r;\theta)\|_2^2\\
h(r;\theta)=f(W\cdot g(Vr+\mu)+b) $$

- $S$ : 점수 벡터 $r$ 의 집합
- $f, g$ : 활성함수 (Sigmoid, Identity Function)

![image](https://user-images.githubusercontent.com/91870042/158581152-31c2a110-e3ea-4f21-b753-c78fc886b12d.png){: .align-center}

테스트 결과 MovieLens와 Netflix 데이터셋에서 비교적 단순한 모델인 RBM* 과 MF 등의 모델보다 더 좋은 성능을 보여주었다. RBM은 Restrictive Boltzman Machine 을 말한다.

![image](https://user-images.githubusercontent.com/91870042/158581270-e668c0a2-fa7a-44f4-8dad-d94300416101.png){: .align-center}

테스트 결과를 보았을 때, Hiddent Unit의 개수가 많아질 수록 RMSE가 감소하는 경향을 보였으며 이 논문이 발표된 이후에는 고급 Auto Encoder 기법을 협업필터링에 사용한 후속연구들이 진행되었다.

- DAE: Collaborative Denoising Auto-Encoders for Top-N Recommender Systems,
- VAE: Collaborative Variational Autoencoder for Recommender Systems, ...