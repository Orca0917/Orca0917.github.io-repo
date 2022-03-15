---
title:  "[Recommender System] 추천시스템의 종류와 연관 규칙 분석"
excerpt: "인기도 기반 추천을 하기 위한 연관 규칙분석과 탐색 방법"

categories:
  - RecSys
tags:
  - [AI, Naver, BoostCamp, Recommender System]
toc: true
toc_sticky: true
use_math: true

date: 2022-03-14 03:00:00
last_modified_at: 2022-03-14 03:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# 추천시스템 기법

추천 시스템의 종류

- [Simple Aggregate (popularity, average score, recent uploads)](https://killerwhale0917.github.io/recsys/boostcamp-recsys-recbypopularity/)
- [Association Analysis](#연관-분석)
- [Content-based Recommendation](https://killerwhale0917.github.io/recsys/boostcamp-recsys-tfidf/)
- [Collaborative Filtering](https://killerwhale0917.github.io/recsys/boostcamp-recsys-cfintro/)
- [Item2Vec Recommendation and ANN](https://killerwhale0917.github.io/recsys/boostcamp-recsys-item2vec/)
- [Deep Learning-based Recommendation](https://killerwhale0917.github.io/recsys/boostcamp-recsys-dlmlp/)
- Context-aware Recommendation
- Multi-Armed Bandit(MAB)-based Recommendation

## 개요

추천시스템은 **inference 시간**이 상당하게 중요하게 작용한다. 이 시간이 적어야 사용자들에게 바로 추천을 해줄 수 있는데, 딥러닝은 모델이 무겁고 연산량이 많아서 실제로 많이 사용하고 있지는 않다. 그렇기 때문에 **기계학습을 사용해서 고전적인 방법을 아직도 많은 회사나 기업에서 다루고 있다**. 기계학습을 사용했을 때의 장점은 추천을 한 원인을 같이 제공해줄 수 있는 점도 있다. 물론 성능은 딥러닝을 적용시켰을 때, 가장 좋게 나오고 있다. 

<br/>

# 연관 분석

## 개요

**연관 규칙 분석** (Association Rule Analysis, Association Rule Mining)은 흔히 장바구니 분석 혹은 서열분석이라고도 불린다. 어떤 상품을 구매하거나 조회했을 때, **연속된 거래들 간의 규칙을 발견하기 위한 분석**을 말한다.

예를 들어, 맥주와 기저귀를 같이 구매하는 빈도가 어떻게 되는지, 컴퓨터를 산 고객이 다음에 가장 많이 사는 상품은 무엇인지 분석하는 것이 연관규칙분석에 해당한다.

## 연관 규칙

![image](https://user-images.githubusercontent.com/91870042/158140196-07776458-91e6-41b0-9f41-1afb53a58598.png){: .align-center width="70%"}

연관 규칙은 특정 사건이 발생했을 때, 함게 빈번하게 발생하는 또 다른 사건의 규칙을 말한다. 

> IF (antecedent) THEN (consequent)

위에서 antecedent가 말하는 것은 "특정사건", consequent는 함게 발생하는 또다른 사건을 말한다. 둘은 **서로소 관계**를 만족하고 있어야 한다.

- Ex. antecedent={"빵", "버터"}, consequent={"우유"}

### Frequent Itemset

![image](https://user-images.githubusercontent.com/91870042/158008350-9c8d6e4e-3217-4f59-9cb5-dc7cf0e9886c.png){: .align-center width="70%"}

Frequent Itemset에 대해서 알아보기 앞서서 Itemset에 대해서 살펴보자. **Itemset**은 1개 이상의 아이템으로 구성된 집합을 말한다. 위에서 본 {"빵", "버터"}도 itemset이다. 다른 자료를 살펴보면 *K-Itemset* 이라는 표현을 자주 찾아볼 수 있는데, 이는 Itemset이 K개의 아이템으로 이루어져 있음을 말한다.

**Support count($\sigma$)**는 전체 transaction에서 특정 Itemset이 등장하는 횟수를 말하며, **Support**는 특정 Itemset이 전체 transaction에서 차지하는 비율을 말한다.

- $\sigma$({"빵", "우유"}) = 3
- $\text{Support}$({"빵", "우유"}) = 0.6

**Frequent Itemset**은 한글로 말하면 빈발집합으로 Itemset 중 자주 등장하는 Itemset을 말한다. 특정 비율 이상을 넘기는 Itemset인데, 이때 만족하는 최소 비율을 *minimum support* 라고 부른다. 반대로 그 비율을 만족하지 못하는 것을 Infrequent Itemset 이라고 한다.

## 연관 규칙의 척도

위에서 정의한 빈발집합간의 관계(연관 규칙)을 만들기 위해서는 얼마나 관련이 되어있는가를 판단하는 지표가 필요하다. 이제부터 $X,Y$ 를 Itemset이라고 하고, $N$ 을 전체 transaction의 수라고 정의한다.

### Support⭐

연관 규칙에서 support(지지도)는 **두 itemset $X, Y$ 를 포함하는 transaction의 비율**을 말한다. support 값은 전체 연관 규칙중에서 빈도가 높거나 구성비율이 높은 규칙을 찾거나 불필요한 연산을 줄이기 위해서 사용한다([Apriori 알고리즘](#frequent-itemset-generation-strategies))

$$ s(X)=\frac{n(X)}{N}=P(X), \quad s(X\rightarrow Y)=\frac{n(X \cup Y)}{N}=P(X\cap Y) $$

$$ \quad s(X)\ge s(X\rightarrow Y) $$

### Confidence⭐

Confidence(신뢰도)는 **$X$ 가 포함된 transaction 가운데 $Y$ 도 포함하는 transaction의 비율**을 말한다. Confidence가 높다는 것은 연관성이 높은 유용한 규칙을 뜻한다.

$$ c(X\rightarrow Y)=\frac{n(X\cup Y)}{n(X)}=\frac{s(X\rightarrow Y)}{s(X)}=\frac{P(X \cap Y)}{P(X)}=P(Y\mid X) $$

### Lift⭐

`lift`는 아이템 A를 구매할 때, 아이템 B도 구매하는지 서로 간의 연관성을 파악하는 비율을 의미한다. 이 향상도의 값을 가지고 어떤 관계를 갖는지 알 수 있다.

- $\text{lift}(A\rightarrow B)>1:$ 양의 상관관계를 갖는다. ⇒ 아이템 A를 사면, 아이템 B를 살 것이다!
- $\text{lift}(A\rightarrow B)=1:$ 독립적인 관계를 갖는다. ⇒ 아이템 A를 사는 것은 아이템 B를 사는것에 영향이 없음
- $\text{lift}(A\rightarrow B)<1:$ 음의 상관관계를 갖는다. ⇒ 아이템 A를 사면, 아이템 B를 안살 것이다!

$$ \text{lift}(A\rightarrow B)=\frac{P(B\mid A)}{P(B)} $$

<br/>

위에서는 특정 Itemset $X,Y$ 에 대해서 계산을 진행했는데 문제는 이러한 Itemset을 구성하고 비교하는 경우의 수가 Item의 크기가 많아지면 기하급수적으로 많아지게 되는 것이다. 그렇기 때문에 유의미한 규칙을 사용해야만 한다. 실제로 아이템의 개수가 10개만 되더라도 전체적으로 57,000번의 연산을 진행해야 한다.

유의미한 규칙만 찾아내기 위해서 `minimum support`와 `minimum confidence`를 사용할 수 있다. 이를 사용해서 조건부 확률이 낮은 규칙을 필터링하는 것인데 더 자세한 것은 [Apriori 알고리즘 파트](#frequent-itemset-generation-strategies)에서 다루도록 하겠다.

**lift**값을 사용해서 내림차순으로 정렬하여 의미있는 규칙만을 평가해볼 수 있다. lift값이 크다는 것은 규칙을 구성하는 antecedent와 consequent가 연관성이 높고 유의미하다는 말이다. 또한, lift값을 사용했을 때, 유저의 입장에서 질적으로 만족스러운 결과를 얻을 수 있다.

![image](https://user-images.githubusercontent.com/91870042/158142303-6e988507-a1f2-4c05-bfc2-d2d7b0105889.png){: .align-center width="70%"}

$X$ 를 와인, $Y$ 를 와인오프너, $Z$ 를 생수라고 하고 각각의 조건부 확률을 다음으로 정의한다고 하자.

$$ P(Y\mid X) = 0.1 \quad P(Z\mid X) = 0.2 \quad P(Y) = 0.01 \quad P(Z) = 0.2 $$

위의 값을 lift값으로 계산해보면 다음과 같다.

$$ l(X\rightarrow Y) = \frac{P(Y\mid X)}{P(Y)} = 10 $$

$$ l(X\rightarrow Z) = \frac{P(X\mid Z)}{P(X)} = 1 $$

여기서 말하고자 하는 것은, 단순 조건부확률만 보았을 때는 와인을 샀을 때 생수를 살 확률이 와인을 샀을 때 와인오프너를 살 확률보다 높게 나왔었다. 하지만 lift값으로 계산을 해보니 와인을 샀을 때 와인오프너를 살 비율이 더 높다는 것을 알 수 있다. 바로 생수는 그 관계와 상관없이 많이 구매하는 물품이기 때문이다.

## 연관 규칙의 탐색

다시 돌아와서, 주어진 transaction에서 아래의 조건을 만족하는 연관 규칙을 찾아야 유의미한 연관규칙을 얻어내 수 있었다. 어떻게 하면 만족하는 연관 규칙을 찾아낼 수 있을까.

$$\text{support} \ge \text{minimum support}$$

$$\text{confidence} \ge \text{minimum confidence}$$

### Brute-force Approach

Brute-force는 가능한 모든 규칙을 나열해서 직접 계산을 통해서 된다 안된다를 판별하는 것이다. 당연히 모든 경우의 수를 확인하는 것이기 때문에 정확하지만, 시간과 계산이 매우 많이 필요로 한다는 단점이 존재한다. inference 시간이 중요한 추천시스템의 입장에서는 매우 치명적일 수 있다.

![image](https://user-images.githubusercontent.com/91870042/158009981-1df3d97c-5973-43af-919f-16d66b997fc1.png){: .align-center width="70%"}

시간복잡도는 $O(NMW)$ 이며, 실제로 계산하는 횟수는 다음과 같이 계산해볼 수 있다.

$$ R=\sum_{k=1}^{d-1}\left [ \binom{d}{k}\times \sum_{j=1}^{d-k}\binom{d-k}{j} \right ] = 3^d=2^{d+1}+1 $$

따라서 효율적인 연관규칙을 탐색하는 방법을 찾아야 하며 이는 크게 2가지 단계로 구성되어 있다.

1. **Frequent Itemset Generation** : minimum support를 만족하는 모든 Itemset 생성하기
2. **Rule Generation**: mimimum confidence 이상의 연관규칙을 생성한다. 이 때, 규칙을 이루는 antecedent와 consequent는 서로소를 만족해야 한다.

위의 2가지 과정 중에서 첫 번째 빈발집합을 찾는 과정이 계산을 많이 필요로 하기 때문에 그 부분을 최적화 시켜야 한다.

### Frequent Itemset Generation Strategies

첫 번째 방법으로, 가능한 후보 Itemset의 개수를 줄여버리는 것이다. $M$ 을 감소시키는 방법인데 가지치기를 통해서 줄일 수 있다. 이 방법을 사용하는 알고리즘이 **Apriori 알고리즘**이다.

다음은 탐색하는 transaction의 수를 줄이는 것이다. $N$ 을 감소시키는 방법으로 Itemset의 크기가 커짐에 따라서 전체 $N$ 개의 transaction보다 적은 개수로 탐색하게 해준다. 이 방법을 사용하는 알고리즘은 **DHP(Direct Hashing & Pruning) 알고리즘**이다.

마지막으로 전체 탐색횟수를 줄이는 알고리즘이 있다. $NM$ 을 감소시키는 방법이며 효율적인 자료구조를 사용해서 후보 Itemset과 transaction의 수 모두를 저장해서 모든 Itemset과 transaction의 조합에 대해서 탐색하지 않아도 되게 만들어주는 것이다. 이것을 사용하는 알고리즘은 **FP-Growth 알고리즘**이다.