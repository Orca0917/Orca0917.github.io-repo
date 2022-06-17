---
title:  "[Recommender System] Multi-Armed Bandit 심화"
excerpt: "MAB 알고리즘인 Thompson sampling과 LinUCB에 대해 알아보자"

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

# Thompson Sampling

Thomspson sampling과 다른 MAB 알고리즘과의 차이점은 reward 추정치인 $Q_t(a)$ 를 구할 때 확률분포를 사용함에 있다. Thompson Sampling은 확률분포인 베타분포를 사용해서 MAB문제를 해결하고자 하였다. 베타분포가 무엇인지부터 확인해보자

**베타분포**

베타분포는 2개의 양의 변수로 표현되는 확률분포이며 베타분포에서 sampling 한 값은 0~1사이의 값을 갖는다. 수식으로 표현하면 아래와 같다.

$$ Beta(x\mid \alpha, \beta) = \frac{1}{B(\alpha, \beta)}x^{\alpha-1}(1-x)^{\beta-1} $$

- $B(\alpha, \beta)$ : 두 변수 $\alpha, \beta$ 에 의해서 정의되는 베타함수를 말한다.

수식자체로 이해하기는 어렵기 때문에 예제와 함께 이해를 진행해보자. 사용자에게 보여지는 광고배너가 있다고 하고 두개의 변수는 $\alpha, \beta$를 각각 클릭했을 때와 클릭하지 않았을 때라고 해보자. 그리고 이 배너를 클릭할 확률을 베타분포로 부터 sampling하여 구할 수 있다.

| Step1 : 데이터가 없는 초기 상태

각 광고배너는 초기에는 데이터가 없기 때문에 모두 $Beta(1, 1)$ 로 부터 sampling된다. 이럴 때, 그래프는 아래와 같이 그려진다.

- banner1 ~ Beta(1, 1)
- banner2 ~ Beta(1, 1)
- banner3 ~ Beta(1, 1)

