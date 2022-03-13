---
title:  "[Recommender System] Collaborative Filtering 2"
excerpt: "모델을 기반으로 한 협업필터링 MBCF와 SVD의 원리 그리고 베이즈 정리를 사용한 BPR"

categories:
  - RecSys
tags:
  - [AI, Naver, BoostCamp, Recommender System]
toc: true
toc_sticky: true
 
date: 2022-03-12 02:00:00
last_modified_at: 2022-03-12 02:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Model Based Collaborative Filtering

## NBCF의 한계

바로 이전 포스팅에서는 이웃기반 협업필터링 (NBCF)를 살펴보았고 그에 문제점으로 `Sparsity`문제와 `Scalability`의 문제가 있다고 하였다. 이를 Matrix Factorization으로 해결할 수 있다고 했는데 그 전에 다시 한 번 각각의 문제가 무엇을 의미하는지 알아보자.

### Sparsity(희소성) 문제

![image](https://user-images.githubusercontent.com/91870042/158059313-8dc075c5-4431-4212-bbe4-4cd38e27b2f5.png){: .align-center width="70%"}

희소성 문제는 유저-아이템 행렬에 데이터가 채워져있지 않아서 부족한 것을 말한다. 데이터가 부족하면 **유사도의 계산이 부정확**해질 수 있기 때문에 추천 성능이 떨어지게 된다. 가장 처음에 데이터가 완전히 없는 경우에는 추천을 할 수가 없는 `Cold Start` 문제가 발생한다.

### Scalability(확장성) 문제

확장성 문제는 유저와 아이템이 점점 늘어날 수록 그 계산량이 많아져서 시간이 오래걸리게 되는 문제이다. 유저와 아이템이 많아야 정확한 예측을 할 수 있지만 시간이 오래걸려서 사용하기 힘들다는 단점이 있었다.

이런 문제를 모델을 기반으로 한 추천 시스템인 MBCF는 어떻게 해결할 수 있었는지 알아보자.

## Model Based Collaborative Filtering

![image](https://user-images.githubusercontent.com/91870042/158059594-209e5e8d-1ad0-41ec-9185-a893f44e8425.png){: .align-center width="70%"}

기존에는 유저나 아이템간의 유사성을 판단해서 추천을 진행하는 방식으로 진행했다면, 이번에는 **데이터가 내포하고 있는 패턴을 찾아서 추천에 이용**을 하는 것이다. 이 방법을 사용하는 협업필터링 과정이 MBCF이다. 

MBCF는 파라미터를 이용해서 학습하는 Parametric Machine Learning 을 사용하며, 주어진 데이터를 사용해서 모델을 학습한다. 학습을 진행하면서 데이터의 정보는 파라미터의 형태로 압축이 될 것이고, 이 모델의 파라미터는 결국 데이터의 패턴을 나타내게 되며 최적화를 통해 지속적인 업데이트가 이루어질 수 있다.

### MBCF의 특징

다시 말하지만, MBCF를 사용하면 데이터에 숨겨진 유저-아이템 관계의 잠재적 특성/패턴(Latent Factor)을 찾는 것이 가능하다.

NBCF와의 차이점과 비교하자면 NBCF는 유저 또는 아이템 벡터를 계산된 형태로 저장을 하지만, MBCF는 모두 학습을 통해서 변경되는 파라미터이다. 참고로, NBCF는 계산된 값을 저장하기 때문에 `Memory-Based Collaborative Filtering` 이라고도 한다.

현업에서는 Matrix Factorization 기법을 많이 사용하며 최근에는 MF의 원리를 딥러닝 모델에 응용하는 기법이 높은 성능을 보이고 있다.

### MBCF의 장점

1. *모델의 학습과 서빙*  
  유저-아이템 데이터는 학습에만 사용되고 학습된 모델은 데이터를 압축된 형태로 저장한다. 또한, 서빙을 진행하는 과정에서는 이미 학습된 모델을 통해서 추천을 하기 때문에 **서빙속도가 빠르다**.

2. *Sparsity와 Scalability 개선*  
  NBCF는 Sparse Ratio가 99.5% 이하일 때만 사용가능했지만, MBCF는 그 이상의 비율을 가지고 있어도 좋은 성능을 보인다. 사용자, 아이템의 개수가 많이 늘어나도 계산량이 많아서 느린 NBCF와 달리 좋은 추천 성능을 보이고 있다.

3. *Overfitting 방지*  
  NBCF는 특정 가까운 이웃에 의해서 크게 영향을 받을 수 있는 반면, MBCF는 **전체 데이터의 패턴을 학습**하도록 모델이 작동을 한다.

4. *Limited Coverage 극복*  
  NBCF는 공통의 유저와 아이템 벡터를 많이 공유해야 유사도의 값이 정확해질 수 있다. 또한 유사도의 값이 정확하지 않을 때는 이웃의 정보를 올바르게 사용하지 못할 수 있다. 하지만 MBCF는 전체 데이터를 사용해서 모델을 학습하므로 유사도의 값과 관련된 문제는 발생하지 않는다.

## Issue: Implicit Feedback

[여기](https://killerwhale0917.github.io/recsys/boostcamp-recsysbasic-1/#%EC%9C%A0%EC%A0%80-%EC%95%84%EC%9D%B4%ED%85%9C-%EC%83%81%ED%98%B8%EC%9E%91%EC%9A%A9-%EC%A0%95%EB%B3%B4)에서 설명했다시피, 존재하는 데이터는 Explicit Feedback보다 Implicit Feedback이 훨씬 더 많은 비율을 차지하고 있다. 이러한 데이터를 어떻게 잘 사용할지 고민해보아야 한다.

## Latent Factor Model

최근에는 Latent Factor보다는 Embedding 이라는 말을 더 많이 사용하고 있다. 유저와 아이템의 관계를 잠재적 요인으로 표현할 수 있다고 보는 모델로서 현실세계의 다양하고 복잡한 유저-아이템 관계를 몇 개의 벡터로 압축해서 표현하는 것을 말한다.

유저-아이템 행렬을 저차원의 행렬로 분해하는 방식으로 작동되며, 분해한 각 차원의 의미는 모델의 학습을 통해서 생성되며 표면적으로 각각이 무슨 의미를 갖는지는 알 수가 없다.

![image](https://user-images.githubusercontent.com/91870042/158022395-b4cef103-04e6-4d3f-af2d-184bcd5f4a52.png){: .align-center width="70%"}
<p align="center"><i>Latent Factor Model의 예시 (https://woono.tistory.com/149)</i></p>

생성한 유저와 아이템 벡터를 같은 공간에 나타냈을 때 유저와 아이템의 유사한 정보를 확인해볼 수 있다. 유저와 아이템 벡터가 가까이 놓일 경우 해당 유저에게 해당 아이템이 추천될 확률이 높아진다.

<br/>

# Singular Value Decomposition

## SVD의 개념

**SVD는 2차원 행렬을 분해하는 방법**이다. User와 Item의 관계를 나타내는 행렬이 있다고 할 때, **잠재요소**를 나타내는 행렬을 포함하도록 분해하는 것이다.

![image](https://user-images.githubusercontent.com/91870042/158022371-f37c9857-c929-4de5-84c3-23b4bb545fdc.png){: .align-center width="70%"}

기존 유저와 아이템의 행렬정보를 $$R$$ 이라고 했을 때, 유저-잠재요인 $$U$$, 잠재요인의 중요도를 나타내는 $$\Sigma$$, 잠재요인-아이템 $$V^T$$ 행렬로 분해해서 나타내는 것을 말한다.

$$ R = U\Sigma V^T $$

- $$U$$ : 유저와 잠재요인의 관계
- $$V$$ : 아이템과 잠재요인의 관계
- $$\Sigma$$ : 잠재요인의 중요도를 나타내는 대각행렬

위에서 잠재요인의 전체를 사용하지 않고 K개의 대표 특이치를 사용해서 원래의 행렬을 근사시키는 것을 **Truncated SVD**라고 부른다.

![image](https://user-images.githubusercontent.com/91870042/158022654-ad2bfceb-7517-47aa-92c2-74e21da7825f.png){: .align-center width="70%"}


$$ R\approx\widehat{U}\,\widehat{\Sigma_k}\,\widehat{V^T}=\hat{R} $$

몇 개의 특이치만을 사용해서 유용한 정보를 유지할 수 있다고 보며, 분해된 행렬이 부분 복원되면서 가장 중요한 정보로 요약이 되는 것이다. 이때 이 **k개의 Latent Factor는 유추만 할 수 있을 뿐, 각각이 무엇을 의미하는지는 알 수가 없다**.

## SVD의 한계

분해하려는 행렬의 결측치가 너무 많고 불완전할 때는 정의할 수 없다. 실제 대부분의 데이터는 Sparse한 행렬이기 때문에 사용하기가 어렵다. 그렇기 때문에 빈 공간을 채우는 Imputation 과정을 진행해야 한다. 이 과정을 거치면 계산량이 많아지기 때문에 많은 계산 비용을 요구하며, 정확하지 않은 Imputation은 데이터를 왜곡시키고 예측의 성능을 낮춘다. 그렇기 때문에 SVD의 원리를 차용하되, 다른 접근 방법의 필요성을 느끼게 되었다.

<br/>

# Matrix Factorization

## MF 문제 정의

유저-아이템의 행렬을 저차원의 유저와 아이템 각각의 잠재벡터와의 행렬곱으로 분해하는 방법이 SVD였는데, 이 SVD를 계산하기 위해서는 Imputation 과정을 필요로 했다. 따라서 Matrix Factorization은 유저-아이템의 행렬에서 비어있는 값은 보지않고, 관측된 값만 학습에 사용하도록 하였다.

### 기존 MF 모델

유저-아이템의 행렬을 이제부터 $$R$$이라고 표기하겠다. 이 Rating Matrix $$R$$을 $$P$$와 $$Q$$로 분해하여 $$R$$과 최대한 가까운 $$\hat{R}$$을 추론하는 방법을 사용했다.

$$ R\approx P\times Q^T = \hat{R} $$

![image](https://user-images.githubusercontent.com/91870042/158022883-da814304-87fb-4853-9763-fd0bcd46f942.png){: .align-center width="70%"}


### Objective Function

유저-아이템 행렬 $$R$$에서 특정 유저 $$u$$와, 특정 아이템 $$i$$의 값을 $$r_{u,i}$$라고 표현하면, 예측값과 실제값의 차이 제곱의 합을 최소로 하는 SSE 방식을 사용해서 아래와 같이 나타내볼 수 있다.

$$ \underset{P,Q}{\min}\sum_{\text{ovserved }r_{u,i}}(r_{u,i}-p_u^Tq_i)^2 $$

여기서 **$$P$$와 $$Q$$는 학습이 가능한 파라미터이며 하나 주목할 것은 SVD와 Ranking 행렬에서 값이 있는 것만 사용을 했다는 것**이다.

더 나아가서, 여기에 Overfitting이 되는 것을 막기 위한 `Penalty Term`을 추가해볼 수 있다. 여기서는 L2-norm을 사용한 정규화를 진행한다.

$$ \underset{P,Q}{\min}\sum_{\text{ovserved }r_{u,i}}(r_{u,i}-p_u^Tq_i)^2+\lambda(\|p_u\|_2^2+\|q_i\|_2^2) $$

### 정규화

Weight를 손실함수에 넣어주면, Weight값이 너무 커지지 않도록 막아주는 역할을 해서 Overfitting을 방지할 수 있다. 앞에 곱해주는 상수값 ($$\lambda$$) 에 의해서 받는 영향력이 달라진다. 하지만 이 값이 너무 커지게 되면 weight가 제대로 변하지 않아서 학습이 잘 되지 않는 Underfitting 현상을 볼수도 있다.

정규화 하는 방법에는 L1-norm, L2-norm을 사용한 정규화 방법이 있다.

![image](https://user-images.githubusercontent.com/91870042/158023414-62fbf83a-bbbe-4f77-90f4-29c2bb5468c9.png){: .align-center width="70%"}


## MF의 학습과정

### SGD: 확률적 경사하강법

확률적 경사하강법은 학습셋에서 전체 데이터를 사용하는 것이 아니라, 미니배치를 사용해서 부정확하지만 빠르게 목표지점을 향해서 내려가는 기법을 말한다. 자세한 내용은 [여기](https://killerwhale0917.github.io/aimath/boostcamp-math-gradient-descent2/#%ED%99%95%EB%A5%A0%EC%A0%81-%EA%B2%BD%EC%82%AC%ED%95%98%EA%B0%95%EB%B2%95)에서 확인할 수 있다.

### MF 모델에서의 SGD

먼저 MF 모델에서 사용하는 손실함수와 Error Term이 무엇인지 다시 한 번 나타내보면 다음과 같다.

$$ L=\sum(r_{u,i}-p_u^Tq_i)^2+\lambda(\|p_u\|_2^2+\|q_i\|_2^2) $$

$$ e_{ui}=r_{ui}-p_u^Tq_i $$

위의 식을 각각의 파라미터 $$p_u$$와 $$q_i$$로 편미분 한 값을 Gradient의 반대방향으로 업데이트를 진행하면 된다.

$$
\begin{aligned}
    \frac{\partial L}{\partial p_u}&=\frac{\partial (r_{ui}-p_u^Tq_i)^2}{\partial p_u}+\frac{\partial \lambda \|p_u\|_2^2}{\partial p_u}\\
    &=-2(r_{ui}-p_u^Tq_i)q_i+2\lambda p=-2(e_{ui}q_i-\lambda p_u)
\end{aligned}
$$

$$
\begin{aligned}
    p_u\leftarrow p_u+\eta \cdot(e_{ui}q_i-\lambda p_u)\\
    q_i\leftarrow q_i+\eta \cdot(e_{ui}p_u-\lambda q_i)\\
\end{aligned}
$$


## MF와 추가적인 기법들

[Matrix Factorization Techniques for Recommender Systems](https://ieeexplore.ieee.org/abstract/document/5197422)는 MF기반 추천으로 가장 널리 알려진 논문이며 기본적이 MF에 다양한 테크닉을 추가해서 성능을 향상시켰다.

### Adding Biases

생각을 해보면 **모든 유저는 아이템에 대해서 동일한 기준으로 평점을 주는 것은 아니다**. 어떤 유저는 짜게 주는 사람도 있고 어떤 유저는 후하게 높게 평점을 주는 사람도 있다. 반대로 아이템도 위와 같은 편향을 가질수가 있다. 그렇기 때문에 전체의 평균과 유저와 아이템의 `bias값`을 추가해서 예측 성능을 높이는 것이다.

기존의 목적함수에 Bias를 추가해보면 다음 처럼 나타낼 수 있다.

$$ \underset{P,Q}{\min}\sum_{\text{ovserved }r_{u,i}}(r_{u,i}-\mu - b_u-b_i-p_u^Tq_i)^2+\lambda(\|p_u\|_2^2+\|q_i\|_2^2+b_u^2+b_i^2) $$

위에서 나타낸 수식 중에서 모든 데이터에 대한 평균값 ($$\mu$$)는 학습이되는 파라미터가 아니라 처음에 평균값을 구한 값이다. 추가로 학습이 이루어지는 파라미터는 bias정보인 $$b_u, b_i$$ 이다.

추가된 파라미터 2개를 합해서 총 4개의 파리미터를 업데이트 하는 식을 다음처럼 작성해볼 수 있다.

$$
\begin{aligned}
    b_u \leftarrow b_u+\gamma \cdot (e_{ui}-\lambda b_u)\\
    b_i \leftarrow b_i+\gamma \cdot (e_{ui}-\lambda b_i)\\
    p_u \leftarrow p_u+\gamma \cdot (e_{ui}q_i-\lambda p_u)\\
    q_i \leftarrow q_i+\gamma \cdot (e_{ui}p_u-\lambda q_i)
\end{aligned}
$$

### Adding Confidence Level

실생활에서 **모든 평점이 의미있는 평점은 아니라**는 것을 알 수 있다. 댓글 알바와 같은 것을 통한 조작된 것일 수도 있고 많은 광고를 통해 사람들이 몰려서 클릭이나 조회수가 높아지는 것일 수도 있다. 이럴 경우 각 평점 $$r_{u,i}$$에 대한 신뢰도를 의미하는 $$c_{u,i}$$를 추가해볼 수 있다.

$$ \underset{P,Q}{\min}\sum_{\text{ovserved }r_{u,i}}c_{u,i}(r_{u,i}-\mu - b_u-b_i-p_u^Tq_i)^2+\lambda(\|p_u\|_2^2+\|q_i\|_2^2+b_u^2+b_i^2) $$

### Adding Temporal Dynamics

이 기법은 시간에 따라서 변화하는 유저와 아이템의 특성을 반영하고자 했다. 아이템은 시간이 지남에 따라서 인기도가 떨어지고, 유저는 시간이 흐르면서 평점을 내리는 기준이 엄격해지게 되었다. 따라서 파라미터가 시간을 반영하도록 모델을 설계하는 것이다.

$$ \widehat{r_{u,i}(t)}=\mu+b_u(t)+b_i(t)+p_u^Tq_i(t) $$

<br/>

# MF for Implicit Feedback

지금까지 알아본 Matrix Factorization은 유저가 아이템에게 남긴 평점 정보인 Explicit Feedback을 통한 예측을 진행했었다. 이제는 **Implicit Feedback을 MF에 사용하는 방법**에 대해서 알아보도록 하자. 

Implicit Feedback데이터에 적합하도록 MF 기반 모델을 설계하여 성능을 향상시킨 논문이 존재한다. `Collaborative Filtering for Implicit Feedback Datasets`

![image](https://user-images.githubusercontent.com/91870042/158059853-b9fea6ac-f5a9-483b-bf6f-2bb885689012.png){: .align-center width="70%"}

## Alternating Least Square (ALS)

일반 MF모델을 학습시키기 위해서는 SGD를 사용했었다. 하지만, SGD는 데이터의 양이 많아지면 그만큼 연산량이 많아져서 학습속도가 느려지게 된다.

SGD는 유저 행렬과 아이템 행렬을 동시에 업데이트 했었다면, **ALS는 두 행렬을 번갈아가면서 업데이트를 진행**한다. 두 행렬 중 하나를 상수화시켜서 나머지 하나를 업데이트 하는 것이다.

### SGD와의 비교

**SGD보다 Sparse한 데이터에 대해 더 강건하게 학습**을 할 수 있다. 이는 실제 관측되어서 학습에 사용할 데이터가 적어도 잘 학습을 할 수 있음을 말한다. 또한 SGD와 달리 **대용량 데이터를 병렬처리하여 빠른학습이 가능**하다.

### ALS Objective Function

ALS의 목적함수는 MF의 기본 목적함수와 동일하지만 업데이트 하는 방법이 다르다.

$$ L=\sum(r_{u,i}-p_u^Tq_i)^2+\lambda(\|p_u\|_2^2+\|q_i\|_2^2) $$

그리고 아래의 수식을 사용해서 $$P, Q$$를 서로 번갈아가면서 업데이트를 진행한다.

$$
\begin{aligned}
    p_u=(Q^TQ+\lambda I)^{-1}Q^Tr_u\\
    q_i=(P^TP+\lambda I)^{-1}P^Tr_i
\end{aligned}
$$

## MF for Implicit Feedback

유저가 해당 아이템을 선호하는가의 여부(`Preference`)를 먼저 이진값으로 표현한다. 그리고 선호한다면 어느 정도 선호하는지를 나타내는 `Confidence`도 생성한다.

$$ c_{ui}=1+\alpha\cdot r_{ui} $$

여기서 $$\alpha$$ 값은 positive feedback과 negative feedback간의 상대적인 중요도를 조정하는 하이퍼 파라미터이다. 지금 이 confidence 항을 기존의 ALS 목적함수에 추가하면 MF에서 Confidence level 을 추가한 식과 동일하게 나타난다.

$$ \underset{P,Q}{\min}\sum_{\text{ovserved }r_{u,i}}c_{u,i}(f_{u,i}-p_u^Tq_i)^2+\lambda(\|p_u\|_2^2+\|q_i\|_2^2) $$

그리고 학습에 사용되는 파라미터를 업데이트 하는 식에 Confidence 와 Preference를 추가한다.

$$
\begin{aligned}
  p_u=(Q^TC^uQ+\lambda I)^{-1}Q^TC^uf_u\\
  q_i=(P^TC^iP+\lambda I)^{-1}P^TC^if_i
\end{aligned}
$$

<br/>

# Bayesian Personalized Ranking

![image](https://user-images.githubusercontent.com/91870042/158059947-93dff3fd-3cc0-4f43-a5ff-c1fd283ce466.png){: .align-center width="70%"}

## BPR의 개념

BPR은 논문 BPR: Bayesian Personalized Ranking from Implicit Feedback 에서 제시된 내용으로, Implicit Feedback 데이터를 사용한 Matrix Factorization 모델을 학습할 수 있는 새로운 관점을 제시한 논문이다. **베이지안 추론에 기반**하고 있으며 Implicit Feedback을 통해서 서로 다른 아이템에 대한 유저의 선호도를 반영하도록 모델링을 진행한다.

### Personalized Ranking

아이템을 추천하는 문제는 사용자에게 순서가 있는 아이템리스트를 만들어서 제공하는 것과 동일하다. 그렇기에 개인화된 아이템 순위를 반영한 리스트를 만드는 것이 중요하다. 예를 들어 아이템 $$i$$보다 아이템 $$j$$를 더 선호한다면, 이 정보를 사용해서 Matrix Factorization 모델을 학습시키는 것이다. 이러한 순위를 만드는 것은 유저 $$u$$에 대한 **Personalized Ranking** 이라고 부른다.

사용자의 클릭, 구매 등의 로그는 Implicit Feedback 데이터이다. 평점과 같이 아이템에 대한 선호의 정도가 분명하게 드러나지 않기 때문이다. Implicit Feedback은 이렇게 아이템을 관측했다!의 정보만 가지고 있을 뿐이다. 반대로 관측하지 않은 데이터에 대해서는 어떻게 처리할 수 있을지 생각해봐야 한다. 크게 다음 2가지로 관측하지 않은 이유를 나타내볼 수 있다.

- 유저가 아이템에 진짜 관심이 없어서
- 유저가 실제로는 관심이 있지만 해당 아이템의 존재를 몰라서

### 접근

BPR은 관측된 아이템과 관측되지 않은 아이템에 대해서 ***"관측된 아이템을 관측하지 않은 아이템보다 선호한다"*** 라는 가정을 가지고 출발한다. 동시에, ***"서로 같은 부류의 아이템(관측한 아이템끼리, 관측하지 않은 아이템끼리)간의 선호도는 추론을 할 수가 없다"***고 가정한다. 이런 접근 방식은 관측하지 않은 아이템에 대해서도 임의의 선호도를 부여해서 학습할 수 있고, 그 결과 관측하지 않은 것들도 순위를 매겨서 나타낼 수가 있다. 

학습을 위한 데이터셋을 생성하는 과정을 살펴보자. 유저 $$u$$ 가 선호하는 아이템들을 $$I_u^+$$라고 하고, $$i$$를 선호하는 아이템, $$j$$를 선호하지 않는 아이템이라고 했을 때 다음처럼 나타내 볼 수 있다. (`\`는 차집합을 의미)

$$ D_s:= \{(u,i,j)\mid i\in I_u^+ \wedge j\in I \backslash I_u^+ \} $$

### 최대 사후확률 추정

$$ p(\Theta \mid >_u) \propto p(>_u\mid \Theta)p(\Theta) $$

- 사전 확률(prior): $$p(\Theta)$$ , 파라미터에 대한 사전 정보
- 가능도 (likelihood): $$p(>_u\mid \Theta)$$, 주어진 파라미터에 대한 유저의 선호 정보의 확률
- 사후 확률(posterior): $$p(\Theta \mid >_u)$$, 주어진 유저의 선호 정보에 대한 파라미터의 확률

위에서 사후 확률을 직접적으로 계산하는 것은 불가능하기 때문에 근사할 수 있는 **사전확률과 가능도의 곱으로 연산**을 한다. 사수확률을 최대화하는 것은 주어진 유저 선호정보를 최대한 잘 나타내는 파라미터를 추정하는 것이다.

### 가능도 (likelihood)

모든 유저의 선호정보 $$>_U$$ 는 다음과 같이 나타낼 수 있다.

$$ \prod_{u\in U}p(>_u|\Theta) = \prod_{u, i, j\in D_s}p(i>_u j)=\prod_{u, i, j\in D_s}\sigma(\widehat{x_{uij}}(\Theta)) $$

여기서 $$p(i >_u j)는 유저 $$u$$가 아이템 $$j$$ 보다 $$i$$를 선호할 확률을 말한다. 그리고 유저의 벡터를 $$p_u$$, 아이템 $$i$$의 벡터를 $$q_i$$라고 한다면, 유저 $$u$$가 아이템 $$i$$ 보다 $$j$$를 좋아할 확률은 다음과 같다.

$$ p(i>_u j):=\sigma(\widehat{x_{uij}}(\Theta)), \qquad \sigma(x):=\frac{1}{1+e^{-x}} $$

$$ \hat{r}_{ui}=p_u^Tq_i,\quad \hat{r}_{uj}=p_u^Tq_j,\quad \widehat{x_{uij}}=\hat{r}_{ui}-\hat{r}_{uj}=p_u^Tq_i-p_u^Tq_j $$

### 사전확률 (prior)

사전확률은 학습해야할 파라미터에 대한 사전확률을 말하는데 여기서는 정규분포를 따른다고 가정한다. 정규분포는 평균이 모두 0이고 공분산 행렬이 $$\Sigma_\Theta$$ 인 정규분포를 말한다.

MF에서 유저와 아이템에 대한 정보는 벡터로 표현되었기 때문에 정규분포를 표현할 때도 벡터방식으로 표현이 이루어져야 한다. 이때 공분산행렬은 $$\lambda_\Theta I$$로 설정하여 하이퍼 파라미터의 개수를 조정한다. 다음식에서는 이 부분이 정규화를 진행하도록 유도된다.

$$ p(\Theta)\sim N(0, \Sigma_\Theta)=N(0, \lambda_\Theta I) $$

## BPR의 학습 및 추론

### BPR의 목적함수

$$
\begin{aligned}
  \text{BPR-OPT} &= \ln p(\Theta \mid >_u) \\\\
  &= \ln p(>_u \mid \Theta)p(\Theta) \\\\
  &= \ln \prod_{(u,i,j)\in D_s} \sigma(\hat{x}_{uij})p(\Theta)\\\\
  &= \sum_{(u,i,j)\in D_s}\ln \sigma(\hat{x}_{uij})+\ln p(\Theta) \\\\
  &= \sum_{(u,i,j)\in D_s}\ln \sigma(\hat{x}_{uij}) - \lambda_\Theta\|\Theta\|^2
\end{aligned}
$$

위의 수식은 미분이 가능하기 때문에 경사하강법을 이용해서 학습을 진행할 수 있다. 하지만 논문에서는 경사하강법을 사용한 학습이 적절한 학습방법이 아니라고 소개한다. 논문에서는 `LEARNBPR`라는 학습 방법을 소개하였다.

### LEARNBPR

LEARNBPR은 **Bootstrap 기반의 SGD를 사용**한다. 위에서 학습에 사용하는 데이터셋을 다음과 같이 정의했었다.

$$ D_s:= \{(u,i,j)|i\in I_u^+ \wedge j\in I \backslash I_u^+ \} $$

여기서 문제점은 관측한 아이템 $$i$$ 보다 관측되지 않은 아이템 $$j$$가 훨씬 많아서 **학습의 불균형(비대칭)이 발생**할 수 있다는 것이다. 불균형이 발생되면 동일한 유저 $$u$$와 아이템 $$i$$에 대해서 계속 업데이트가 되어서 수렴이 되지 않게 된다.

이문제를 해결하기 위해서 LEARNBPR은 $$u,i,j$$ 단위로 랜덤 샘플링을 진행해서 $$i$$와 $$j$$의 비대칭 학습을 해소하였다. 동일한 유저와 아이템이 나오지 않기 때문에 성능도 우수하였다.

![image](https://user-images.githubusercontent.com/91870042/158058672-c1213e32-5212-40e4-8fb5-d3b55412f011.png){: .align-center width="70%"}


최종적으로 이를 Matrix Factorization에 적용해서 파라미터를 업데이트 하는 과정은 다음과 같다.

![image](https://user-images.githubusercontent.com/91870042/158058745-1212051e-9ae5-45c0-85ac-b61e49b9a111.png){: .align-center width="70%"}


### BPR 요약

- Implicit Feedback 데이터만을 활용해 아이템 간의 선호도 도출
- MAximum A Posterior 방법을 통해 파라미터를 최적화
- LEARNBPR이라는 Bootstrap 기반의 SGD를 활용해 파라미터를 업데이트
- Matrix Factorization에 BPR Optimization을 적용한 결과 성능이 우수
