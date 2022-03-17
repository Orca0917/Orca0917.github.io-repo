---
title:  "[Recommender System] RNN을 사용한 추천시스템"
excerpt: "순환신경망(RNN)의 개념을 간단하게 이해하고, 이를 적용한 추천시스템에 대해서 알아보자"

categories:
  - RecSys
tags:
  - [AI, Naver, BoostCamp, Recommender System]
toc: true
toc_sticky: true
use_math: true

date: 2022-03-16 02:00:00
last_modified_at: 2022-03-16 02:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Recommender System with RNN

RNN을 사용한 추천시스템을 알아보기 전에, RNN의 개념에 대해 간단하게 알아보자

## RNN

RNN(Recurrent Neural Network)는 순환신경망으로 시퀀스 데이터의 처리와 이해에 좋은 성능을 보이는 신경망 구조이다. 시퀀스 데이터의 현재 상태가 다음 상태에 영향을 미치는 특성을 모델에 적용한 것이다.

![image](https://user-images.githubusercontent.com/91870042/158841049-338ae011-e9ea-41b8-b3d5-0d87992c3d81.png){: .align-center}

하지만 이 일반적인 RNN은 옛날의 데이터가 현재에 미치는 영향력이 너무 작아진다는 문제점이 있었다. 그래서 이 RNN을 개선한 LSTM과 GRU가 나오게 되었다.

## LSTM

![image](https://user-images.githubusercontent.com/91870042/158841762-356bca0c-953b-44ae-8c13-50bcdde45f67.png){: .align-center}

LSTM은 시퀀스가 길어지는 경우 학습 능력이 현저하게 저하되는 RNN의 한계를 극복하기 위해서 고안된 모델이다. 이문제를 해결하기 위해서 LSTM은 cell state라는 구조를 제안하였으며 3가지의 Gate로 구성되어 있다. 

- forget Gate: 이전의 정보를 모두 활용하지 않고 일부를 지운다.
- input Gate: 현재의 입력을 모두 활용하지 않고 일부만 사용한다.
- output Gate: 출력으로 cell state의 모든 정보를 보내는 것이 아니라, 일부를 내보낸다.

## GRU

![image](https://user-images.githubusercontent.com/91870042/158842214-8bdd1ca5-7b6e-4ea4-afc7-6b9c7b7c6075.png){: .align-center}

GRU는 LSTM을 변형시킨 모델로 비슷하게 2개의 Gate로 구성되어 있다. GRU는 LSTM과 성능은 비슷하면서 파라미터와 연산량이 적어 모델이 훨씬 가볍기 때문에 자주 사용된다.

- reset Gate : 이전 상태에서 넘어오는 정보를 얼마나 사용할지 결정
- update Gate : 입력과 이전상태의 정보를 사용해서 얼마나 업데이트 진행할지 결정

<br/>

# RNN과 추천시스템

유저가 선호하는 아이템은 항상 일정하지 않고, 시간이 흐름에 따라 변화하며 추천시스템은 이렇게 시간에 따라 변화하는 유저의 취향을 알고 현재에 맞는 아이템을 추천해야 한다. 이런 추천방법은 session based recommender system이라고 부른다.

## GRU4Rec

![image](https://user-images.githubusercontent.com/91870042/158847674-56a072d0-a410-4403-aa5d-9a2634739f65.png){: .align-center}

Session-Based Recommendation with Recurrental Networks는 ‘지금’ 고객이 원하는 상품을 추천하는 것을 목표로하여 추천시스템에 RNN을 적용시킨 논문이다.

GRU4Rec은 사용자가 현재 세션에서 소비한 시퀀스 데이터를 GRU의 입력으로 넣어 바로 다음에 올 확률이 가장 높은 아이템을 추천하는 아이디어를 사용하였다.

### 모델 구조

![image](https://user-images.githubusercontent.com/91870042/158847547-a4f52a50-af9c-4234-83c4-db26b774b283.png){: .align-center}

위의 그림에서 임베딩 레이어가 점섬으로 표시되어 있는데, 사용을 해도 좋지만 사용을 하지 않아도 되는 것을 말한다. 논문에서는 임베딩 레이어를 사용하지 않았을 때 성능이 더 높다고 하였지만, 이 논문 이후의 RNN을 사용한 추천시스템에서는 임베딩 레이어를 잘 사용하였다.

시퀀스 데이터가 GRU 레이어를 통과하여 시퀀스 상 모든 아이템들에 대한 맥락적 관계를 학습한다. 마지막 Feedforward 레이어 역시 점선으로 표시되어 선택적으로 사용할 수 있으며, 최종 출력으로는 다음에 유저가 가장 선택할 아이템에 대한 선호도 점수를 반환한다.

### 학습 과정

**Session Parallel Mini batches**

![image](https://user-images.githubusercontent.com/91870042/158847441-88106790-0ab9-4bd1-a4c8-5d09ea08839d.png){: .align-center}

사용자가 한 번의 세션에서 보는 아이템의 수는 천차만별이다. 그렇기 때문에 GRU4Rec의 입력으로 다양한 길이를 갖는 세션이 들어올 수 있다. 대부분의 세션 길이는 짧지만, 길이가 긴 세션도 존재하기 때문에 이를 주의해야 한다.

길이가 짧은 세션들만 단독으로 사용되어 idle하지 않도록, 세션을 병렬적으로 구성하여 미니배치 학습을 진행하게 하면 이 문제를 해결할 수 있다. GRU4Rec은 세션의 길이가 짧은 것들은 다른 세션의 뒤에 붙여서 학습을 할 수 있도록 만들었다.

**Sampling on the output**

![image](https://user-images.githubusercontent.com/91870042/158844603-055d8e07-3c5c-4443-989c-6b0fc6350f7e.png){: .align-center}

현실에서는 아이템의 수가 매우 많아서 모든 후보 아이템에 대한 확률을 계산하는 것에는 무리가 있다. 그렇기 때문에 negative sampling을 사용해 subset을 구성하고 이 아이템들을 사용해 모델을 학습시켜야 한다.

세션에 존재하는 데이터들은 유저가 상호작용을 한 데이터이지만 상호작용 하지 않은 데이터에 대해서도 평가를 내려야 하기 때문에 학습을 진행해야 한다. 상호작용을 하지 않은 아이템은 존재 자체를 몰랐거나 관심이 없었다고 볼 수 있다. 논문에서는 아이템의 인기가 높은데 사용자가 상호작용을 하지 않았다면, 관심이 없는 아이템이라고 보았다.

### 결과

![image](https://user-images.githubusercontent.com/91870042/158845276-e2888a3f-412e-493d-9687-2b772a07443e.png){: .align-center}

기존에 가장 좋은 성능을 보이던 Item-KNN 모델 대비 GRU4Rec은 약 20% 더 높은 추천 성능을 보였다. 모델을 고정시키고 다양한 loss를 사용해보고 GRU의 hidden unit을 조절하면서 다양한 실험을 해보았는데, GRU의 hidden unit이 클 때 더 좋은 추천 성능을 보였다. (Cross Entropy 예외)