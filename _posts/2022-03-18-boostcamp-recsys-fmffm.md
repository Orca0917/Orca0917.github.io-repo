---
title:  "[Recommender System] Factorization Machine과 Field Factorization Machine"
excerpt: "컨텍스트 기반 추천시스템인 FM과 FFM에 대해서 알아보자"

categories:
  - RecSys
tags:
  - [AI, Naver, BoostCamp, Recommender System]
toc: true
toc_sticky: true
use_math: true

date: 2022-03-18 01:00:00
last_modified_at: 2022-03-18 01:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Factorization Machine

![image](https://user-images.githubusercontent.com/91870042/158924725-e594f98e-3f40-43c7-a1e5-b0171a39531c.png){: .align-center}

Factorization Machine(FM)은 Support Vector Machine(SVM)과 MF(Matrix Facotirzation)의 장점을 결합한 모델을 말한다. 먼저 FM이 등장한 배경에 대해서 알아보자.

## 개요

딥러닝이 등장하기 전까지는 SVM이 ML분야에서 가장 많이 사용되는 모델이었다. SVM은 커널공간을 사용해서 비선형 데이터셋에 대해 높은 성능을 보여주고 있었다. 협업필터링이 등장하고, SVM보다 MF계열의 모델이 더 높은 성능을 보이기 시작했다. 매우 희소한 데이터에 대해서는 SVM이 좋은 성능을 내지 못했기 때문이다.

하지만 이런 장점을 갖는 MF는 특별한 환경이나 특별한 조건을 갖춘 데이터에 대해서만 사용할 수 있었다. 그 예로, 입력으로는 [유저, 아이템]으로 되어있고 출력은 [평점] 꼴을 갖는 데이터만 사용이 가능했다.

그래서 SVM과 MF가 갖는 장점을 결합하고자 하였고 그 결과 Factorization Machine 이 탄생하게 되었다.

## FM의 수식

$$
\hat{y}(x)=w_0+\sum_{i=1}^{n}w_ix_i+\sum_{i=1}^{n}\sum_{j=i+1}^{n}\left<\mathbf{v}_i, \mathbf{v}_j \right> x_ix_j, \quad w_0\in\mathbb{R}, w_i\in\mathbb{R}, \mathbf{v}_i\in\mathbb{R}^k
$$

$$
\left<\mathbf{v}_i, \mathbf{v}_j \right> := \sum_{f=1}^{k}\mathbf{v}_{i,f}\cdot\mathbf{v}_{j,f}
$$

위의 수식에서 학습이 되는 파라미터는 $\mathbf{v}_i, \mathbf{v}_j$ 이며, 피처간의 상호작용을 모델링할 수 있도록 만들었다. 또한 수식을 보면, 첫번째와 두번째 항은 Logistic Regression 의 식과 동일하였다. 다만 FM은 뒤에 상호작용을 위한 텀을 추가해서 나타냈다.

다시 위의 식을 Polynomial Model과 비교하면, 전체적으로 유사하게 보이지만, 두번째 Term에서 $x_ix_j$ 와 곱해지는 값을 하나의 파라미터가 아니라 각각 나누어서 일반화시켰다.

## FM의 활용

유저와 아이템(영화)에 대해서 평점을 예측하는 모델을 생각해보자. 유저와 영화에 대한 평점 데이터는 대표적인 High Sparsity 데이터이다. FM을 사용하기 위한 데이터셋 생성을 하면 유저와 영화에 대한 정보 각각이 들어가고 그 평점 정보를 output으로 한다.

![image](https://user-images.githubusercontent.com/91870042/158925986-3bb5817e-d143-4537-9f16-f263f63541b1.png){: .align-center}

그리고 위의 정보를 FM에 맞는 데이터로 임베딩하여 표현하면 아래와 같이 나타내볼 수 있다.

![image](https://user-images.githubusercontent.com/91870042/158926098-efe11404-1155-46a1-833e-e269b882c9ed.png){: .align-center}

이런 입력데이터로 바꾸게 되면, 입력 값의 차우너이 전체 유저와 아이템의 수만큼 증가하게 되며 아주 높은 sparsity를 갖는 데이터가 된다. 이제 그러면 FM에서는 이 데이터를 어떻게 학습하는지에 대해서 살펴보자.

![image](https://user-images.githubusercontent.com/91870042/158926380-05f24f9b-031d-40da-a10f-9d2d0ec332b3.png){: .align-center}

위의 예시에서 `User A` 에 대해 영화 `ST` 에 대한 평점 예측을 진행해보자. `User A` 에 대한 벡터를 $V_A$, 영화 `ST` 에 대한 벡터를 $V_{ST}$ 라고 하자. 두 벡터 $V_A, V_{ST}$ 는 Factorization Machine에 의해 학습되기 때문에 상호작용이 반영될 수 있다.

- $V_A:$ `User B`, `User C` 가 `User A` 와 공유하는 영화 `SW` 의 평점 데이터를 통해서 학습된다.
- $V_{ST}:$ `User B` , `User C` 의 영화 `ST` 에 대한 평점 데이터를 통해서 학습된다.

추가로 그 이외의 다른 Context 정보를 통해서 예측을 해볼 수도 있다. (Other Movies rated, Time, Last Movie rated)

## FM의 장점

SVM와 비교했을 때, FM은 매우 Sparse한 데이터에 대해서도 높은 예측 성능을 보여준다. SVM에 비해서 낮은 선형 복잡도 $(O(kn))$ 를 갖기 때문에 수십억개의 학습데이터에 대해서도 빠르게 학습한다. 모델의 학습에 필요한 파라미터의 개수도 선형적으로 비례한다.

Matrix Factorization과 비교했을 때 MF는 협업필터링 환경 뿐만 아니라 여러 예측 문제에 모두 활용 가능한 범용적인 지도 학습 모델이다. 일반적인 실수변수를 모델의 입력으로 사용하며 유저ID, 아이템ID 뿐만아니라 다른 부가 정보들을 학습에 사용할 수 있다.

<br/>

# Field-aware Factorization Machine

![image](https://user-images.githubusercontent.com/91870042/158927394-bd95aad2-8cbc-4e4e-b2f1-5ead115ccd09.png){: .align-center}

Field-aware Factorization Machines for CTR Prediction은 FM의 변형된 모델인 FFM을 제안하여 더 높은 성능을 보인 논문이다. FFM에 대해 살펴보기 전에 그 등장배경먼저 알아보자

## FFM의 개요

**등장배경**

FM은 예측 문제에 두루 적용 가능한 모델로, Sparse한 데이터에서도 CTR예측에 대해 좋은 성능을 보여주었었다. FFM은 FM을 발전시킨 모델로서 `PITF 모델` 에서 아이디어를 얻었다.

- `PITF 모델` : Pairwise Interaction Tensor Factorization

기존 Matrix Factorization에서는 2차원 행렬을 분해해서 연산을 했었다면, 이번에는 3차원 텐서에 대해서 분해해 연산을 하는 것이다. PITF는 (user, item, tag) 3개의 필드에 대한 클릭율을 예측하기 위해서 (user,item), (item, tag), (user, tag) 각각에 대한 서로 다른 latent factor를 정의하였다.

각각에 대해 서로 다른 잠재 요소를 사용해 더 정확한 학습이 이루어지도록 만들었다. 정리하자면, FM을 일반화하여 여러개의 필드에 대해서 잠재 요소를 정의한 것이 FFM인 것이다.

**특징**

입력 변수를 필드로 나누어 필드별로 서로 다른 잠재요인을 가지도록 factorize한다. 기존의 FM은 하나의 변수에 대해서 k개로 factorize했으나 FFM은 f개의 필드에 대해 각각 k개로 factorize하였다. 그렇다면, Field는 어떻게 정의하고 나누어야 할까?

필드는 모델을 설계할 때 사용자가 정의하는 것! 같은 의미를 갖는 변수들의 집합으로 설정한다. 예를 들어 다음 처럼 나타낼 수 있다.

- 유저: Sex, Device, OS
- 아이템: Ads, Category
- 컨텍스트: App, Banner

CTR 예측에 사용되는 피쳐는 이보다 훨씬 다양한데, 피쳐의 개수만큼 필드를 정의하여 사용할 수 있다.

## FFM의 수식

$$
\hat{y}(x)=w_0+\sum_{i=1}^{n}w_ix_i+\sum_{i=1}^{n}\sum_{j=i+1}^{n}\left<\mathbf{v}_{i, f_j}, \mathbf{v}_{j, f_i} \right> x_ix_j, \quad w_0\in\mathbb{R}, w_i\in\mathbb{R}, \mathbf{v}_{i,f}\in\mathbb{R}^k
$$

수식이 다소 복잡하니 다음 예시와 함께 FM과 비교하여 살펴보자. 아래의 그림과 같은 광고 클릭 데이터가 존재한다고 하고 feature는 Publisher, Advertiser, Gender가 있다고 하자.

![image](https://user-images.githubusercontent.com/91870042/158928198-c05b3d2e-c545-440a-835d-7cbb8f846544.png){: .align-center}

먼저 위의 광고데이터에 대해서 Factorization Machine의 연산과정을 나타내면 다음과 같다.

$$
\hat{y}(x) = \mathbf{w}_0 + \mathbf{w}_\text{ESPN} + \mathbf{w}_\text{Nike} + \mathbf{w}_\text{Male} + \mathbf{v}_\text{ESPN} \cdot \mathbf{v}_\text{Nike} + \mathbf{v}_\text{ESPN} \cdot \mathbf{v}_\text{Male} + \mathbf{v}_\text{Nike} \cdot \mathbf{v}_\text{Male}
$$

FM은 필드가 존재하지 않으며, 하나의 변수에 대해서 factorization 차원인 $k$ 만큼 파라미터를 학습한다.

다음은 FFM를 사용한 연산과정이다.

$$
\hat{y}(x) = \mathbf{w}_0 + \mathbf{w}_\text{ESPN} + \mathbf{w}_\text{Nike} + \mathbf{w}_\text{Male} + \mathbf{v}_\text{ESPN,A} \cdot \mathbf{v}_\text{Nike,P} + \mathbf{v}_\text{ESPN,G} \cdot \mathbf{v}_\text{Male,P} + \mathbf{v}_\text{Nike,G} \cdot \mathbf{v}_\text{Male,A}
$$

FFM은 필드를 사용하기 때문에 각 필드를 $P, A, G$ 로 정의하였고, 하나의 변수에 대해 필드의 개수인 $f$ 와 factorization 차원인 $k$ 의 곱 만큼의 파라미터를 학습시킨다.

## FFM 필드 구성

**Categorical Feature**

![image](https://user-images.githubusercontent.com/91870042/158928898-e574532d-96c4-455d-aff7-dc3351ab93ed.png){: .align-center}

Categorical Feature는 같은 카테고리에 속한 피처들을 필드로 묶어서 정의한다. FFM을 보면 필드는 피처가 속한 필드를 나타내는 것을 알 수 있다.

**Numerical Featrue**

실수형 값의 필드는 다음 2가지 방법으로 정의해볼 수 있다. 이해를 돕기위해 실수값의 예시는 아래 그림에 나온 정보를 사용해보자

![image](https://user-images.githubusercontent.com/91870042/158929574-a98227fd-fa1b-44f8-bd61-ef1f5b72fca4.png){: .align-center}

- Dummy Field  
  Dummy 필드는 실수 값이 갖는 그 값 하나를 필드로 구성하여 나타내는 것을 말한다. 따로 실수값에 대한 처리를 진행하지 않고 필드 자체로 사용하는 것이다.

  $$ \text{Yes} \quad \text{AR:AR:45.73} \quad \text{Hidx:Hidx:2}\quad \text{Cite:Cite:3} $$

- Discreize  
  실수값들에 대해서 $n$ 개의 구간으로 나누어 이진 값을 사용하고, $n$ 개의 변수를 하나의 필드에 할당하는 것을 말한다.

  $$ \text{Yes} \quad \text{AR:45:1} \quad \text{Hidx:2:1}\quad \text{Cite:3:1} $$

## FM과 FFM의 성능 비교

데이터 셋에 따라 조금씩 다르지만, Logitstic Regression에 비해 FM과 FFM이 더 좋은 성능을 보여주고 있다.

![image](https://user-images.githubusercontent.com/91870042/158929919-ce1692b8-7e9c-4e39-8154-35855d80411e.png){: .align-center}

