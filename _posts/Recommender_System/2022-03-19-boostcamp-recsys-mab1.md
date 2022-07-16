---
title:  "[Recommender System] Multi-Armed Bandit 기초"
excerpt: "MAB가 무엇이고 이것을 추천시스템에 어떻게 적용시킬 수 있을까"

categories:
  - RecSys
tags:
  - [AI, Naver, BoostCamp, Recommender System]
toc: true
toc_sticky: true
use_math: true

date: 2022-03-19 00:00:00
last_modified_at: 2022-03-19 00:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Multi-Armed Bandit

Multi-Armed Bandit에 대해 알아보기 전에 One-Armed Bandit이 무엇인지 찾아보자. One-Armed Bandit은 카지노에 존재하는 슬롯 머신이다. 그러면 Multi-Armed Bandit은 이런 슬롯머신이 여러개 존재하는 상황을 떠올려 보면 된다.

![image](https://user-images.githubusercontent.com/91870042/159110130-70105a14-af4c-4a7f-b98c-245384b1e0ec.png){: .align-center}

위의 사진처럼 총 $k$ 개의 슬롯 머신이 존재하고 팔을 당길 수 있는 횟수가 총 $N$ 번일 때, 어떻게 해야 최대의 이익을 얻을 수 있을까? 라는 문제가 Multi-Armed Bandit문제이다. 아래에서는 이 수익을 최대화 하기 위한 강화학습 알고리즘에 대해서 살펴보고자 한다. 그 전에 문제를 다시 정의해보자.

각 슬롯머신에 대해서 매번 얼마의 수익(=보상, reward)이 발생되는지 모르기 때문에 가장 간단하게 모든 슬롯머신을 동일한 횟수로 당겨볼 수 있다. 하지만 이는 최대의 reward를 보장하지 않는다. 또 하나의 방법으로는 일정 횟수만큼 슬롯머신들을 당겨보고 그 중에서 평균적으로 좋은 reward를 주는 슬롯머신에 남은 횟수를 모두 투자해볼 수 있다.

여기서 알아볼 수 있는 2개의 중요한 관점은 Exploration(탐색)와 Exploitation(활용)이다.

- 탐색: 모든 슬롯머신들을 탐색할 수록 더 좋은 결과를 내는 슬롯머신을 알아낼 수 있다. 하지만, 탐색에 너무 많은 횟수를 사용하게 되어 최대의 보상을 얻을 수가 없다.
- 활용: 탐색에 적은 횟수를 사용했기 때문에, 선택한 슬롯머신만을 활용하는 것이 최적이 아닐 수 있다. 그렇기 때문에 최대의 보상을 얻을 수가 없다.

이런 문제 때문에, 우리는 exploration과 exploitaiton을 적절히 조절하여 최대의 이익을 얻을 수 있도록 만들어야 한다.

## 문제 정의

위에서 소개한 Multi-Armed Bandit문제를 수식으로 작성하면 아래와 같다.

$$ q_*(a) \doteq \mathbb{E}[R_t \mid A_t = a] $$

- $t$ = 슬롯머신을 플레이할 수 있는 횟수. 일반화하여 time step 이라고 부른다.
- $A_t$ = 시간 $t$ 에 플레이한 슬롯머신이다. 일반화하여 action 이라고 부른다.
- $R_t$ = 시간 $t$ 에 특정 슬롯머신을 플레이하여 받은 이익이며 일반화하여 reward 라고 한다.
- $q_*(a)$ = 슬롯머신(action) $a$ 에 대한 reward 의 실제 기대값이다.

이는 각 슬롯머신이 나온 reward값에 대해 기대값을 낸 것을 말한다. 우리가 실제 발생할 $q_*(a)$ 에 대한 값을 정확히 알고있다면 매번 최적의 선택을 하여 최대의 이익을 얻을 수 있지만 그럴 수가 없기 때문에 추정을 할 수 밖에 없다. 따라서 우리는 시간 $t$ 에 대한 추정치 $Q_t(a)$ 를 최대한 정밀하게 구하는 것이 목표이다.

## MAB: Greedy Algorithm

MAB에서 그리디 알고리즘은 Simple Average Method 라고도 부른다. 말 그래도 현재 시점 $t$ 에 대해서 최고의 성능을 보여준 슬롯머신을 매번 계산하여 선택하는 것이다. 이를 수식으로 나타내면 다음과 같다.

$$ Q_t(a)\doteq \frac{\text{sum of rewards when a taken prior to t}}{\text{number of times a taken prior to t}} = \frac{\sum_{i=1}^{t-1}R_i\cdot 1_{A_i=a}}{\sum_{i=1}^{t-1} 1_{A_i=a}} $$

하지만, 이런 그리디 방법은 처음에 선택되는 슬롯머신과 그에 대한 reward에 따라서 매우 크게 영향을 받을 수 있다. 시행횟수가 많아진다면 전체적으로 이상적인 추정치 값인 $q_*(a)$ 에 근접할 수 있지만 시행횟수가 매우 적은 초반에는 그 값의 신뢰도가 매우 낮기 때문이다. 바로 Exploration이 부족한 문제가 발생한다.

## MAB: Epsilon Greedy Alogrithm

위에서 소개한 그리디 알고리즘(Simple Average Method)에 epsilon인 랜덤 확률값을 추가한 개념이다. 위에서는 매번 최적의 효율을 내는 슬롯머신을 선택하였다면 이번에는 슬롯머신을 플레이하기 전에 동전을 던진다. 그 동전은 앞이 나오면 지금까지 좋은 성능을 보인 슬롯머신을 선택하고 뒤가 나오면 랜덤한 슬롯머신을 선택하도록 하였다. 수식으로 표현하면 아래와 같다.

$$ A_t=\left\{\begin{matrix}\underset{a}{\text{argmax}} Q_t(a) \quad \text{with probability } 1-\epsilon \\\text{a random action} \quad \text{with probability } \epsilon\end{matrix}\right. $$

이 epsilong greedy algorithm은 그 발상이 굉장히 단순하면서도 강력한 알고리즘이다. 하지만 time step이 많아질 수록 그 성능이 좋지 못하다는 단점이 있다.

## MAB: Upper Confidence Bound

Upper Confidence Bound(UCB)알고리즘은 최적의 슬롯머신을 선택할 때, 그 슬롯머신이 최적일지에 대한 신뢰도(불확실성)을 추가하여 계산하도록 하였다. 먼저 수식을 살펴보자

$$ A_t\doteq \underset{a}{\text{argmax}} \left[Q_t(a) + c\sqrt{\frac{\ln t}{N_t(a)}} \right] $$

- $N_t(a):$ 슬롯머신 $a$ 를 선택했던 횟수
- $c$ : exploration을 조정하는 하이퍼 파라미터

루트 내에 존재하는 식을 살펴보면 시행횟수가 많아질 수록 분자는 $\ln$ 을 사용했기 때문에 일정 지점에 수렴하게 되지만 분모는 갈 수록 커지기 때문에 그 값이 0에 수렴할 것이다. 이는 시행횟수가 많아지면 불확실성이 낮다는 것을 의미한다.

반대로, 시행횟수가 적다면 루트 내의 값이 크기 때문에 불확실성이 높다는 말이 된다. 이처럼 UCB알고리즘은 이런 불확실성 이라는 개념을 도입하여 최적의 선택을 할 수 있도록 만들었다. 이렇게 만들어진 UCB 알고리즘은 Exploration과 Exploitation이 적절하게 나눌 수 있는 결과를 가져왔다.

![image](https://user-images.githubusercontent.com/91870042/159111034-ae3410ef-9df5-4b3e-951c-e8fcdd633f2a.png){: .align-center}

중요한것은 위에서 소개한 Epsilon Greedy 알고리즘과 UCB 알고리즘은 적절한 하이퍼 파라미터를 선택해야지만 최고의 성능을 보여줄 수 있다는 것이다. 그렇게 되어야만 exploration과 exploitation이 각각 절절하게 이루어질 수 있다.

<br/>

# 추천시스템과 MAB

위에서 소개한 MAB와 MAB알고리즘을 어떻게 추천시스템에 적용시켜볼 수 있는지 살펴보자. 기존의 CTR 추천문제를 Bandit 문제로 바꾸면 아래와 같다.

- reward = 실제 서비스의 지표인 클릭 또는 구매
- action = 개별 아이템
- 추천방식 = 정책(알고리즘)

이 리워드를 최대화 시키는 방향으로 모델을 학습하고 추천을 수행하면 다른 무거운 추천 모델을 사용하지 않고도 간단한 Bandit 기법을 적용해도 온라인 지표(CTR)이 좋아진다. exploration은 지속적으로 변화하는 유저의 취향을 탐색하거나 추천하려는 아이템의 분야를 확장하는 것을 말하고, exploitation은 추천에서 유저의 취향에 맞는 아이템을 추천하는 것을 말한다.

## 예시: 유저에게 아이템 추천

유저 개개인에 대해서 모든 아이템에 대한 Bandit을 구하는 것은 데이터가 매우 부족하기 Bandit이 수렴하지 않아 불가능하다. 유저가 모든 아이템에 대해서 구매를 하고 평을 남긴 것이 아니기 때문이다. 그렇기에 비슷한 유저들 끼리 묶어서 그룹화하여 해당 그룹에 대한 Bandit을 구하는 것이 효과적이다.

![image](https://user-images.githubusercontent.com/91870042/159111269-bc4e1036-d3ba-4947-abb8-83dc6bef1abd.png){: .align-center}

비슷한 유저들 끼리 묶어 클러스터링하여 각 그룹별 후보 리스트를 생성해야 하는데 여기서는 인기도 기반으로 리스트를 생성할 수도 있고, 협업필터링을 기반하여 아이템 리스를 만들어볼 수 있다. 유저에 대한 추천의 경우, 필요한 Bandit의 수는 유저 클러스터의 개수 와 후보 아이템 개수의 곱과 같다.

## 예시: 유사한 아이템 추천

![image](https://user-images.githubusercontent.com/91870042/159111373-c179335e-9cc6-4767-b7d9-d1f8a1d22bb5.png){: .align-center}

주어진 아이템과 유사한 아이템 리스트를 만들어서 Bandit을 적용해볼 수 있다. 먼저 유사한 아이템을 추출하는 방법에는 이전에 배운 MF, Item2Vec 기반의 유저-아이템 상관관계를 기반으로 한 유사도, 컨텐츠 기반의 유사도를 구하여 생성할 수 있다. 그리고 생성된 유사 아이템 리스트에 대해서 Bandit 알고리즘을 적용하여 CTR을 높이는 방향으로 선택한다. 이때 필요한 Bandit의 수는 아이템의 개수와 후보 아이템의 개수간의 곱과 같다.

