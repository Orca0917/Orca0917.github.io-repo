---
title: "Neural Collaborative Filtering 논문 리뷰"

categories:
  - paper
tags:
  - [AI, Recommender System]
toc: true
toc_sticky: true
use_math: true

date: 2022-03-22 00:00:00
last_modified_at: 2022-03-22 00:00:00

layout: single
classes: wide
---

# Neural Collaborative Filtering

[📜 논문 링크](https://arxiv.org/abs/1708.05031)

![image](https://user-images.githubusercontent.com/91870042/159477904-9d657603-9d1b-40f2-ad9d-69447740b074.png){: .align-center}

## ABSTRACT

이 논문은 최근에 딥러닝을 사용한 모델들이 다양한 분야(자연어처리, 컴퓨터비전)에서 좋은 성능을 보여줌에 따라 추천시스템에도 딥러닝을 적용시켜보고자 하였다. 그리고 Neural Collaboraitve Filtering 모델이 해결하고자 했던 문제는 Implicit Feedback 데이터를 사용해서 협업필터링(CF)를 했을 때의 문제점이다.

이 논문이 발표되기 이전에도 딥러닝을 추천시스템에 적용한 사례는 있었지만 단순히 부족한 정보를 보조하는데에만 사용이 되었을 뿐이며 유저와 아이템 사이의 상호작용(interaction)을 얻기 위해서는 여전히 잠재 요소(latent factor)간의 내적을 필요로 하였다.

NCF(Neural Collaborativ Filtering)는 내적하는 연산을 대체하였고, 딥러닝의 특징 중 하나인 비선형성(non-linearlity)를 사용해 유저와 아이템의 상호작용을 학습하도록 하였다. 수많은 실험 결과 기존 추천시스템에서의 SOTA(State-Of-The-Art)보다 더 좋은 성능을 보여주었고, 딥러닝의 레이어가 많아질 수록 더 좋은 성능을 보였다고 말한다.

## INTRODUCTION

논문은 처음에 기존에 존재하던 Matrix Factorization(MF)의 한계를 지적하면서 시작된다. 협업필터링에서 MF가 좋은 성능을 보임에도 그 발목을 잡는 것이 내적(inner product)연산이라고 한다. 그리고 다음과 같은 주장을 하였다.

> 내적연산은 선형성을 띄기 때문에, 유저와 아이템간의 복잡한 상호작용을 학습하기 어렵다.

그렇기 때문에 딥러닝을 사용한 추천시스템이 필요하다고 말한다. 앞서서 말했듯이 딥러닝을 사용한 추천시스템 연구는 있었고, 유망하였지만 여전히 유저와 아이템 벡터간의 내적을 사용한 것을 한계점으로 보았다. 그리고 NCF에서 사용할 데이터는 Implicit Feedback 데이터인데 그 장단점을 아래와 같이 말한다.

**장점**

- 데이터를 수집하기가 쉽다.
- Explicit Feedback 데이터보다 훨씬 많은 양의 데이터를 모을 수 있다.

**단점**

- 사용자(user)의 만족도가 명시적으로 나타나지 않아서 사용하기가 까다롭다.
- Negative Feedback 정보가 없다.

논문에서 한 주요 성과 3가지는 다음과 같다.

1. 유저와 아이템의 latent feature를 생성할 수 있는 신경망 구조 도입
2. Matrix Factorization은 NCF의 일부임으로 보이고, 비선형성을 학습하기 위해서 MLP를 사용
3. 현실세계를 잘 담은 데이터셋에 대해 수 많은 실험을 하여 협업필터링에서 딥러닝이 유망함을 보임

## PRELIMINARIES

### **Learning from Implicit Data**

NCF는 유저의 Implicit Feedback 데이터를 받아 다음과 같이 표현하였다.

$$
\begin{aligned}
y_{u,i} = \begin{cases}1, \quad \text{if interaction(user $u$, item $i$) is observed} \\ 0, \quad \text{otherwise} \\ \end{cases}
\end{aligned}
$$

유저와 아이템의 상호작용이 있었을 때, 1로 표시하고 그렇지 않을 때는 0으로 표시하는 것이다. 하지만 여기서 주의해야 할 것은 상호작용을 하였다고 해서, $u$ 가 $i$ 를 선호하는 것은 아니다. 반대로, 상호작용하지 않았다고 해서 $u$ 가 $i$ 를 선호하지 않는것도 아니다. 값이 0이라면 그 이유를 다음과 같이 생각해볼 수 있다.

- 아이템의 존재 자체를 몰라서 상호작용이 없었던 경우
- 아이템을 정말 선호하지 않아서 상호작용이 없었던 경우

Implicit Feedback을 사용하는 추천시스템은 비어있는 상호작용에 대해서 점수를 매기고 아이템에 순위를 부여해 상위 $k$ 를 추천하는 문제를 해결한다. 이 때 수식을 아래와 같이 나타내볼 수 있다.

$$ \hat{y}\_{ui} = f(u, i \mid \Theta) $$

- $\hat{y}_{ui}$ : 유저 $u$ 와 아이템 $i$ 의 예측 상호작용 점수
- $f$ : 모델의 파라미터를 사용해서 점수를 예측하는 interaction function
- $\Theta$ : 모델의 파라미터

결국 해야할 일은 점수를 가장 잘 예측하는 모델 파라미터 $\Theta$ 를 최적화 시키는 것인데, 일반적으로 목적함수를 설정해서 최적화 시키는 방법을 사용한다. 그리고 문제를 풀기 위해서 2가지의 목적함수가 잘 사용된다.

- pointwise loss : 예측값 $\hat{y}_{ui}$ 와 실제값 $y\_{ui}$ 의 오차 제곱합을 최소화 하는 형태
- pariwise loss : 관측된 값 $\hat{y}_{ui}$ 가 관측되지 않은 값 $\hat{y}\_{uj}$ 보다 높게 rank 되게 하는 형태

하지만 Implicit Feedback 데이터의 특성상 Negative data가 없기 때문에, 관측하지 않은 값을 모두 Negative Feedback으로 간주하거나, 일부를 Negative Feedback으로 간주한다.

### **Matrix Factorization**

Matrix Factorization은 만들어진 행렬 $Y$ 에 대해서 저차원의 행렬 $P, Q$ 로 분해하여 표현하는 방법을 말하며 다음과 같이 표현할 수 있다. 식을 보면 알 수 있듯이 MF는 잠재요소를 사용한 선형모델로 간주된다.

$$ \hat{y}_{ui} = f(u, i \mid \mathbf{p}\_u, \mathbf{q}\_i) = \mathbf{p}\_u^T\mathbf{q}\_i = \sum_{k=1}^K p*{uk}q*{ik}$$

- $K$ : latent space의 차원(크기)
- $\mathbf{p}_u$ : 유저 $u$ 에 대한 latent vector
- $\mathbf{q}_i$ : 아이템 $q$ 에 대한 latent vector

다음은 이제 이런 선형모델을 사용하는 Matrix Factorization의 예시를 소개하며 그 한계점을 지적한다.

![image](https://user-images.githubusercontent.com/91870042/159450460-1834ab29-b8a1-4ebb-9e73-02c394d78af5.png){: .align-center width="80%"}

(a)에 유저와 아이템의 상호작용 행렬이 존재한다고 했을 때, 유저간의 유사도를 Jaccard 유사도를 사용해 구하면 다음과 같다.

- $s_{23} = 0.66, \quad s_{12} = 0.5, \quad s_{13} = 0.4$

위의 유사도를 바탕으로 latent space에 나타내보면 검은색 실선으로 그려진 (b)를 얻을 수 있다. 문제는 새로운 유저 $u_4$ 가 추가되었을 때 발생한다. $u_4$ 와 나머지 유저들에 대해서 유사도를 구하고 비교하면 아래와 같다.

- $s_{41} = 0.6 > s_{43} = 0.4 > s_{42} = 0.2$

먼저 $u_1$ 과 $u_4$ 가 가장 유사하기 때문에 위의 그림 (b)에서 파란색 점선으로 나타내볼 수 있다. 그다음으로 가까운 사람은 $u_3$ 인데, 아무리 애를 써도 $u_3$ 보다 $u_2$ 가 $u_4$ 와 가깝게 그려질 수 밖에 없다. NCF는 이 부분에서 선형모델을 사용하는 Matrix Factorizaiton의 한계점을 말한다.

물론 위의 상황은 latent factor의 수가 5개 밖에 안되는 특이한 상황이며, 그 수를 늘리면 충분히 해결할 수 있는 문제이다. 하지만, 그 값이 커지면 over fitting의 문제점이 있어 일반화가 안될 수 있다. 그렇기 때문에 논문은 Neural Network를 통해서 문제를 해결하였다.

## NEURAL COLLABORATIVE FILTERING

### General Framework

![image](https://user-images.githubusercontent.com/91870042/159453156-c4d13368-0aff-4c4c-baec-c74345cf683a.png){: .align-center width="80%"}

위의 그림은 논문에서 소개하는 NCF의 General Framework를 나타낸다. 이제 입력층부터 값이 어떻게 전달되고 레이어서는 어떤 연산이 일어나는지 하나씩 알아보자.

가장 밑에 있는 2개의 feature vector는 모델이 맞추어서 커스터마이징이 가능하다. 이 feature vector는 원핫 인코딩된 것이며 입력(input)으로 사용되게 된다. 여기서 발생할 수 있는 cold-start 문제는 content feature를 추가로 사용하면 간단하게 해결할 수 있다.

- $\mathbf{v}_u^U$ : 유저 $u$ 의 feature vector
- $\mathbf{v}_i^I$ : 아이템 $i$ 의 feature vector

그 다음에 존재하는 Layer는 Embedding Layer이다. 임베딩 레이어는 FCN(Fully Connected Layer)이며 입력으로 들어온 sparse 한 feature vector를 Dense하게 표현해주도록 바꾸어준다. 그렇게 생성된 유저와 아이템 각각의 Latent Vector는 NCF의 첫 레이어로 들어간다. 그리고 이 모델은 예측한 score와 실제 target score간의 오차를 최소화하는 방향으로 학습을 진행한다. 지금까지 소개한 내용을 수식으로 나타내면 다음과 같이 표현해볼 수 있다.

$$ \hat{y}\_{ui} = f(\mathbf{P}^T\mathbf{v}\_u^U, \mathbf{Q}^T\mathbf{v}\_i^I \mid \mathbf{P}, \mathbf{Q}, \Theta_f), \qquad \mathbf{P}\in\mathbb{R}^{M\times K}, \mathbf{Q}\in\mathbb{R}^{N\times K} $$

- $\mathbf{P}, \mathbf{Q}$ : latent factor matrix
- $\Theta_f$ : interaction function $f$ 에 대한 모델 파라미터

<br/>

**Learning NCF**

이 부분은 HyeJinLee님의 포스팅을 보고 참고하여 작성하였습니다. ([링크](https://leehyejin91.github.io/post-ncf/))
{: .notice--info}

모델의 파라미터를 학습시키기 위해 존재하던 pointwise methods 는 주로 제곱오차를 사용한 회귀를 이용했다.

$$ L*{sqr} = \sum*{(u, i)\,\in\,\mathcal{Y}\,\cup\,\mathcal{Y}^-} w*{ui}(y*{ui} - \hat{y}\_{ui})^2$$

- $\mathcal{Y}$ : $\mathbf{Y}$ 에서 상호작용이 관찰된 집합
- $\mathcal{Y}^-$ : 관측되지 않은 상호작용 전부 또는 샘플링된 일부
- $w_{ui}$ : $u$ 와 $i$ 를 학습시키는 학습가중치

위처럼 제곱오차를 사용하는 것은 관측된 데이터들이 Gaussian Distribution으로 부터 생성된 것을 가정하는데, 실제로 implicit data는 위의 조건에 잘 부합하지 않는다. Implicit data의 target 값이 0 또는 1을 갖는 이진 값이기 때문이다. 이런 특성을 잘 살려서 1 이라는 값을 갖게 되면, "$i$ 가 $u$ 는 관련이 있다" 라고 볼 수 있다.

그리고 예측한 값인 $\hat{y}\_{ui}$ 는 $i$ 와 $u$ 가 얼마나 유사한지를 나타낸다고 해석할 수 있다. 확률론적으로 해석하기 위해서 $\hat{y}_{ui}$ 값을 0과 1사이의 값으로 제한시킬 필요가 있다. 지금까지 설명한 것을 바탕으로 likelihood function 을 나타내면 다음과 같다.

$$ p(\mathcal{Y}, \mathcal{Y}^{-}|\mathbf{P},\mathbf{Q},\Theta*{f})= \prod*{(u,i) \in \mathcal{Y} }{\hat{y}_{u,i}} \prod_{(u,j) \in \mathcal{Y}^{-}} ({1-\hat{y}\_{u,j}}) $$

다시, 위의 식에 음의 log를 취하면 다음과 같다.

$$
\begin{aligned}
L &= -log \ p(\mathcal{Y}, \mathcal{Y}^{-} | P,Q,\Theta_{f}) \\
\\
  &= -\sum_{(u,i) \in \mathcal{Y}} y_{u,i}\ log \ \hat{y}_{u,i} - (\sum_{(u,j) \ in  \mathcal{Y}^{-}} (1- y_{u,i}) \ log \ (1-\hat{y}_{u,j}) ) \\

 \\

  &= -\sum_{(u,i) \in  \mathcal{Y} \cup \mathcal{Y}^{-} } ( y_{u,i}\ log \ \hat{y}_{u,i} + (1- y_{u,i}) \ log \ (1-\hat{y}_{u,i}))
\end{aligned}
\\
$$

이렇게 생성된 목적함수가 NCF를 최적화 시키는 방법이며, SGD를 사용해 최적화를 진행시킬 수 있다. 여기서 하나 봐야할 점은 위에서 구한 식이 log loss라고 불리는 binary cross-entropy loss와 동일하다는 것이다. 이렇게 확률론적 분석을 NCF에 적용함에 따라 implicit feedback 문제를 binary classification 문제로 바꿀 수 있는 것이다.

Negative Instance인 $\mathcal{Y}^-$ 에 대해서는 각 iteration 마다 균일하게 샘플링하며, 그 비율을 조절해 최적의 값을 찾아나가야 한다. (아래에서 결과를 분석하는 RQ2 에서 다룬다)

### Generalized Matrix Factorization (GMF)

이번 파트에서는 MF가 NCF의 일부임을 보이면서 이름을 GMF로 부른다. GMF에서는 임베딩 레이어에서 생성된 latent vector들을 입력으로 받는다. 이때 첫번째 레이어의 mapping function을 다음과 같이 정의한다.

$$ \phi_1(\mathbf{p}\_u, \mathbf{q}\_i) = \mathbf{p}\_u \odot \mathbf{q}\_i$$

그리고 output 레이어로 다음과 같이 전달한다.

$$ \hat{y}_{ui} = a_{out}(\mathbf{h}^T(\mathbf{p}\_u \odot \mathbf{q}\_i)) $$

- $a_{out}$ : 활성함수(activation function)
- $\mathbf{h}$ : 가중치

논문에서는 더 많은 표현을 할 수 있도록 활성함수로 sigmoid를 사용하였고, $\mathbf{h}$ 는 log loss를 사용해서 학습시켰다.

### Multi-Layer Perceptron (MLP)

MLP도 입력으로 user latent vector와 item latent vector를 받는데, 일반적으로 그 둘을 concatenate 시킴으로서 결합하였다. 하지만, 이런 단순한 결합은 유저와 아이템의 latent feature간의 상호작용을 하게한다고 전혀 설명할 수 없다. 그렇기 때문에 이 논문은 hidden layer를 추가하여 유저와 아이템간의 상호작용을 배우도록 만들었다.

$$
\begin{aligned}
\mathbf{z}_1 &= \phi_1(\mathbf{p}_u, \mathbf{q}_i) = \begin{bmatrix}
\mathbf{p}_u \\ \mathbf{q}_i
\end{bmatrix},\\
\phi_2(\mathbf{z}_1)&=a_2(\mathbf{W}_2^T\mathbf{z}_1 + \mathbf{b}_2),\\
&\dots\dots\\
\phi_L(\mathbf{z}_{L-1}) &= a_L(\mathbf{W}_L^T\mathbf{z}_{L-1} + \mathbf{b}_L),\\
\hat{y}_{ui}&=\sigma(\mathbf{h}^T\phi_L(\mathbf{z}_{L-1}))
\end{aligned}
$$

- $\mathbf{W}_x$ : $x$ 번째 레이어의 가중치 행렬
- $b_x$ : $x$ 번째 레이어의 편향 벡터 (bias vector)
- $a_x$ : $x$ 번째 레이어의 활성함수

### Fusion of GMF and MLP

![image](https://user-images.githubusercontent.com/91870042/159468544-c4e61884-b8f4-4449-baad-337fff94b5da.png){: .align-center width="80%"}

지금까지 생성한 GMF와 MLP를 어떻게 결합(융합)시킬 수 있을지에 대해 이제 고민해봐야 한다. 가장 간단한 해결은 두 구조가 동일한 임베딩 레이어를 공유하도록 만드는 것이다. 하지만 각 모델에 맞는 최적의 입력 임베딩의 크기가 다르기 때문에 서로 다른 임베딩을 두는 것이 좋다.

논문은 서로 다른 임베딩 벡터를 두어 각 모델에 다른 입력이 전달될 수 있도록 만들었다. 그리고 각 모델의 마지막 hidden layer에서 출력을 concatenate 시켰다. 이 과정을 수식화 하면 다음과 같다.

$$
\begin{aligned}
\phi^{GMF} &= \mathbf{p}_{u}^{G} \odot \mathbf{q}_{i}^{G} \\
\phi^{MLP} &= a_{L}(\mathbf{W}_{L}^{T}(a_{L-1}(...a_{2}(\mathbf{W}_{2}^{T} \begin{bmatrix}
\mathbf{p}_{u}^{M}  \\
\mathbf{q}_{i}^{M}
\end{bmatrix}+\mathbf{b}_{2})...))+\mathbf{b}_{L}) \\
\hat{y}_{u,i} &= \sigma(\mathbf{h}^{T} \begin{bmatrix}
\phi^{GMF}  \\
\phi^{MLP}
\end{bmatrix})
\end{aligned}
$$

- $\mathbf{p}_u^G$ : GMF의 유저 임베딩
- $\mathbf{p}_u^M$ : MLP의 유저 임베딩
- $\mathbf{q}_i^G$ : GMF의 아이템 임베딩
- $\mathbf{q}_i^M$ : MLP의 아이템 임베딩

## EXPERIMENTS

논문은 다음 3가지 질문에 대해 초점을 맞추어서 실험을 진행하였다.

> RQ1. Do our proposed NCF methods outperform the state-of-the-art implicit collaborative filtering methods?
> 여기서 제안한 NCF 방법론이 기존 implicit feedback 데이터를 다루는 SOTA보다 더 좋은 성능을 내는가?

> RQ2. How does our proposed optimization framework (log loss with negative sampling) work for the recommendation task?
> 여기서 제안한 최적화 기법이 추천에서 어떤 일을 하는가?

> RQ3. Are deeper layers of hidden units helpful for learning from user-item interaction data?
> hidden layer가 많을 수록 유저와 아이템의 상호작용을 학습하는데 더 좋은가?

### Experimental Settings

**Datasets**  
실험에 있어서 사용한 데이터셋는 MovieLens 데이터셋과 Pinterest에서 제공한 데이터셋을 사용하였다. 각 evaluation 데이터셋에 대한 통계는 아래와 같다.

![image](https://user-images.githubusercontent.com/91870042/159469268-ddab3e25-2d25-4eb4-aabd-80d932b26aa8.png){: .align-center width="80%"}

**Evaluation Protocols**  
평가에 사용한 지표는 Hit Ratio(HR)과 Normalized Discounted Cumulative Gain(NDCG) 지표를 사용하였다.

**Baselines**  
NCF모델과 비교할 대상은 ItemPop., ItemKNN, BPR, eALS 이다.

### Performance Comparison (RQ1)

![image](https://user-images.githubusercontent.com/91870042/159469832-5ec13130-8e58-4efa-9434-152c1306c2f5.png){: .align-center}

위 사진은 HR@10과 NDCG@10 에 대한 성능을 보여준다. 먼저 가장 눈에 띄는 부분은 SOTA로 불리는 eALS 또는 BPR보다 두 데이터셋에서 더 나은 성능을 보여주었다. Pinterest는 예측 요소가 8로 적었음에도 불구하고, 큰 예측 요소인 64를 사용한 eALS나 BPR보다 지속적으로 좋은 성능을 보였다.

MLP와 GMF에 대해서 비교적 강한 성능을 보여주었는데 MLP가 살짝 아래에 나타나있는 것을 보인다. 하지만, MLP는 레이어를 추가하여 성능을 올릴 수 있기 때문에 1차원적으로만 바라볼 수 없다. 마지막으로 GMF는 BPR보다 거듭되는 성장을 보여주었고, 이는 log loss를 사용한 classification의 효율을 증명해주었다. (둘은 동일한 MF 모델을 사용하였지만, 목적함수가 다름)

![image](https://user-images.githubusercontent.com/91870042/159471329-ace37390-9897-4afc-b255-08abc61ce17d.png){: .align-center}

위와 비슷하게 $\text{Top}-K$ 도 NCF가 가장 좋은 성능을 보였으며, $K$ 의 값이 커짐에 따라서도 높은 성능을 지속적으로 보여주었다.

### Log Loss with Negative Sampling (RQ2)

![image](https://user-images.githubusercontent.com/91870042/159471904-5d066f1e-784d-4bc6-9649-43b923ca29e5.png){: .align-center}

Implicit Feedback이 0과 1로 구성되어 있음에 따라 논문에서는 이를 binary classfication task와 같이 해결하였다. 논문은 NCF를 확률론적 모델로 보고 log loss를 통해 최적화를 진행하였다. 위의 사진 중 상단(Figure 6)은 loss의 변화를 보인다.

먼저 확인할 수 있는것은 NCF는 iteration 수가 늘어날 때마다 loss 값은 줄어들고 성능은 향상되는 것을 볼 수 있다. 가장 효과적인 update는 초기 10번의 iteration에서 발생하였고, 더 많은 iteration은 over fitting을 야기시킬 수 있다. 그리고 NCF 방법론들 간에서 비교를 진행하면 성능 순서는 NeuMF, MLP, GMF 순이었다. 이를 통해 log loss를 통한 최적화가 implicit data 를 통해 학습하는데 효과적임을 보여준다.

pointwise log loss를 pairwise 목적함수보다 사용하기 좋은 것은, 샘플링하는 비율을 조절할 수 있다는 것이다. 위의 사진에서 하단(Figure 7)을 보면 더 많은 Negative Sampling을 할수록 초반에 성능의 증가폭을 보임을 알 수 있다. 논문에서는 위의 실험결과를 통해 1개의 positive sampling 당 3~6개의 Negative Sampling을 진행하는 것이 최적이라고 발표하였다.

### Is Deep Learning Helpful? (RQ3)

![image](https://user-images.githubusercontent.com/91870042/159473257-33eaa6cd-0431-4e75-9d6f-78da77273d8f.png){: .align-center width="80%"}

마지막으로 논문은 MLP에서 사용하는 hidden layer의 수가 많아지면 성능이 좋아지는지 확인하였다. 실험결과 layer가 점점 증가할 수록, 성능이 올라감을 알 수 있다. 특히 MLP-0은 hidden layer가 존재하지 않아 임베딩된 벡터간의 concatentation만 이루어진 상태를 말하는데, 이때는 특히 더 성능이 좋지 않은 것을 보아 단순 concatenation 보다 hidden layer를 통과시키는 것이 좋다는 것을 보였다.
