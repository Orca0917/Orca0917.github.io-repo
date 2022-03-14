---
title:  "[Recommender System] KNN 협업필터링과 점수 예측방법"
excerpt: "KNN 협업필터링에 대한 소개와 아이템 또는 유저간의 유사도를 구하는 방법과 실제로 점수를 예측하는 방법에 대해서 알아보자"

categories:
  - RecSys
tags:
  - [AI, Naver, BoostCamp, Recommender System]
toc: true
toc_sticky: true
 
date: 2022-03-14 06:00:00
last_modified_at: 2022-03-14 06:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# KNN CF, Similarity Function

KNN 협업필터링 (K-Nearest Neighbors CF, KNN CF)의 원리를 이해하고 유저나 아이템간의 유사도를 측정하는 방법에 대해서 알아보자

## KNN CF

### NBCF의 한계

아이템 $i$에 대해서 평가를 한 유저집합을 $\Omega_i$라고 했을 때, 아이템 $i$에 대한 평점을 예측하기 위해서는 $\Omega_i$에 속한 모든 유저와의 유사도를 구해야 한다. 하지만, 이때 **유저의 수가 너무 많다면 연산량이 많고 성능이 떨어지기도 한다**.

이때 유저의 수가 많은데 그 성능이 떨어지는 이유로 별점 알바와 같은 것을 예로들 수 있다. 모든 사람이 남긴 평점에 신뢰도가 높지 않기 때문에 이와 같은 일이 발생한다.

### KNN CF의 아이디어

