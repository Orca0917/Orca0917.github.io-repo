---
title:  "[Recommender System] GNN을 사용한 추천시스템"
excerpt: "Graph에 대한 간단한 개념과 GNN을 사용한 추천시스템인 NGCF와 LGCN에 대해 알아보자"

categories:
  - RecSys
tags:
  - [AI, Naver, BoostCamp, Recommender System]
toc: true
toc_sticky: true
use_math: true

date: 2022-03-16 1:00:00
last_modified_at: 2022-03-16 01:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Recommender System with GNN

## Graph Neural Network

Graph로 구성된 Graph Neural Network에 대해 알아보기 전에, Graph가 무엇인지 간단하게 살펴보자

### Graph

![image](https://user-images.githubusercontent.com/91870042/158601781-23e6d11d-c79d-4464-9c20-77f3dc23d4df.png){: .align-center width="70%"}

그래프는 정점(노드, node)와 정점들을 연결하는 간선(edge)로 이루어진 자료구조이다. 정점들의 집합을 $V$, 간선의 집합을 $E$ 로 표현하며 그래프 전체를 말할 때는 $G=(V,E)$ 라고 정의한다. 그렇다면 GNN에서 그래프를 사용하는 이유는 무엇인지, 그래프를 사용했을 때의 장점이 무엇인지 알아보자.

1. `관계, 상호작용과 같은 추상적인 개념을 다루기에 적합하다.`

    ![image](https://user-images.githubusercontent.com/91870042/158602691-f58fe9bd-1b1b-4cec-aa4a-8984cc621c28.png){: .align-center width="60%"}

    현실세계의 다양한 문제들은 대부분 관계 또는 상호작용으로 이루어져있다. 사람과 사람사이의 연결도 관계이고 그 사이에서는 상호작용이 일어난다. 이런 부분에서 그래프는 현실세계를 표현하기에 매우 적합하며 복잡한 문제들을 그래프를 사용하면 간단한 표현으로 단순화시킬 수 있다.

    예로, SNS에 존재하는 친구간의 네트워크, 코로나-19 바이러스의 확산, 유저와 아이템의 소비관계들은 모두 그래프로 나타내볼 수 있다.

2. `비유클리드 공간(Non-Euclidean Space)을 표현하거나 학습할 수 있다.`

    ![image](https://user-images.githubusercontent.com/91870042/158603444-23e48b3a-f456-40bb-993e-cf7448275d4c.png){: .align-center}

    우리가 흔히 다루는 이미지나 텍스트, 정형데이터는 Euclidean Space에서 격자형태로 표현하는 것이 가능하다. 하지만 그래프로 자주 나타내는 SNS 관계데이터나 분자구조 들은 비유클리드 공간에 존재한다. 그래프를 사용하면 이런 분자구조들도 쉽게 나타내볼 수 있다.

다시 돌아와서 GNN은 그래프를 사용한 신경망으로 그래프 데이터에 대해 적용할 수 있는 신경망이다. GNN이 하고자 하는 것은 이웃 노드들 간의 정보를 사용해 특정 노드를 잘 표현하는 특징을 찾아내는 것이다.

![image](https://user-images.githubusercontent.com/91870042/158604268-c4b34628-9b68-4a85-ad69-4a1c3ca5d21f.png){: .align-center}

이 과정을 가장 간단하게 구현하는 방법으로 그래프의 모습을 인접행렬(Adjacent Matrix)로 나타내고 이를 MLP에 사용을 해볼 수 있다. 하지만 이 연산은 노드가 많아질수록 연산량은 기하급수적으로 많아지며 노드의 순서가 바뀌면 의미가 달라질 수 있어 사용하기가 힘들다.

### Graph Convolution Network

![image](https://user-images.githubusercontent.com/91870042/158605320-e6141ec7-e222-4c38-a643-5f18dc31f3fd.png){: .align-center}

방금 설명한 인접행렬을 사용해 MLP의 입력으로 넣는 방법은 매우 비효율적이어서 사용하기 힘들었다. Graph Convolution Network는 이미지 관련된 연산으로 사용되는 컨볼루션 연산을 그래프에 사용해서 여러개의 노드를 한 번에 계산해 연산량을 줄이면서 컨볼루션을 여러번 수행하면 멀리 떨어진 정점과의 관계도 추출해낼 수 있다.

## Neural Graph Collaborative Filtering

![image](https://user-images.githubusercontent.com/91870042/158605567-45ac9092-bed5-4d82-8a7a-5634592650e9.png){: .align-center}

이 논문은 유저-아이템 상호작용을 GNN으로 임베딩하는 과정에서 인코딩하는 방법을 제시하였다. NGCF에 대해 설명하기 전에 그 등장배경을 살펴보자. 기본적인 협업필터링 MF를 살펴보면 크게 2가지 특징을 모델이 잘 학습해야했다.

- 유저와 아이템 각각의 Dense Representation [Embedding]
- 유저와 아이템의 상호작용 [Interaction]

기존의 신경망을 적용한 협업필터링 모델들은 유저와 아이템의 상호작용을 임베딩하는 단계에서 접근하지 못했었다. 상호작용을 연산하기 위해서는 각각의 임베딩을 구한 이후에야 내적을 통해 계산할 수 있었다.

### NGCF의 아이디어

NGCF는 이 문제를 보고 유저와 아이템의 상호작용이 임베딩 단계에서 부터 학습이 진행될 수 있도록 만들었다. 일반적인 유저와 아이템의 상호작용 그래프를 생성하면 유저와 모든 아이템간의 상호작용을 표현할 수는 없었다. 다음 예시를 보자.

![image](https://user-images.githubusercontent.com/91870042/158606967-0a6ec201-b129-49d9-b9c6-83f2bfbef99e.png){: .align-center}

위의 그림에서는 유저1 $u_1$ 이 아이템4 $i_4$ 와 상호작용하는 부분이 없기 때문에 학습을 통해서 추천하기가 힘들었다. 이 부분을 GNN을 통해 경로가 1보다 큰 연결(High-order Connectivity)을 하여 그 관계를 우측 그림처럼 임베딩시켰다.

### NGCF의 전체 구조

![image](https://user-images.githubusercontent.com/91870042/158607604-59a272e2-88ba-4158-b309-c6011a97be31.png){: .align-center}

- 임베딩 레이어 : 유저와 아이템의 초기 임베딩을 생성한다.
- 임베딩 전파 레이어 : high-order connectivity를 학습
- 유저와 아이템의 선호도 예측 레이어 : 서로 다른 전파 레이어에서 만들어진 임베딩을 연결해서 최종 예측한다.

위의 사진을 자세히 보면 왼쪽은 유저노드를 기준으로 임베딩이 전파되는 유저 임베딩 레이어이고 오른쪽은 아이템 노드를 기준으로 임베딩이 전파되는 아이템 임베딩 레이어이다.

### 임베딩 레이어

![image](https://user-images.githubusercontent.com/91870042/158610508-1264c272-269e-4812-a64d-63dbc9723794.png){: .align-center}

기존의 협업필터링에서는 생성된 임베딩을 곧바로 Interaction Function 에 입력되었짐나, NGCF에서는 이 임베딩을 GNN으로 전파시켜 refine 하는 과정을 거쳐서 정제된 임베딩을 생성한다. 이 과정은 Collaborative Signal을 명시적으로 임베딩 레이어에 주입하기 위해서이다.

### 임베딩전파 레이어 (#1. First-order Propagation)

유저와 아이템의 collaborative signal을 담을 메세지를 구성하고 결합하는 단계이다. 아래에서 등장하는 수식은 유저 임베딩을 기준으로 작성되었다.

> Message Construction

$$
m_{u\leftarrow i}=\frac{1}{\sqrt{|\mathcal{N}_u||\mathcal{N}_i|}}\left(W_1e_i+W_2(e_i\odot e_u)\right)
$$

- $m_{u\leftarrow i}$ : 하나의 아이템 노드에서 유저에게 전달되는 Message. 하나의 유저는 여러개의 아이템을 가질 수 있기 때문에 아래의 식에서 결합을 진행
- $\mathcal{N}_u,\mathcal{N}_i$ : 유저,아이템의 이웃한 유저, 아이템 집합
  
`아이템 임베딩` ($e_i$) 과 `아이템임베딩과 유저임베딩을 성분곱한 결과`를 더한 값을 정규화 시켜서 Message를 생성한다. 정규화를 시키는 것은 유저를 기준으로 연결된 아이템이 많을 수록 Collaborative Signal값이 커지기 때문에 이웃한 노드의 개수로 나누어 준다.

> Message Aggregation

$$
e_u^{(1)}=\text{LeakyReLU}\left(m_{u\leftarrow u}+\sum_{i \in \mathcal{N}_u}m_{u\leftarrow i} \right)
$$

- $e_u^{(1)}$ : 첫번째 레이어에 전파되는 유저 임베딩 값
- $m_{u\leftarrow u}$ : 자신으로 부터 전달되는 Message
- $\sum\_{i \in \mathcal{N}\_u}m_{u\leftarrow i}$ : 유저와 연결된 모든 아이템으로 부터 오는 Message의 합

위의 수식은 이웃한 노드로 부터 전파된 Message를 결합한 것이기 때문에 `1-hop` 전파를 통한 임베딩이라고 한다.

### 임베딩전파 레이어 (#2. High-order Propagation)

앞서서 설명했듯이, `1-hop` 만을 기준으로 하면 유저가 소비하지 않은 아이템과는 연결이 이루어지지 않는다. 그렇기 때문에 $l$ 차 이웃으로 부터 전파된 메세지를 사용할 수 있어야 한다.

![image](https://user-images.githubusercontent.com/91870042/158610639-d2d71e4a-bdbf-4305-ae08-18b1982b0185.png){: .align-center}

기존에 `1-hop` 까지 계산한 임베딩을 사용하는 것이 아니라 `3-hop` 까지 계산된 임베딩 값을 사용함으로써 언급한 문제를 해결할 수 있다. 위의 과정을 수식적으로 나타내보면 First-Order Propagation과 유사한 형태를 띄고 있다.

$$
\begin{aligned}
m_{u\leftarrow i}^{(l)}&=p_{ui}\left( W_1^{(l)}e_i^{(l-1)}+W_2^{(l)}(e_i^{(l-1)}\odot e_u^{(l-1)})\right)\\m_{u\leftarrow u}^{(l)}&=W_1^{(l)}e_u^{(l-1)}
\end{aligned}
$$

$$
e_u^{(l)}=\text{LeakyReLU}\left(m_{u\leftarrow u}^{(l)}+\sum_{i \in \mathcal{N}_u}m_{u\leftarrow i}^{(l)} \right)
$$

### 선호도 예측 레이어

임베딩 전파레이어에서 각 `hop` 마다 연산된 임베딩 벡터를 모두 concatenate하여 최종 임베딩 벡터를 계산한다. 만들어진 임베딩 벡터간의 내적 연산을 수행해서 최종 선호도 값을 예측한다.
    
$$
e_u^*=e_u^{(0)}\Vert\cdots\Vert e_u^{(L)} \qquad e_i^*=e_i^{(0)}\Vert\cdots\Vert e_i^{(L)}
$$

- $\Vert$ 는 concatenation 연산자를 말한다.

$$
\hat{y}_{NGCF}(u,i)=e_u^{*T}e_i^*
$$

### 결과 및 요약

![image](https://user-images.githubusercontent.com/91870042/158611026-e8de51d7-1d65-4e76-be05-8399ca081c28.png){: .align-center}

실험 결과 임베딩 전파 레이어가 많아질수록 모델의 추천 성능이 향상되었다. 하지만, 레이어가 너무 많이 쌓이면 overfitting이 발생하였다. 위의 사진에서 레이어의 수가 3 ~ 4일 때 가장 좋은 성능을 보이는 것을 알 수 있다.

![image](https://user-images.githubusercontent.com/91870042/158611441-726d1eb0-0cd1-41e4-8ce3-5c736ac36465.png){: .align-center}

Matrix Factorization과 비교를 하면 NGCF는 더 빠르게 수렴하고 recall도 더 높은 값을 갖는다. 그 이유는 모델의 표현력과 파라미터의 수가 크고 임베딩 전파를 통한 representation 능력이 좋아졌기 때문이다.

![image](https://user-images.githubusercontent.com/91870042/158611761-1166d97f-3032-40f6-9302-4c1ebd0af736.png){: .align-center}

MF와 비교하여 시각화했을 때, 유저-아이템이 임베딩 공간에서 더 명확하게 구분됨을 알 수 있다.

## Light GCN

![image](https://user-images.githubusercontent.com/91870042/158612219-bb055c17-ae84-4454-8bc9-6db98fe2d6cf.png){: .align-center}

GCN에서 가장 핵심적인 부분만 사용해서 더 정확하고 가벼운 추천 모델을 제시한 논문이다.

### Light GCN의 아이디어

- Light Graph Convolution : 이웃 노드의 임베딩을 가중합 하는것이 LGCN의 컨볼루션 연산의 전부이다. 그렇기 때문에 학습 파라미터와 연산량이 감소하였다.
- Layer Combination : 레이어가 깊어질 수록 학습 강도가 약해질 것이라는 아이디어를 적용해서 모델을 단순화 시켰다.

### Light GCN의 모델 구조

![image](https://user-images.githubusercontent.com/91870042/158612555-f2efb5e2-6dfa-4a9b-b911-ea524bb2f141.png){: .align-center}

NGCF의 임베딩 전파레이어 연산을 단순화 시켰으며 각 레이어에서 나온 임베딩 결과를 concatenation 하지 않고, 가중합을 사용하였다.

### LGCN의 임베딩전파 레이어

LGCN은 연결된 노드만 사용하였기 때문에 자기자신으로 들어오는 Message를 학습하는 부분이 없다. 또한, 임베딩 레이어에서 학습파라미터가 없는 것을 확인할 수 있다. (단, 0번째 임베딩 레이어에는 학습파라미터가 존재한다.)

$$
e_u^{(k+1)}=\sum_{i\in \mathcal{N}_u}\frac{1}{\sqrt{|\mathcal{N}_u|}\sqrt{|\mathcal{N}_i|}}e_i^{(k)}\\
e_i^{(k+1)}=\sum_{u\in \mathcal{N}_i}\frac{1}{\sqrt{|\mathcal{N}_i|}\sqrt{|\mathcal{N}_u|}}e_u^{(k)}
$$

### LGCN의 예측 레이어

임베딩된 결과를 모두 concatentation 하지 않고, 가중합을 사용해서 최종 임베딩 벡터를 생성한다. 이때 가중합을 하는 가중치는 하이퍼파라미터로 설정할 수도 있고, 학습 파라미터로 설정할 수도 있다. 하지만, 큰 차이가 없기 때문에 하이퍼 파라미터로 설정을 한다. 

논문에서는 하이퍼파라미터 값으로 $(k+1)^{-1}$ 로 지정하였다. 레이어가 깊어질 수록 가중치되는 값도 줄어든다. 이는 처음에 언급했던 것으로 레이어가 깊어지면 전달되는 Collaborative Signal도 약해질 것이다 라는 아이디어를 수식으로 나타낸 것이다.

$$
e_u=\sum_{k=0}^Ka_ke_u^{(k)}\qquad e_i=\sum_{k=0}^Ka_ke_i^{(k)}\qquad
a_k=\frac{1}{k+1}
$$

### 결과 및 요약

![image](https://user-images.githubusercontent.com/91870042/158613484-65163042-adb3-447a-8082-4485a07e11f3.png){: .align-center}

LGCN이 학습을 통한 손실함수와 추천 성능 모두 NGCF보다 뛰어나다. training loss가 낮으면서 추천 성능이 좋다는 것은 모델의 일반화 성능이 높다는 것이다.

NGCF는 임베딩 레이어마다 학습 파라미터가 존재해서 계산이 복잡해 overfitting의 위험이 있다. 하지만 LGCN은 레이어의 가중치를 없애서 학습을 단순화 시켰지만 컨볼루션 연산을 그대로 사용해서 성능은 높일 수 있었다.