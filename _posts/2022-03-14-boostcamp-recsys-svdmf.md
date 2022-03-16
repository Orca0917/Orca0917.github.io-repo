---
title:  "[Recommender System] Singular Value Decomposition과 Matrix Factorization"
excerpt: "2차원 행렬을 분해하는 SVD와 SVD를 응용한 MF에 대해서 알아보자"

categories:
  - RecSys
tags:
  - [AI, Naver, BoostCamp, Recommender System]
toc: true
toc_sticky: true
 
date: 2022-03-14 08:00:00
last_modified_at: 2022-03-14 08:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Singular Value Decomposition

## SVD의 개념

**SVD는 2차원 행렬을 분해하는 방법**이다. User와 Item의 관계를 나타내는 행렬($R$) 이 있다고 할 때, **잠재요소**를 나타내는 행렬을 포함하도록 분해하는 것이다.

![image](https://user-images.githubusercontent.com/91870042/158414212-8ea4d6c4-bb15-4896-b076-c0da73b0597c.png){: .align-center width="90%"}

기존 유저와 아이템의 행렬정보를 $R$ 이라고 했을 때, 유저-잠재요인 $U$, 잠재요인의 중요도를 나타내는 $\Sigma$, 잠재요인-아이템 $V^T$ 행렬로 분해해서 나타내는 것을 말한다.

$$ R = U\Sigma V^T $$

- $U$ : 유저와 잠재요인의 관계
- $V$ : 아이템과 잠재요인의 관계
- $\Sigma$ : 잠재요인의 중요도를 나타내는 대각행렬

위에서 잠재요인의 전체를 사용하지 않고 K개의 대표 특이치를 사용해서 원래의 행렬을 근사시키는 것을 **Truncated SVD**라고 부른다.

![image](https://user-images.githubusercontent.com/91870042/158415879-89ad3094-9ddb-4899-8d6f-e278829d2eab.png){: .align-center width="90%"}


$$ R\approx\widehat{U}\,\widehat{\Sigma_k}\,\widehat{V^T}=\hat{R} $$

Truncated SVD는 몇 개의 특이치만을 사용해서 유용한 정보를 유지할 수 있다고 보며, 분해된 행렬이 부분 복원되면서 가장 중요한 정보로 요약이 된다. 이때 이 **$k$ 개의 Latent Factor는 유추만 할 수 있을 뿐, 각각이 무엇을 의미하는지는 알 수가 없다**.

## SVD의 한계

분해하려는 행렬의 결측치가 너무 많고 불완전할 때는 정의할 수 없다. 실제 대부분의 데이터는 Sparse한 행렬이기 때문에 사용하기가 어렵다. 그렇기 때문에 빈 공간을 채우는 Imputation 과정을 진행해야 한다. 

그러나 이 과정을 거치면 계산량이 많아지기 때문에 많은 계산 비용을 요구하며, 정확하지 않은 Imputation은 데이터를 왜곡시키고 예측의 성능을 낮춘다. 그렇기 때문에 SVD의 원리를 차용하는 다른 접근 방법이 필요해졌다.

<br/>

# Matrix Factorization

## MF 문제 정의

유저-아이템의 행렬을 저차원의 유저와 아이템 각각의 잠재벡터와의 행렬곱으로 분해하는 방법이 SVD였는데, 이 SVD를 계산하기 위해서는 Imputation 과정을 필요로 했다. 따라서 **Matrix Factorization은 유저-아이템의 행렬에서 비어있는 값은 보지않고, 관측된 값만 학습에 사용**하도록 하였다.

### 기본 MF 모델

유저-아이템의 행렬을 이제부터 $R$이라고 표기하겠다. 이 Rating Matrix $R$을 $P$와 $Q$로 분해하여 $R$과 최대한 가까운 $\hat{R}$을 추론하는 방법을 사용했다.

$$ R\approx P\times Q^T = \hat{R} $$

![image](https://user-images.githubusercontent.com/91870042/158417480-08265642-6f33-4c8a-8c3a-0175330e6fb1.png){: .align-center width="90%"}


### Objective Function

유저-아이템 행렬 $R$ 에서 특정 유저 $u$ 와, 특정 아이템 $i$ 의 값을 $r_{u,i}$ 라고 표현하면, 예측값과 실제값의 차이 제곱의 합을 최소로 하는 SSE 방식을 사용해서 아래와 같이 나타내볼 수 있다.

$$ \underset{P,Q}{\min}\sum_{\text{observed }r_{u,i}}(r_{u,i}-p_u^Tq_i)^2 $$

여기서 **$P$ 와 $Q$ 는 학습이 가능한 파라미터이며 하나 주목할 것은 SVD와 Ranking 행렬에서 값이 있는 것만 사용을 했다는 것**이다.

더 나아가서, 여기에 Overfitting이 되는 것을 막기 위한 `Penalty Term`을 추가해볼 수 있다. 여기서는 `L2-norm`을 사용한 정규화를 진행한다.

$$ \underset{P,Q}{\min}\sum_{\text{observed }r_{u,i}}(r_{u,i}-p_u^Tq_i)^2+\lambda(\|p_u\|_2^2+\|q_i\|_2^2) $$

### 정규화

Weight를 손실함수에 넣어주면, Weight값이 너무 커지지 않도록 막아주는 역할을 해서 Overfitting을 방지할 수 있다. 앞에 곱해주는 상수값 ($\lambda$) 에 의해서 받는 영향력이 달라진다. 하지만 이 값이 너무 커지게 되면 weight가 제대로 변하지 않아서 학습이 잘 되지 않는 Underfitting 현상을 볼수도 있다.

정규화 하는 방법에는 L1-norm, L2-norm을 사용한 정규화 방법이 있다.

![image](https://user-images.githubusercontent.com/91870042/158023414-62fbf83a-bbbe-4f77-90f4-29c2bb5468c9.png){: .align-center width="70%"}


## MF의 학습과정

### SGD: 확률적 경사하강법

확률적 경사하강법은 학습셋에서 전체 데이터를 사용하는 것이 아니라, 미니배치를 사용해서 부정확하지만 빠르게 목표지점을 향해서 내려가는 기법을 말한다. 자세한 내용은 [여기](https://killerwhale0917.github.io/aimath/boostcamp-math-gradient-descent2/#%ED%99%95%EB%A5%A0%EC%A0%81-%EA%B2%BD%EC%82%AC%ED%95%98%EA%B0%95%EB%B2%95)에서 확인할 수 있다.

### MF 모델에서의 SGD

먼저 MF 모델에서 사용하는 `손실함수`와 `Error Term`이 무엇인지 다시 한 번 나타내보면 다음과 같다.

$$ L=\sum(r_{u,i}-p_u^Tq_i)^2+\lambda(\|p_u\|_2^2+\|q_i\|_2^2) $$

$$ e_{ui}=r_{ui}-p_u^Tq_i $$

위의 식을 각각의 파라미터 $p_u$와 $q_i$로 편미분 한 값을 Gradient의 반대방향으로 업데이트를 진행하면 된다.

$$
\begin{aligned}
    \frac{\partial L}{\partial p_u}&=\frac{\partial (r_{ui}-p_u^Tq_i)^2}{\partial p_u}+\frac{\partial \lambda \|p_u\|_2^2}{\partial p_u}\\
    &=-2(r_{ui}-p_u^Tq_i)q_i+2\lambda p=-2(e_{ui}q_i-\lambda p_u)
\end{aligned}
$$

$$
\begin{aligned}
\\
    p_u\leftarrow p_u+\eta \cdot(e_{ui}q_i-\lambda p_u)\\
    q_i\leftarrow q_i+\eta \cdot(e_{ui}p_u-\lambda q_i)\\
\end{aligned}
$$


## MF와 추가적인 기법들

[Matrix Factorization Techniques for Recommender Systems](https://ieeexplore.ieee.org/abstract/document/5197422)는 MF기반 추천으로 가장 널리 알려진 논문이며 기본적이 MF에 다양한 테크닉을 추가해서 성능을 향상시켰다. 아래에서는 이 논문에서 사용한 다양한 기법들에서 살펴보고자 한다.

### ✔ Adding Biases

생각을 해보면 **모든 유저는 아이템에 대해서 동일한 기준으로 평점을 주는 것은 아니다**. 어떤 유저는 짜게 주는 사람도 있고 어떤 유저는 후하게 높게 평점을 주는 사람도 있다. 반대로 아이템도 위와 같은 편향을 가질수가 있다. 그렇기 때문에 전체의 평균과 유저와 아이템의 `bias값`을 추가해서 예측 성능을 높이는 것이다. 그래서 기존의 목적함수에 Bias를 추가해보면 다음 처럼 나타낼 수 있다.

$$ \underset{P,Q}{\min}\sum_{\text{observed }r_{u,i}}(r_{u,i}-\mu - b_u-b_i-p_u^Tq_i)^2+\lambda(\|p_u\|_2^2+\|q_i\|_2^2+b_u^2+b_i^2) $$

위에서 나타낸 수식 중에서 모든 데이터에 대한 평균값 ($\mu$)는 학습이되는 파라미터가 아니라 처음에 평균값을 구한 값이다. 추가로 학습이 이루어지는 파라미터는 bias정보인 $b_u, b_i$ 이다. 이 추가된 파라미터 2개를 합해서 총 4개의 파리미터를 업데이트 하는 식을 다음처럼 작성해볼 수 있다.

$$
\begin{aligned}
    b_u \leftarrow b_u+\gamma \cdot (e_{ui}-\lambda b_u)\\
    b_i \leftarrow b_i+\gamma \cdot (e_{ui}-\lambda b_i)\\
    p_u \leftarrow p_u+\gamma \cdot (e_{ui}q_i-\lambda p_u)\\
    q_i \leftarrow q_i+\gamma \cdot (e_{ui}p_u-\lambda q_i)
\end{aligned}
$$

### ✔ Adding Confidence Level

실생활에서 **모든 평점이 의미있는 평점은 아니라**는 것을 알 수 있다. 댓글 알바와 같은 것을 통한 조작된 것일 수도 있고 많은 광고를 통해 사람들이 몰려서 클릭이나 조회수가 높아지는 것일 수도 있다. 이럴 경우 각 평점 $r_{u,i}$ 에 대한 `신뢰도`를 의미하는 $c_{u,i}$ 를 추가해볼 수 있다.

$$ \underset{P,Q}{\min}\sum_{\text{observed }r_{u,i}}c_{u,i}(r_{u,i}-\mu - b_u-b_i-p_u^Tq_i)^2+\lambda(\|p_u\|_2^2+\|q_i\|_2^2+b_u^2+b_i^2) $$

### ✔ Adding Temporal Dynamics

이 기법은 시간에 따라서 변화하는 유저와 아이템의 특성을 반영하고자 했다. 아이템은 시간이 지남에 따라서 인기도가 떨어지고, 유저는 시간이 흐르면서 평점을 내리는 기준이 엄격해지게 되었다. 따라서 파라미터가 시간을 반영하도록 모델을 설계하는 것이다.

$$ \widehat{r_{u,i}(t)}=\mu+b_u(t)+b_i(t)+p_u^Tq_i(t) $$
