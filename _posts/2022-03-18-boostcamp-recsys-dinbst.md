---
title:  "[Recommender System] Deep Interest Network, Behavior Sequence Transformer"
excerpt: "User Behavior Feature를 사용하는 DIL과 BST에 대해 간단하게 알아보자"

categories:
  - RecSys
tags:
  - [AI, Naver, BoostCamp, Recommender System]
toc: true
toc_sticky: true
use_math: true

date: 2022-03-18 04:00:00
last_modified_at: 2022-03-18 04:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Deep Interest Network

![DIL 논문 표지](https://user-images.githubusercontent.com/91870042/158994900-05826d95-a079-4fb5-aed5-6a638dced536.png){: .align-center}

기존의 딥러닝 기반의 모델들은 모두 유사한 임베딩과 Fully Connected Layer를 거치는 모델링을 진행하였다. 하지만 이런 기존의 방식은 사용자의 다양한 관심사를 반영하기가 힘들었다. 예로 사람은 하나의 카테고리 상품만 찾아보지 않고, 여러가지의 카테고리에 동시에 관심을 가질 수 있다.

Deep Interest Network(DIN)은 학습할 때, 유저의 이런 행동양식을 학습하고자 했고 그에 따른 입력으로는 유저가 기존에 소비하던 아이템의 리스트가 필요했다. 이 유저의 소비리스트를 이용해 행동 피처를 생성하고 예측 하고자 하는 대상 아이템과의 유사성 또는 관련성을 학습하였다.

다음 사진을 보면, 기존의 학습 데이터 이외에 User Behavior Features 를 추가한 것을 볼 수 있다.

![User Behavior Feature가 포함된 데이터셋](https://user-images.githubusercontent.com/91870042/158995719-ec84977e-0cc2-4d58-a1f8-3f2c8ef52c6d.png){: .align-center}

## DIN의 구조

![DIN의 모델구조 이미지](https://user-images.githubusercontent.com/91870042/159015579-3c2ad756-2cf6-4b4e-aa94-18776181d8c4.png){: .align-center}

위의 그림은 Deep Interest Network의 모델을 나타내며, 이는 크게 3가지 구성으로 나누어볼 수 있다.

1. Embedding Layer
2. Local Activation Layer
3. Fully Connected Layer

**Embedding Layer**

임베딩 레이어는 위에서 이미지로 살펴본 User Behavior Feature 가 포함된 데이터 셋에서 각 feature들을 벡터로 임베딩을 해준다. 모델 구조의 최하단에 입력이 들어가서 임베딩 시키고 각 feature의 임베딩 벡터를 concat 시켜주는 것을 확인해볼 수 있다.

**Local Activation Layer**

Deep Interest Network의 핵심적인 레이어로 추천하고자 하는 아이템(이 논문에서는 광고를 말한다)과 User behavior 데이터 간의 유사도를 구한다. $N$ 개의 임베딩된 User Behavior 벡터는 각각 한 번씩 광고 임베딩과 Activation Unit에 들어간다.

모델 구조의 우측 상단은 Activation Unit을 표현한 것이다. Activation Unit은 출력으로 하나의 스칼라 값인 Activation Weight 를 반환하는데, 이 값은 입력으로 받아온 두 개의 벡터간의 유사도를 의미한다. Activation Unit 구조에 존재하는 Dice는 DIL 논문에서 발표한 활성함수이다. 이 Activation Weight 값이 높은 것은 연관성이 높은 것을 말하며 나중에 이 정보를 더 많이 사용할 수 있게 내적으로 연산을 해준다.

Activation Unit을 거친 총 $N$ 개의 스칼라 값은 기존의 임베딩 벡터와 내적으로 연산되며, 이 연산된 $N$ 개의 벡터들은 다시 SUM Pooling을 통해 하나의 벡터로 뭉쳐진다. 이렇게 뭉쳐진 벡터와 User Behavior 이외의 피처 임베딩 벡터(User Profile Vector, Context Feature Vector, ...) 들은 모두 concatenate 되어 다음 레이어인 Fully Connected Layer로 전달된다.

**Fully Connected Layer**

Fully Connected Layer는 사실 DIL논문에서 발표한 활성함수 DICE의 사용을 제외하면 기존 모델에서 가지고 있던 MLP 레이어와 동일하다.

<br/>

논문에서는 지금까지 살펴본 내용들을 다음 그림으로 나타내었다. 기존의 유저가 어떤 상품에 관심을 갖었는지 나타내는 User Behavior 정보가 있다면, 광고하고자 하는 아이템과 각각 비교해서 어떤 것이 유사도가 높은지 판단한다. 그래서 아래 사진에서는 추천하고자 하는 아이템이 외투였는데, 외투와 비슷한 아이템들이 높은 유사도를 갖는 것을 볼 수 있다.

![외투와 다른 아이템간의 유사도 예시](https://user-images.githubusercontent.com/91870042/159018289-a93ce680-2eb8-4180-a5e5-28ed06bafa91.png){: .align-center}

## 모델의 성능

![DIL 모델의 성능(결과)](https://user-images.githubusercontent.com/91870042/159018435-504e6d08-4b86-494a-b5b2-e95a3e6a56a2.png){: .align-center}

논문에서 사용한 MovieLens와 Amazon 데이터셋에 대해 이전에 살펴본 Wide & Deep 또는 DeepFM보다 더 좋은 결과를 내고 있다. 또한 활성함수인 Dice를 사용하였을 때, 더 높은 성능을 보여주었다.

<br/>

# Behavior Sequence Transformer

![image](https://user-images.githubusercontent.com/91870042/159108123-45cdd598-c764-446e-8618-452afea4ec49.png){: .align-center}

위 논문은 NLP 분야에서 좋은 성능을 내는 Transformer 구조를 CTR 예측문제에 적용시킨 논문이다. 어떻게 NLP 분야의 Transformer를 CTR예측에 적용시킬 수 있는지, 어떤 공통점이 있었는지 살펴보도록 하자.

CTR예측 데이터와 NLP 번역 데이터는 둘다 sparse한 feature로 구성되어 있다. 또한 그 feature 간의 상호작용에는 low-order feature와 high-order feature간의 상호작용이 존재하여 비선형적인 관계를 이룬다. NLP에서는 문장에 포함된 단어의 순서가 중요하듯 CTR 데이터도 사용자의 행동 순서가 예측에 큰 영향을 미친다. 그렇기 때문에 Transformer구조를 CTR 예측 문제에 사용해볼 수 있었던 것이다.

사실, 방금 살펴본 좋은 성능을 보인 Deep Interest Network에서 존재하는 Activation Unit은 Transformer의 Attention 역할을 수행하였다. 그렇기 때문에 Transformer의 일부를 사용했다고 볼 수 있다.

BST의 구조를 이해하기 전에는 Transformer에 대한 이해가 필요한데 자세한 내용은 [여기(Transformer)](https://killerwhale0917.github.io/dlbasic/boostcamp-DLBasics-transformer/)에 기술되어 있다.

## BST의 구조

![image](https://user-images.githubusercontent.com/91870042/159108374-13466ba9-f8d1-4a69-a530-af4c30f10017.png){: .align-center}

BST는 입력으로 들어가는 정보가 User Behavior 정보의 집합이 아닌, 그 순서가 있는 Sequence를 입력으로 사용하였다. 그렇게 해서 최종적으로 현재 예측하고자 하는 아이템 정보를 더 정확하게 예측하고자 하였다.

Transformer Layer에서는 Transformer의 인코더와 디코더를 모두 사용하는 것이 아니라 인코더만을 사용하였다. 이 인코더에 대해서 좀 더 자세히 살펴보기 위해 수식으로 나타내면 다음과 같다.

$$ \text{Attention}(\mathbf{Q, K, V}) = \text{softmax}\left( \frac{\mathbf{QK}^T}{\sqrt{d}} \right)\mathbf{V} $$

$$ \mathbf{Q} = \mathbf{EW}^Q, \quad \mathbf{K} = \mathbf{EW}^K, \quad \mathbf{V} = \mathbf{EW}^V$$

- $\mathbf{E}$ : User Behavior Sequence + Target Item 의 임베딩 벡터
- $\mathbf{W}^Q$ : Query Matrix
- $\mathbf{W}^K$ : Key Matrix
- $\mathbf{W}^V$ : Value Matrix

위의 연산은 self-attention 구조이며 위의 결과값을 모두 concatenation하여 multi-head attention을 진행한다.

$$ \mathbf{S} = \text{MH}(E) = \text{Concat}(\text{head}_1, \text{head}_2, \dots, \text{head}_n) = \mathbf{W}^H $$

$$ \text{head}_i = \text{Attention}(\mathbf{Q, K, V}) $$

multihead attention의 결과 $\mathbf{S}$ 는 Feed Forward Neural Network를 통과하며 이 FFN의 앞과 뒤에는 Add & Norm 구조가 추가되어 있다. 이 구조에는 skip-connection과 dropout이 포함되어 있다.

$$ \mathbf{S'} = \text{LayerNorm}(\mathbf{S} + \text{Dropout}(\text{MH}(\mathbf{S})) $$

$$ \mathbf{F} = \text{LayerNorm}(\mathbf{S'} + \text{Dropout}(\text{LeakyReLU}(\mathbf{S'W}^{(1)} + b^{(1)})\mathbf{W}^{(2)} + b^{(2)}) $$

이렇게 되면, 하나의 인코딩 layer를 생성한 것이고 이런 인코딩 layer를 여러개 쌓아서 표현할 수 있다. Transformer에서는 이 인코딩 layer를 6개 쌓아서 표현하였었다. 

## BST의 비교

이번에는 BST가 이전에 배운 DIN과 어떻게 다른지 또한 Transformer와는 어떻게 다른지에 대해서 한 번 살펴보자. DIN과 비교하였을 대는 local activation unit이 transformer layer로 변형이 되었고 입력이 되는 임베딩 또한 user behavior feature 집합에서 user behavoir sequence로 그 순서가 포함되어서 학습하도록 만들었따.

Transformer와 비교하였을 때는 인코딩 과정에서 dropout과 활성함수로 LeakyReLU를 사용하였으며 논문에서는 레이어를 1개~4개만 쌓아서 표현하였다. 이때 성능은 레이어가 1개일 때가 가장 좋게 나왔다. Transformer에서 positional embedding을 `cos`와 `sin`을 통해서 계산했었다면 여기서는 독자적인 Positional Embedding과정 $(pos(v_i) = t(v_t) - t(v_i))$ 을 거쳐서 표현하였다. 이는 물리적인 시간차이를 positional embedding을 사용한 것이다.

## BST의 성능

![image](https://user-images.githubusercontent.com/91870042/159109222-fe8e7b8b-ce18-49c0-9d1a-bccedc011e5b.png){: .align-center}

Transformer를 사용한 CTR 예측인 BST는 CTR 예측문제에서 SOTA에 준하는 성능을 보여주었다. 단 이 Transformer의 인코딩 레이어를 2개이상 사용했을 때는 오히려 성능이 감소되는 경향이 존재하였다. 그 이유로 이 user behavior sequence가 NLP sequence보다는 덜 복잡하기 때문이라고 보고 있다.