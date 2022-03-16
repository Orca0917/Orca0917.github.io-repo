---
title:  "[Recommender System] ALS와 Bayesian Personalized Ranking"
excerpt: "Implicit Feedback 데이터를 사용한 MF와 베이즈 정리를 사용한 BPR에 대해서 알아보자"

categories:
  - RecSys
tags:
  - [AI, Naver, BoostCamp, Recommender System]
toc: true
toc_sticky: true
 
date: 2022-03-14 09:00:00
last_modified_at: 2022-03-14 09:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# MF for Implicit Feedback

지금까지 알아본 Matrix Factorization은 유저가 아이템에게 남긴 평점 정보인 Explicit Feedback을 통한 예측을 진행했었다. 이제는 **Implicit Feedback을 MF에 사용하는 방법**에 대해서 알아보도록 하자. 

Implicit Feedback데이터에 적합하도록 MF 기반 모델을 설계하여 성능을 향상시킨 논문인 `Collaborative Filtering for Implicit Feedback Datasets` 가 있다.

![image](https://user-images.githubusercontent.com/91870042/158059853-b9fea6ac-f5a9-483b-bf6f-2bb885689012.png){: .align-center width="70%"}

이제 이 논문에서 사용한 MF의 학습방법인 Alternating Least Square(ALS)에 대해서 알아보자. ALS는 이 논문 뿐만아니라 Matrix Factorization을 학습시킬때 가장 많이 사용하는 학습방법이다.

## Alternating Least Square (ALS)

이전까지는 일반 MF모델을 학습시키기 위해서 SGD를 사용했었다. 하지만, SGD는 유저의 차원이 늘어나고, 데이터의 양이 많아지면 그만큼 연산량이 많아져서 학습속도가 느려지는 문제점이 있었다.

![image](https://user-images.githubusercontent.com/91870042/158164470-628ce82e-7dc3-4826-9ae5-33876c8195e0.png){: .align-center width="90%"}

SGD는 유저 행렬과 아이템 행렬을 동시에 업데이트 했었다면, **ALS는 두 행렬을 번갈아가면서 업데이트를 진행**한다. 두 행렬 중 하나를 상수화시켜서 나머지 하나를 업데이트 하는 것이다. 이것은 least-square 문제를 푸는것과 같다.

ALS를 사용해 학습하면, **SGD보다 Sparse한 데이터에 대해 더 강건하게 학습**을 할 수 있다. 이는 실제 관측되어서 학습에 사용할 데이터가 적어도 잘 학습을 할 수 있음을 말한다. 또한 SGD와 달리 **대용량 데이터를 병렬처리하여 빠른학습이 가능**하다.

### ALS Objective Function

$$ L=\sum(r_{u,i}-p_u^Tq_i)^2+\lambda(\|p_u\|_2^2+\|q_i\|_2^2) $$

ALS의 목적함수는 MF의 기본 목적함수와 동일하지만 업데이트 하는 방법이 다르며 아래의 수식을 사용해서 $P, Q$를 서로 번갈아가면서 업데이트를 진행한다. 아래 식을 사용하면 단 한번의 행렬연산으로 $P$ 나 $Q$ 의 해를 구할 수 있게 된다. (위의 식을 유저와 아이템에 대해 각각 미분해서 0이 되는 지점을 찾은 값)

$$
\begin{aligned}
    p_u=(Q^TQ+\lambda I)^{-1}Q^Tr_u\\
    q_i=(P^TP+\lambda I)^{-1}P^Tr_i
\end{aligned}
$$

### MF for Implicit Feedback

유저가 해당 아이템을 선호하는가의 여부 `Preference`$(f)$ 를 먼저 이진값으로 표현한다. Implicit Data에서는 데이터가 존재함과 존재하지 않음을 나타내기 때문에 binary 하게 나타낸다.  

그리고 신뢰도를 나타내는 `Confidence`$(c)$도 생성한다. Preference에서 평점이 없는 데이터는 모두 0으로 바꾸어주었다. 하지만 여기서 평점을 남기지 않았는데 선호한 아이템도 분명히 있을 것이다. 논문에서는 이 문제를 해결하기 위해 신뢰도(confidence)를 도입하여 평점을 남기지 않은 아이템도 손실함수에 영향을 주도록 만들었다. (하지만 적은 영향이 가도록 조절!)

$$ f_{ui}=\left\{\begin{matrix} 1\quad r_{ui} > 0\\ 0\quad r_{ui} = 0 \end{matrix}\right. \qquad c_{ui}=1+\alpha\cdot r_{ui} $$

- $f_{ui}$ : 유저 $u$ 가 아이템 $i$ 에 대해 평점을 남겼는가?
- $c_{ui}$ : 유저 $u$ 가 아이템 $i$ 에 남긴 평점에 대한 신뢰도 (평가를 하지 않아도 영향을 준다)

여기서 $\alpha$ 값은 positive feedback과 negative feedback간의 상대적인 중요도를 조정하는 하이퍼 파라미터이다. 지금 이 confidence 항을 기존의 ALS 목적함수에 추가하면 MF에서 Confidence level 을 추가한 식과 동일하게 나타난다.

$$ \underset{P,Q}{\min}\sum_{\text{observed }r_{ui}}c_{ui}(f_{ui}-p_u^Tq_i)^2+\lambda(\|p_u\|_2^2+\|q_i\|_2^2) $$

그리고 학습에 사용되는 파라미터를 업데이트 하는 식에 Confidence 와 Preference를 추가한다. 위의 식을 유저와 아이템 각각에 대해서 미분해서 0이 되는 지점을 찾으면 아래와 같은 식을 얻을 수 있다. 미분과정에 대해서 자세한 설명은 [갈아먹는 추천 알고리즘 [4] Alternating Least Squares](https://yeomko.tistory.com/4)에 정말 자세하게 나와있다. 따라가는게 어렵지 않으니 한 번 보는 것을 추천한다.

$$
\begin{aligned}
  p_u=(Q^TC^uQ+\lambda I)^{-1}Q^TC^uf_u\\
  q_i=(P^TC^iP+\lambda I)^{-1}P^TC^if_i
\end{aligned}
$$

<br/>

# Bayesian Personalized Ranking

## BPR의 개념

![image](https://user-images.githubusercontent.com/91870042/158059947-93dff3fd-3cc0-4f43-a5ff-c1fd283ce466.png){: .align-center width="90%"}

BPR은 논문 BPR: Bayesian Personalized Ranking from Implicit Feedback 에서 제시된 내용으로, Implicit Feedback 데이터를 사용한 Matrix Factorization 모델을 학습할 수 있는 새로운 관점을 제시한 논문이다. BPR은 **베이지안 추론에 기반**하고 있으며 Implicit Feedback을 통해서 서로 다른 아이템에 대한 유저의 선호도를 반영하도록 모델링을 진행한다.

### Personalized Ranking

아이템을 추천하는 문제는 사용자에게 순서가 있는 아이템 리스트를 만들어서 제공하는 것과 동일하다. 그렇기에 개인화된 아이템 순위를 반영한 리스트를 만드는 것이 중요하다. 예를 들어 아이템 $i$ 보다 아이템 $j$ 를 더 선호한다면, 이 정보를 사용해서 Matrix Factorization 모델을 학습시키는 것이다. 이러한 순위를 만드는 것을 유저 $u$ 에 대한 **Personalized Ranking** 이라 부른다.

> Implicit Feedback을 사용해서 아이템 간의 순위를 매기는 방법?

사용자의 클릭, 구매 등의 로그는 Implicit Feedback 데이터이며 평점과 같이 아이템에 대한 선호의 정도가 분명하게 드러나지 않는 데이터이다. Implicit Feedback은 이렇게 '아이템을 관측했다' 의 정보만 가지고 있을 뿐이다. 그러면 유저가 관측하지 않은 아이템은 유저가 왜 보지 않은 것일까? 크게 다음 2가지로 관측하지 않은 이유를 나타내볼 수 있다.

- 유저가 아이템에 진짜 관심이 없어서
- 유저가 실제로는 관심이 있지만 해당 아이템의 존재를 몰라서

### 접근

기존의 MF방식은 관측하지 않은 데이터에 대해서는 고려를 하지 않아 모두 관심을 보이지 않는다라 하여 계산을 진행했다. 다음은 기존의 MF 방식이 연산한 과정을 보인다. (+ : 관측한 데이터, ? : 관측하지 않은 데이터)

![image](https://user-images.githubusercontent.com/91870042/158527317-4a3ce144-8abf-434d-9c54-08e8fd127d51.png){: .align-center width="90%"}

이러면 *실제로는 관심이 있지만 해당 아이템의 존재를 모르는 경우* 를 고려하지 못하게 된다. BPR은 이 문제를 해결하고자 관측된 아이템과 관측되지 않은 아이템에 대해서 다음 2가지 가정을 하고 출발한다.

-  **"관측된 아이템을 관측하지 않은 아이템보다 선호한다"**
-  **"서로 같은 부류의 아이템(관측한 아이템끼리, 관측하지 않은 아이템끼리)간의 선호도는 추론을 할 수가 없다"**

이런 접근 방식은 관측하지 않은 아이템에 대해서도 임의의 선호도를 부여해서 학습할 수 있고, 그 결과 관측하지 않은 것들도 순위를 매겨서 나타낼 수가 있다. 

![image](https://user-images.githubusercontent.com/91870042/158527895-ca08d526-447b-4ee7-ac73-21123ec118d8.png){: .align-center}

이번엔 학습을 위한 데이터셋을 생성하는 과정을 살펴보자. 유저 $u$ 가 선호하는 아이템들을 $I_u^+$ 라고 하고, $i$ 를 선호하는 아이템, $j$ 를 선호하지 않는 아이템이라고 했을 때 다음처럼 나타내 볼 수 있다. ($\backslash$ 는 차집합을 의미)

$$ D_s := \{(u,i,j)\mid i\in I_u^+ \wedge j\in I \backslash I_u^+ \} $$

### 최대 사후확률 추정 (Maximum A Posterior)

$$ p(\Theta \mid >_u) \propto p(>_u\mid \Theta)p(\Theta) $$

- 사전 확률(prior): $p(\Theta)$ , 파라미터에 대한 사전 정보
- 가능도 (likelihood): $p(>_u\mid \Theta)$, 주어진 파라미터에 대한 유저의 선호 정보의 확률
- 사후 확률(posterior): $p(\Theta \mid >_u)$, 주어진 유저의 선호 정보에 대한 파라미터의 확률

Bayesian optimization은 위의 사후 확률을 최대화 하는 파라미터 $\Theta$ 를 찾는 것을 말한다. 사후 확률을 직접적으로 계산하는 것은 어렵기 때문에 그 값에 근사할 수 있는 **사전확률과 가능도의 곱으로 연산**을 한다. 그리고 여기서 찾은 $\Theta$ 값은 주어진 유저 선호정보를 최대한 잘 나타내는 파라미터를 말한다.

### 가능도 (likelihood)

모든 유저의 선호정보 $>_u$ 는 다음과 같이 나타낼 수 있다.

$$ \prod_{u\in U}p(>_u|\Theta) = \prod_{u, i, j\in D_s}p(i>_u j)=\prod_{u, i, j\in D_s}\sigma( \hat{x}_{uij}(\Theta)) $$

여기서 $p(i >_u j)$ 는 유저 $u$ 가 아이템 $j$ 보다 $i$ 를 선호할 확률을 말한다. 그리고 유저의 벡터를 $p_u$, 아이템 $i$ 의 벡터를 $q_i$ 라고 한다면, 유저 $u$ 가 아이템 $j$ 보다 $i$ 를 좋아할 확률은 다음과 같다.

$$ p(i>_u j):=\sigma(\hat{x}_{uij}(\Theta)), \qquad \sigma(x):=\frac{1}{1+e^{-x}} $$

$$ \hat{r}_{ui}=p_u^Tq_i,\quad \hat{r}_{uj}=p_u^Tq_j,\quad \hat{x}_{uij}=\hat{r}_{ui}-\hat{r}_{uj}=p_u^Tq_i-p_u^Tq_j $$

### 사전확률 (prior)

사전확률은 학습해야할 파라미터에 대한 사전확률을 말하는데 이 논문에서는 정규분포를 따른다고 가정하였다. 정규분포는 평균이 모두 0이고 공분산 행렬이 $\Sigma_\Theta$ 인 정규분포를 말한다.

MF에서 유저와 아이템에 대한 정보는 벡터로 표현되었기 때문에 정규분포를 표현할 때도 벡터방식으로 표현이 이루어져야 한다. $\lambda_\Theta I$ 는 모델링할 때 정해야 하는 하이퍼파라미터로 정규화를 진행하게 한다. 바로 아래에서는 이 부분이 어떻게 정규화를 유도하는지 소개한다.

$$ p(\Theta)\sim N(0, \Sigma_\Theta)=N(0, \lambda_\Theta I) $$

## BPR의 학습 및 추론

### BPR의 목적함수


위에서 가능도(likelihood)와 사전확률(prior)에 대해서 정의했으므로, 이제는 사후확률을 최대화 시키는 파라미터 $\Theta$ 를 구할 차례이다. 

$$ \text{BPR-OPT}: \arg\max_\Theta p(\Theta \mid >_u) $$

- $\Theta$ : $p_u, q_i$ (user latent vector, item latent vector)
- $\hat{x}\_{uij} := \hat{x}_{ui} - \hat{x}\_{uj} = p_uq_i^T-p_uq_j^T$

![image](https://user-images.githubusercontent.com/91870042/158531173-1aa71b89-1327-432f-ba41-3bf529c42ffc.png){: .align-center width="90%"}

가장 마지막 수식의 $\lambda_\Theta$ 가 L2-정규화가 되면서 정규화를 하도록 도와주게 되며 앞은 가능도 (likelihood)가 된다.

### LEARNBPR

위의 수식은 미분이 가능하기 때문에 경사하강법을 이용해서 학습을 진행할 수 있다. 하지만 논문에서는 경사하강법을 사용한 학습이 적절한 학습방법이 아니라고 소개한다. 논문에서는 `LEARNBPR`라는 학습 방법을 소개하였다.

LEARNBPR은 **Bootstrap 기반의 SGD를 사용**한다. 위에서 학습에 사용하는 데이터셋을 다음과 같이 정의했었다.

$$ D_s:= \{(u,i,j)|i\in I_u^+ \wedge j\in I \backslash I_u^+ \} $$

이 데이터셋의 문제점은 관측한 아이템 $i$ 보다 관측되지 않은 아이템 $j$ 가 훨씬 많아서 **학습의 불균형(비대칭)이 발생**할 수 있다는 것이다. 불균형이 발생되면 동일한 유저 $u$ 와 아이템 $i$ 에 대해서 계속 업데이트가 되어서 수렴이 되지 않게 된다.

이문제를 해결하기 위해서 LEARNBPR은 $u,i,j$ 단위로 랜덤 샘플링을 진행해서 $i$ 와 $j$ 의 비대칭 학습을 해소하였다. 동일한 유저와 아이템도 나오지 않기 때문에 성능도 우수하였다.

![image](https://user-images.githubusercontent.com/91870042/158530432-0b4f7a07-092d-474d-84e1-a6df8d6cb73d.png){: .align-center width="70%"}

최종적으로 이를 Matrix Factorization에 적용해서 파라미터를 업데이트 하는 과정은 다음과 같다.

![image](https://user-images.githubusercontent.com/91870042/158530811-6b51a61f-4836-44de-9564-949b6453e34e.png){: .align-center width="70%"}


### BPR 요약

- Implicit Feedback 데이터만을 활용해 아이템 간의 선호도 도출
- Maximum A Posterior 방법을 통해 파라미터를 최적화
- LEARNBPR이라는 Bootstrap 기반의 SGD를 활용해 파라미터를 업데이트
- Matrix Factorization에 BPR Optimization을 적용한 결과 성능이 우수