![베타분포 step1](https://user-images.githubusercontent.com/91870042/159113719-37169c48-8dd5-48ed-9222-fa588fcb6e59.png){: .align-center width="50%"}

| Step2 : 광고를 랜덤하게 노출

베타분포를 적절한 수준까지 업데이트하기 위해서 광고를 일정 기간 랜덤하게 노출시켜 데이터를 얻는다. 아래와 같은 방법으로 지속적으로 노출하면 그래프가 어느정도 데이터를 잘 표현하게 된다.

- banner1 ~ Beta(2, 1) // 광고를 노출시켰더니 클릭하였다.
- banner2 ~ Beta(1, 2) // 광고를 노출시켰지만, 클릭하지 않았다.
- banner3 ~ Beta(1, 1) // 광고를 노출시키지 않았다.

![베타분포 step2](https://user-images.githubusercontent.com/91870042/159113941-8decf97f-d101-40aa-a3cb-9c4b75e102e7.png){: .align-center}

최종 결과로 각 배너는 아래와 같은 베타분포를 띄고 샘플링한 값을 우측에 표시했을 때 아래와 같다고 하자.

- banner1 ~ Beta(3, 2) : 0.386
- banner2 ~ Beta(2, 3) : 0.438
- banner3 ~ Beta(2, 2) : 0.616

위의 샘플링 결과중에서 가장 큰 값은 banner3 이기 때문에, banner3 을 한 번더 노출시킨다. 그 이후, 유저가 클릭했다고 가정하면 banner3의 베타분포는 Beta(3, 2) 이 된다. (클릭인 $\alpha$ 값의 증가)

다시 그 베타분포에서 샘플링한 결과가 아래와 같다고 하자

- banner1 ~ Beta(3, 2) : 0.851
- banner2 ~ Beta(2, 3) : 0.079
- banner3 ~ Beta(3, 2) : 0.430

여기서 샘플링 결과가 가장 큰 값을 갖는 배너인 banner1을 노출시키고 이후에 유저 클릭하지 않았다 하면 banner1의 베타분포는 Beta(3, 3)이 된다. 이런식으로 지속적으로 베타분포를 업데이트 하면 결국에 분포가 특정 지점에서 높은 값을 갖게 된다. 진행 과정은 아래와 같다.

![image](https://user-images.githubusercontent.com/91870042/159114391-bd15f002-4a90-473d-874f-15ea1b19a732.png){: .align-center}

아래는 Thompson Sampling 과정 전체를 pseudo-code로 표현한 것이다.

![image](https://user-images.githubusercontent.com/91870042/159114444-bf5ded4c-0378-4f45-9f06-b8ae5e273dd7.png){: .align-center width="70%"}

<br/>

# LinUCB

## 개요

LinUCB에 대해서 알아보기전에 Contextual Bandit 문제가 무엇인지 알아야한다. Context는 유저나 아이템이 갖는 정보중에서 ID를 제외한 나머지 부가적인 정보를 의미한다. 여기에는 유저의 Demographic 정보나, 아이템의 카테고리 또는 태그와 같은 정보들이 포함되어 있다.

이 Bandit문제도 이 Context를 포함하는지에 따라서 2가지로 구분해볼 수 있다. 

**Context-free Bandit**

Context-Free Banit은 context 정보를 사용하지 않는 Bandit으로 이전 포스팅에서 다룬 Epsilong Greedy 알고리즘이나 UCB 알고리즘이 여기에 속한다.

$$ q_*(a) \doteq \mathbb{E}[R_t \mid A_t = a] $$

context-free Bandit은 동일한 action에 대해 유저의 context 정보와 상관없이 동일한 reward를 받았었다.

**Contextual Bandit**

Contextual Bandit은 유저나 아이템의 context 정보를 사용하는 bandit이다. 이를 사용하면 유저나 아이템의 context 정보에 따라서 같은 $t$ 에 동일한 action을 취하더라도 다른 reward를 받게 된다. 그렇기 때문에 개인화된 추천에 잘 사용해볼 수 있다.

$$ q_*(a) \doteq \mathbb{E}[R_{t,a} \mid X_{t,a}] $$

## LinUCB

$$ A_t\doteq\arg\max_a\left[ x_{t,a}^T\theta_a^* + \alpha\sqrt{x_{t,a}^T\mathbf{A}_a^{-1}x_{t,a}}\right], \quad \text{where}\; \mathbf{A}_a=\mathbf{D}_a^T\mathbf{D}_a+\mathbf{I}_d $$

$$ x_{t,a}^T\theta_a^* = \mathbb{E}[r_{t,a} \mid x_{t, a}]$$

- $x_{t,a}:$ $d$ 차원의 유저에 대한 context 벡터이다.
- $\theta_a^*:$ Action $a$ 에 대한 $d$ 차원의 학습 파라미터
- $\mathbf{D}_a :$ $t$ 시점의 $m$ 개의 컨텍스트 벡터로 구성된 $m\times d$ 크기의 행렬

위의 수식중 첫번재 수식에서 2번째 term (루트가 포함된 수식)은 exploration을 하는 수식으로 탐색 횟수가 많아질 수록 그 값은 작아지게 된다. 그래서 횟수가 많아진다면, 앞에 있는 1번째 term이 의미하는 것으로 수렴하게 된다. 여기서 1번째 term이 의미하는 것은 $t$ 시점에 Action $a$ 를 취한것의 기댓값이다.

### 예시

![image](https://user-images.githubusercontent.com/91870042/159116019-8ce93bf2-4f5a-4a1a-bfb1-833e5c20df9a.png){: .align-center}

위와 같은 예시를 살펴보자. 유저는 3명이고 context 벡터가 각각 male, female, young, old를 나타내고 있을 때 유저에 대한 context 벡터를 표현한 것이다. 이후, $D$ 는 $m$ 개의 context 벡터로 구성된 행렬을 생성한다.

그리고 이 정보들을 사용해서 학습 파라미터인 $\theta$ 를 학습시키서 아래의 그림과 같은 결과나 나왔다고 하면, 각 아이템에 대해 어떤 context 정보가 가장 의미있는지를 알 수 있다. 예를 들어, 첫번째 상품인 화장품은 female에 대해 의미가 높다는 것을 알 수 있다.

![image](https://user-images.githubusercontent.com/91870042/159116153-b2d0b3ce-c4ba-4965-9f73-73da5f6e0a34.png){: .align-center}

## LinUCB 의사코드

![image](https://user-images.githubusercontent.com/91870042/159116321-7c40ed54-6505-435b-9176-9424f5a36ca4.png){: .align-center width="70%"}