![image](https://user-images.githubusercontent.com/91870042/158017313-a55517c0-ab28-433b-b64a-f8753b941d96.png){: .align-center width="50%"}
<p align="center"><i>K=3인 경우의 KNN CF from wikipedia</i></p>

$\Omega_i$에 속한 유저 중에서 유저 $u$와 **가장 유사한 K명의 유저만을 사용**해서 평점을 예측하는 것이다. 일반적으로 K의 값은 25~50의 값을 사용하며 이는 직접 튜닝을 진행해야 하는 하이퍼파라미터이다.

하지만 여기서 가장 유사한 $K$명을 뽑기 위해서 다른 모든 유저와의 유사도를 구해야하야 하는 것이라고 생각이 들 수 있다. 이 문제를 해결하기 위한 ANN의 기법이 있고 이 알고리즘에 대해서는 다른 포스팅에서 작성하려고 한다.

## Similarity Measure

![image](https://user-images.githubusercontent.com/91870042/158017255-6963251e-6180-4076-a13d-b578808ea0c0.png){: .align-center width="50%"}
<p align="center"><i>Various ML metrics from Towards Data Science</i></p>

두 아이템 또는 유저간의 유사도를 측정하는 방법에 대해서 알아보자. 유사성을 측정하는 방법에는 여러가지가 있지만, 일반적으로 **거리의 역수 개념**을 사용한다. 거리가 멀수록 유사도는 작아지고, 거리가 가까울수록 유사도가 높아진다. 여기서 중요한 것은 **거리를 어떻게 측정하는지에 따라서 유사도 측정방법이 달라지는 것**이다.

### Mean Squared Difference Similarity

주어진 유저-아이템 rating matrix에 대해서 계산을 진행한다. 유저 또는 아이템에 대해서 MSDS의 수식을 다음과 같이 나타낼 수 있다.

$$ \text{msd}(u,v)=\frac{1}{|I_{uv}|}\cdot\sum_{i\in I_{uv}}(r_{ui}-r_{vi})^2, \qquad \text{msd-sim}(u,v)=\frac{1}{\text{msd}(u,v)+1} $$

$$ \text{msd}(i,j)=\frac{1}{|U_{ij}|}\cdot\sum_{u\in U_{ij}}(r_{ui}-r_{uj})^2, \qquad \text{msd-sim}(i,j)=\frac{1}{\text{msd}(i,j)+1} $$

유사도는 유클리드 거리에 반비례하는 성질을 갖고 있으며 분모가 0이 되는 것을 방지하기 위해서 1을 더해준다.

### Cosine Similarity

추천시스템에서 가장 많이 사용하는 유사도로 자세한 내용은 [여기](https://killerwhale0917.github.io/recsys/boostcamp-recsys-tfidf/#cosine-similarity)에 작성되어 있다.

### Pearson Similarity (Pearson Correlation)

피어슨 유사도는 피어슨 상관계수를 사용하여 계산한다. 주어진 두 벡터 $X, Y$에 대해서 다음과 같이 나타낼 수 있다.

$$ \text{pearson-sim}(X, Y)=\frac{\sum_{i=1}^{N}(X_i-\overline{X})(Y_i-\overline{Y})}{\sqrt{\sum_{i=1}^{N}(X_i-\overline{X})^2}\sqrt{\sum_{i=1}^{N}(Y_i-\overline{Y})^2}} $$

각 벡터를 표본평균으로 정규화하여 코사인 유사도를 구하는 값이다. 표본평균으로 할 경우, **평점의 크기차이를 보정해줄 수 있다**. 반대로 코사인 유사도는 평점의 크기가 달라도 유사도가 높게 나온다.

직관적으로 해석하면 (X와 Y가 함께 변하는 정도) / (X와 Y가 따로 변하는 정도) 라고 나타낼 수 있다. 결과값이 1에 가까우면 양의 상관관계, 0일 경우 독립, -1에 가까울수록 음의 상관관계를 나타낸다.

### Jaccard Similarity

벡터가 아닌 집합을 사용해서 유사도를 구하는 방법이다. 두 집합이 얼마나 같은 집합을 공유하고 있는지를 계산하는 방법이다.

$$ J(A,B)=\frac{|A\cap B|}{|A\cup B|}=\frac{|A\cap B|}{|A|+|B|-|A\cap B|} $$

집합의 개념을 사용하기 때문에 코사인 유사도나 피어슨 유사도와 달리 **길이(차원)이 달라도 이론적으로 유사도를 계산하는 것이 가능**하다.

<br/>

# Rating Prediction

## UBCF - Absolute / Relative Rating

### Average

아이템 $i$ 에 대한 다른 유저들의 평점들의 평균을 내서 예측하는 것을 말한다. 하지만, 나와 유사도가 높은 사람의 평점을 더 많이 반영하는 것이 더 효과적이기 때문에 바로 Weighted Average의 개념이 나왔다.

![image](https://user-images.githubusercontent.com/91870042/158016213-7d0a889a-af8e-4c19-ab7d-0443d4bb5ac4.png){: .align-center width="70%"}

### Weighted Average

나와 다른 유저간의 유사도를 반영해서 평점을 예측하는 것을 말한다. 나와 비슷한 취향을 갖는 유저의 평점 정보는 더 많이 반영한다. 아래의 예시에서는 각 유저와의 유사도를 0.95, 0.6, 0.85라고 가정한 것이다.

![image](https://user-images.githubusercontent.com/91870042/158016220-b4a21f8e-8641-46b9-a0c0-b370deac0633.png){: .align-center width="70%"}

$$ \frac{0.95\cdot5+0.6\cdot1+0.85\cdot3}{0.95+0.6+0.85}=3.3 $$

### Absolute Rating Formula

유저의 절대적 평점을 사용하는 방법으로, 방금 소개한 2가지 Average, Weighted Average 기법이 Absolute Rating Formula에 속한다. 전체 유저 $U$와 전체 아이템 $I$에 대한 평점 데이터가 존재할 때, 유저 $u$와 아이템 $i$에 대한 평점 $\hat{r}(u,i)$를 예측하면 다음과 같다.

- Average

$$ \hat{r}(u,i)=\frac{\sum_{u'\in\Omega_i}r(u', i)}{|\Omega_i|} $$

- Weighted Average

$$ \hat{r}(u, i)=\frac{\sum_{u'\in\Omega_i}\text{sim}(u, u')\cdot r(u',i)}{\sum_{u'\in\Omega_i}\text{sim}(u, u')} $$

Absolute Rating의 한계로 유저가 주는 평점의 기준이 모두 제각각이라는 것이 있다. 어떤 유저는 전체적으로 높은 평점을 줄 수 있고, 반대로 어떤 유저는 평점을 상향평준화 해서 주는 경우가 있다. 이런 상황속에서 평균으로 점수를 계산하면 올바르지 못한 결과를 얻을 수 있다.

### Relative Rating Formula

유저의 상대적인 평점을 사용하는 것이다. 상대적인 정보를 이용하기 위해서 유저가 측정한 점수를 그대로 사용하지 않고, 그 유저의 평점 정보의 **편차를 사용**한다. 예를 들어, 어떤 유저의 평균 평점이 2.5점인데 5점을 책정하였으면 아주 높게 평점을 남긴것이고, 모든 아이템을 항상 5점으로 준 유저는 아이템간의 선호도 비교를 하기가 어려워진다.

그러면, 유저-아이템 행렬에서 평점에 대한 편차를 계산은 어떻게 해야할까. Relative Rating Formula는 모든 평점 데이터를 편차값으로 바꾸고 원래의 평점이 아닌 편차를 예측한다. 이후에 이 예측된 편차정보를 예측하고자 하는 유저의 평균평점에 더해준다.

$$ \text{Predicted Rating} = \text{User Average Rating} + \text{Predicted Deviation} $$

> 편차정보의 계산

$$ dev(u,i)=r(u,i)-\overline{r_u}\quad \text{for known rating} $$

> 편차정보를 사용해서 유저의 평점 예측하기

$$ \widehat{dev}(u,i)=\frac{\sum_{u'\in\Omega_i}dev(u',i)}{|\Omega_i|}=\frac{\sum_{u'\in\Omega_i}r(u',i)-\overline{r_{u'}}}{|\Omega_i|} $$

$$ \hat{r}(u,i)=\overline{r_u}+\frac{\sum_{u'\in\Omega_i}r(u',i)-\overline{r_{u'}}}{|\Omega_i|}=\overline{r_u}+\widehat{dev}(u,i) $$

## IBCF - Absolute / Relative Rating

UBCF의 계산방법과 동일하며 단순히 행과 열의 기준을 바꾸기만 하면 된다.

- Absolute Rating (Weighted Average)

$$ \hat{r}(u,i)=\frac{\sum_{i'\in\Phi_u}sim(i,i')\cdot r(u,i')}{\sum_{i'\in\Phi_u}sim(i,i')} $$

- Relative Rating

$$ \hat{r}(u,i)=\overline{r_i}+\frac{\sum_{i'\in\Phi_u}sim(i,i')\{ r(u,i')-\overline{r_{i'}}\}}{\sum_{i'\in\Phi_u}sim(i,i')} $$

## Top-N Recommendation

![image](https://user-images.githubusercontent.com/91870042/158017225-6502a9e4-05bc-4fdd-a1b7-d0ceddf72f62.png){: .align-center width="70%"}
<p align="center"><i>Item Recommendation in Coupang</i></p>

협업필터링의 최종목적은 유저 $u$가 아이템 $i$에게 부여할 평점을 예측하는 것이었다. 추천시스템의 목적은 그런 예측된 평점중에서 높은 평점의 아이템을 유저에게 추천하는 것이다. 그렇기 때문에 타겟 유저에 대한 아이템의 예측 평점 계산이 완료되면 높은 순으로 정렬하여 상위 $N$개만 뽑아서 추천하면 된다.
