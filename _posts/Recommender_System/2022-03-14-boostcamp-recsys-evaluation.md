---
title:  "[Recommender System] 추천시스템 평가지표"
excerpt: "추천시스템이나 추천모델의 성능을 평가할 수 있는 방법"

categories:
  - RecSys
tags:
  - [AI, Naver, BoostCamp, Recommender System]
toc: true
toc_sticky: true
use_math: true

date: 2022-03-14 01:00:00
last_modified_at: 2022-03-14 01:00:00
---
📌 **알립니다!** <br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다. <br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# 추천시스템 평가지표

## 개요

우리가 만든 추천시스템이나 추천모델의 성능을 평가할 수 있는 방법에 대해서 알아보자

### 비즈니스/서비스 관점

![image](https://user-images.githubusercontent.com/91870042/158133548-61f8df8a-d5db-449d-9c91-87ef45051f82.png){: .align-center width="70%"}


추천시스템이 비즈니스와 서비스에 얼마나 도움이 되었는지를 평가자료로 사용할 수 있다. 그 측정 방법에는 매출에 얼마나 증가했는지, 페이지를 얼마나 많이 보았는지 (Page View), 노출대비 클릭율(**CTR**)이 얼마나 상승했는지 확인해보면 된다.

### 품질 관점

- **연관성**(Relevance) : 추천된 아이템이 유저에게 관련이 있는 상품인지 평가
- **다양성**(Diversity) : 추천된 Top-K 개의 아이템이 얼마나 다양한 아이템이 존재하는지 평가
- **새로움**(Novelty) : 얼마나 새로운 아이템이 추천되고 있는지 평가
- **참신함**(Serendipity) : 유저가 기대하지 못한 뜻밖의 아이템이 추천되고 있는지 평가

## Offline Test

생성한 추천모델을 검증하기 위해서 가장 먼저 수행되는 테스트이다. 유저로부터 **수집한 데이터를 train, valid, test 데이터셋으로 나누어 모델의 성능을 객관적인 지표로 평가**하는 것이다. 보통 offline test에서 좋은 성능을 보인 모델을 Online 서빙에 투입하지만, 실제 서비스에서는 다른 양상을 보이기도 한다.

![image](https://user-images.githubusercontent.com/91870042/158001922-913825f4-d9d8-46f3-9e1f-178673acd2db.png){: .align-center}

그 원인 중 하나가 **Serving Bias**가 존재하기 때문이다. Serving Bias는 온라인에서 서빙하는 과정에서 발생될 수 있는 새로운 로그가 다시 학습에 사용되어서 모델이 개선되는 것을 말한다.

이 모델을 평가하기 위한 지표를 설정해야 하는데 이 성능지표는 앞서서 살펴본 랭킹문제, 예측문제에 따라서 그 방법이 달라진다.

- 랭킹문제: `Precision@K`, `Recall@K`, `MAP@K`, `NDCG@K`, `HitRate`
- 예측문제: `RMSE`, `MAE`

### Precision@K, Recall@K

**Precision@K**는 우리가 추천한 $k$개의 아이템 중에서 실제로 유저가 관심을 보인 아이템의 비율을 말한다. 우리가 얼마나 정확도 있게 추천을 했는지 알아볼 수 있다.

**Recall@K**는 유저가 관심있어한 아이템 중에서 우리가 추천한 비율을 말한다. 얼마나 유저의 성향을 파악해서 추천을 잘 했는지 알아볼 수 있다.

예시로 한 번 살펴보자. 우리가 추천한 아이템의 수 K를 5라고 했을 때, 유저가 관심있어한 아이템이 2개이고, 유저가 관심있어한 아이템의 전체 개수를 4개라고 한다면 다음과 같이 계산할 수 있다.

- $Precision@5 = \frac{2}{5} = 0.4$
- $Recall@5 = \frac{2}{4} = 0.5$

### AP@K, MAP@K

**AP@K**(Average Precision)은 Precision@1부터 Precision@K까지의 평균값을 의미한다. @1부터 더해서 평균을 내기 때문에 높은 순위에 있는 아이템을 추천할 수록 점수가 증가한다. 다시말해 **추천한 아이템 $K$개의 순서도 점수에 포함**이 된다는 의미이다. AP@K의 수식은 다음과 같다.

$$ AP@K=\frac{1}{m}\sum_{i=1}^{K}\text{Precision}@i \times rel(i) $$

- $m$ : 추천된 상품 중 실제로 사용자가 만족한 개수
- $rel(i)$ : $i$ 번째 상품이 실제로 사용자가 만족을 했는지 여부

**MAP@K**(Mean Avereage Precision)은 모든 유저에 대한 AP@K의 평균값을 말한다.

$$ MAP@K=\frac{1}{|U|}\sum_{u=1}^{|U|}(AP@K)_u $$

### NDCG@K

**NDCG@K**(Normal Discounted Cumulative Gain)은 추천시스템에서 가장 많이 사용되는 지표 중 하나로서 원래는 검색에서 등장한 평가지표이다. Precion@K, MAP@K와 마찬가지로 상위 K개의 아이템을 만들어서 비교를 진행한다. MAP@K처럼 상위 K개의 아이템에 대해서 더하는 연산이 존재하기 때문에 추천되는 아이템의 순서도 평가에 반영이 된다.

MAP@K에서 $rel$ 값을 사용할 때는 이진 값(0 또는 1)만 사용했던 것에 비해 그 값을 실수로 사용하기 때문에 유저에게 얼마나 더 관련있는 아이템을 상위로 노출시키는지 알 수 있다. 이제 NDCG의 연산이 어떻게 이루어지는지 알아보자.

- **Cumulative Gain(CG)**: 상위 K개의 아이템에 대한 관련도를 합한 값이다.

$$ CG_K = \sum_{i=1}^{K}rel_i $$

- **Discounted Cumulative Gain(DCG)**: 추천된 아이템의 순서에 따라서 Cumulative Gain값을 Discount한다.

$$ DCG_K = \sum_{i=1}^{K}\frac{rel_i}{log_2(i+1)} $$

- **Ideal Discounted Cumulative Gain(IDCG)**: 이상적인 추천이 일어났을 때의 DCG값으로 가능한 DCG값 들중 가장 큰 값과 같다.

$$ IDCG = \sum_{i=1}^{K}\frac{rel_i^{opt}}{log_2(i+1)} $$

- **Normalized Discounted Cumulative Gain(NDCG)**: 추천 결과에 따라 구해진 DCG를 IDCG로 나눈 값을 말한다. 얼마나 이상적으로 추천이 이루어졌는지 확인해볼 수 있다.

$$ NDCG = \frac{DCG}{IDCG} $$

## Online Test

Online Test는 Offline Test에서 검증된 가설이나 모델을 이용해서 실제 서비스에 적용시켜서 그 추천 결과를 서빙하는 단계이다. Online A/B Test로 진행되고 추천시스템 변경 전후의 성능을 비교하는 것이 아니라, **대조군과 실험군을 두어서 동시에 성능을 평가**한다. 이때 중요한 것은 대조군과 실험군의 환경은 최대한 동일하게 맞춰줘야 한다.

![image](https://user-images.githubusercontent.com/91870042/158005511-28249301-68aa-41b7-bc91-b777b6752cf4.png){: .align-center width="70%"}

실제 현업에서는 의사결정에 사용되는 지표는 모델의 성능이 아니라 **매출 또는 CTR등의 비즈니스 / 서비스 지표를 사용**한다.
