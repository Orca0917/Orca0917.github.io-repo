---
title:  "[Recommender System] 추천시스템과 딥러닝(MLP)"
excerpt: "딥러닝을 추천시스템에 사용했을 때의 장점과, Multi Layer Perceptron을 추천시스템에 적용하는 방법에 대해서 알아보자"

categories:
  - RecSys
tags:
  - [AI, Naver, BoostCamp, Recommender System]
toc: true
toc_sticky: true
use_math: true

date: 2022-03-14 12:00:00
last_modified_at: 2022-03-14 12:00:00
---
📌 **알립니다!** <br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다. <br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Recommender System with Deep Learning

추천시스템에 딥러닝을 적용시켰을 때의 장점은 다음 4가지가 있다.

1. **Nonlinear Transformation**
    - Deep NN은 데이터의 **비선형성을 효과적으로 나타낼 수 있다**.
    - 복잡한 user-item interaction pattern을 효과적으로 모델링하여 user의 선호도를 예측할 수 있다.
2. **Representation Learning**
    - DNN은 raw data로부터 feature representation을 학습해 **사람이 직접 feature design을 하지 않아도 된다.**
    - 텍스트, 이미지, 오디오 등 다양한 종류의 정보를 추천시스템에 활용할 수 있다.
3. **Sequence Modeling**
    - DNN은 자연어처리, 음성신호처리등 **sequential modeling task에서 성공적으로 적용된다**. 유저나 아이템의 시간적 정보를 활용!!
    - 추천시스템에서 next-item prediction, session-based recommendation에 사용된다.
    - 현재까지 소비한 아이템을 바탕으로 다음 구매할 것을 추천
4. **Flexibility**
    - Tensorflow, PyTorch등 다양한 딥러닝 프레임워크를 사용할 수 있다.
    - 추천시스템 모델링의 유연성이 높으며 더 효율적으로 서빙이 가능하다.

![image](https://user-images.githubusercontent.com/91870042/158174366-abe26a1e-4687-485e-9e7e-3401b458a6a9.png){: .align-center width="50%"}
<p align="center"><i>Tensorflow와 PyTorch 프레임워크</i></p>

<br/>

# Recommender System with MLP

다층 퍼셉트론의 개념에 대해서 간단하게 알아보고, 이를 활용한 추천시스템 모델에 대해 살펴보자

## MLP

![image](https://user-images.githubusercontent.com/91870042/158174754-f9806d04-5d10-42a5-961d-2335834dab37.png){: .align-center width="70%"}
<p align="center"><i>Perceptron과 MLP의 구조</i></p>

Multi Layer Perceptron(MLP)는 다층 퍼셉트론으로 퍼셉트로 이루어진 레이어를 여러겹 쌓아 순차적으로 이어놓은 Feed-Forward Neural Network이다. 다층 퍼셉트론을 사용하게 되면, 선형분류만으로 해결하기 어려웠던 문제들을 딥러닝으로 비선형적으로 해결할 수 있다.

## Neural Collaborative Filtering

![image](https://user-images.githubusercontent.com/91870042/158174885-e2fd0f2c-d91f-41e3-a15d-5beffc0e75ff.png){: .align-center width="70%"}
<p align="center"><i>Neural Collaborative Filtering 논문</i></p>

기존 Matrix Factorization 의 한계를 지적하며 신경망 기반의 구조를 사용해 추천시스템의 일반화 성능을 높이는 모델에 대해 설명한 논문으로 Neural Collaborative Filtering 이 있다.

### 아이디어

기존 MF는 유저와 아이템 사이의 선형조합을 구할 수 있었지만, 복잡한 관계를 표현하는 데에는 한계를 가지고 있었다. 논문에서 소개하는 문제점으로 다음 예시를 들어볼 수 있다.

![image](https://user-images.githubusercontent.com/91870042/158175691-10912750-718c-461b-b72d-eef788327d1d.png){: .align-center width="70%"}
<p align="center"><i>Matrix Factorization의 문제점</i></p>

Jaccard 유사도가 $S_{23}>S_{12}>S_{13}$일 때, $p_1, p_2, p_3$을 잠재공간에 나타내면 위의 오른쪽 그림처럼 나타난다고 해보자.
여기서, 새로운 사람 $u_4$를 추가하였을 때 가장 유사한 유저는 $u_1$이고, 그다음으로 유사한 유저는 $u_3$이다. 하지만 여기서 $p_3$보다 $p_2$가 유사해질 수 밖에 없는 모순이 발생한다.

### 모델 (#1. MLP)

![image](https://user-images.githubusercontent.com/91870042/158176699-edba5945-57c4-47fd-b77c-9b2c69aa03fe.png){: .align-center width="70%"}
<p align="center"><i>MLP만 모델에 사용한 구조</i></p>

- `Input Layer` : **one-hot encoding된 sparse한 user(item) vector**: $v_u(v_i)$
- `Embedding Layer` : user(item)의 잠재벡터: $P^Tv_u(Q^Tv_i)$
- `Neural CF Layers` : $\phi_x(\cdots\phi_2(\phi_1(P^Tv_u, Q^Tv_i))\cdots)$ ⇒ 일반적인 MLP Layer를 말한다.
- `Output Layer` : user와 item사이의 관련도
    - 활성함수로는 logistic(sigmoid) 또는 probit함수 사용
    - $\hat{y}_{ui}=\phi_{out}(\phi_x(\cdots\phi_2(\phi_1(P^Tv_u, Q^Tv_i))\cdots)),\quad \hat{y}_{ui}\in[0,1]$

### 모델 (#2. MLP + GMF)

![image](https://user-images.githubusercontent.com/91870042/158177094-f2e1f160-4b31-4167-833c-c7813ce45845.png){: .align-center width="70%"}
<p align="center"><i>MLP와 함께 GMF를 사용한 모델 구조</i></p>

GMF는 Generalized Matrix Factorization으로 MF를 일반화한 모델을 말한다. Neural Collaborative Filtering은 GMF와 MLP를 앙상블하여 각각의 장점을 살리고 단점을 보완하였다.

GMF와 MLP에서 진행되는 임베딩 레이어는 서로 다른 임베딩 레이어를 사용한다.

$$
\begin{aligned}
\phi^{GMF}&=(p_u^G)^Tq_i^G\\
\phi^{MLP}&=\phi_X(\cdots \phi_2(\phi_1(p_u^M, q_i^M))\cdots)\\
\hat{y}_{u,i}&=\sigma\left(h^T\begin{bmatrix}
 \phi^{GMF}\\
\phi^{MLP}
\end{bmatrix} \right)
\end{aligned}
$$

### 결과

![image](https://user-images.githubusercontent.com/91870042/158177780-b69a7035-981c-47fd-bb03-31e2081f37a7.png){: .align-center width="70%"}
<p align="center"><i>GMF+MLP를 사용한 추천시스템의 결과</i></p>

테스트 데이터셋으로 사용한 MovieLens, Pinterest 데이터셋에 대해서 NCF의 추천성능이 기존 MF나 MLP보다 성능이 높게 나왔다.

## YouTube Recommendation

![image](https://user-images.githubusercontent.com/91870042/158178114-676d4924-710d-4880-be55-f4dd7f750dc7.png){: .align-center width="70%"}
<p align="center"><i>Deep Neural Networks for YouTube Recommendations 논문</i></p>

딥러닝 기반 추천시스템을 실제 유튜브 서비스에 적용한 논문으로 "Deep Neural Networks for YouTube Recommendations"가 있다. 실제로 유튜브는 해당 딥러닝 기반 추천시스템을 사용한다고 한다.

### 유튜브 추천 문제 특징

- **Scale**: 엄청 많은 유저와 아이템 vs. 제한된 컴퓨팅 파워
- **Freshness**: 잘 학습된 컨텐츠와 새로 업로드 된 컨텐츠를 **실시간으로 적절히 조합**해야 한다. (exploration, exploitation)
    - 과거의 인기있는 영상만 추천만 하면 안된다.
    - 최근에 등장한 영상이 등장하도록 만들어야 한다.
- **Noise**: 높은 Sparsity, 다양한 외부요인으로 유저의 행동을 예측하기 어렵다.
    - 단순히 좋아요, 구독 등의 explicit데이터만 사용하면 유저의 행동을 예측하기가 어렵다.
    - Implicit Feedback, 낮은 품질의 메타데이터를 잘 활용해야 한다. (해당 영상을 얼마나 시청했는지 정보)

### 전체구조: 2단계 추천시스템

![image](https://user-images.githubusercontent.com/91870042/158178741-4d45050f-d9e4-4bb5-8a7c-1ef872c9c23c.png){: .align-center width="70%"}
<p align="center"><i>2단계로 구성된 YouTube Recommender System</i></p>

1. **Candidate Generation**  
    - High Recall 목표로 추천을 위한 1차적인 후보 목록을 생성한다.
    - 후보로 생성된 많은 비디오를 사용해서 주어진 사용자에 대해 Top N 추천아이템 생성
         - 그 중에서 최종적으로 내보낼 아이템을 추려낸다
  
2. **Ranking**  
    - 유저, 비디오 피쳐를 좀 더 풍부하게 사용
    - 스코어를 구하고 최종 추천 리스트를 제공

### Candidate Generation

![image](https://user-images.githubusercontent.com/91870042/158179507-c7fa1d2d-accf-4cbe-835f-f0134bedf836.png){: .align-center width="70%"}
<p align="center"><i>1단계: Candidate Generation</i></p>

> **Watch Vector와 Search Vector**

![image](https://user-images.githubusercontent.com/91870042/158179827-98c63243-e8e0-4c20-ad52-4c11ccec5cf7.png){: .align-center width="70%"}
<p align="center"><i>watch vector와 search vector의 생성</i></p>

- 과거의 시청이력과 검색 이력을 임베딩한다.
- 마지막 시청기록 또는 검색어가 너무 큰 힘을 갖지 않도록 평균을 낸다.

> **Demographic/Geographic features & Example Age**

![image](https://user-images.githubusercontent.com/91870042/158179891-a03fe18a-8bb7-443e-8c33-c511e76146ef.png){: .align-center width="40%"}
<p align="center"><i>Demographical Information의 사용</i></p>

- Demographic & Geographic features
    - 성별, 인구통계학 정보와 지리적 정보를 피쳐값으로 사용
- Example Age
    - 모델이 과거 데이터 위주로 편향되어 학습되는 문제
    - 시청 로그가 학습 시점으로부터 경과한 정도를 피쳐로 포함한다.
        - 이는 Bootstraping 현상 방지와 Freshness를 제ㄱ고ㅗ한다.

> **Dense Layer & softmax**

![image](https://user-images.githubusercontent.com/91870042/158179982-52bb0b27-8a33-45de-a6b6-190a8f6f1567.png){: .align-center width="70%"}
<p align="center"><i>Dense 레이어를 거쳐 softmax 연산을 진행</i></p>

- 다양한 feature벡터들을 한 번에 concatenate시키고, Dense Layer를 거쳐서 **user vector $u$ 생성**
- 최종 output layer는 **비디오를 분류하는 `softmax()` 를 거친다.**

> **Serving**

![image](https://user-images.githubusercontent.com/91870042/158180059-be9a0796-4f85-459e-ae26-37bd941ebed3.png){: .align-center width="40%"}
<p align="center"><i>최종 모델 서빙 과정 (추천아이템 서빙)</i></p>

- 유저를 input으로 하여 **상위 N개 비디오를 노출**
- 학습 후, 유저 벡터 $u$와 모든 비디오 벡터 $v_j$의 내적을 계산한다.
    - 하지만, 너무 많은 시간이 소모되기 때문에 실제 서비스에 적용하기 어렵다.
        - 아래와 같은 softmax연산을 해서 모든 비디오를 찾아주는 것은 힘들다..

$$
P(w_t=i|U,C)=\frac{e^{v_iu}}{\sum_{j\in V}e^{v_ju}}
$$

- 그렇기 때문에, 가장 빠르게 가까운 유사도를 가진 아이템을 찾아주는 Annoy, Faiss 같은 ANN 라이브러리를 사용하여 빠르게 서빙을 진행해야 한다.

### Ranking

이전 단계인 Candidate Generation 단계에서 생성한 비디오 후보들을 입력으로 하여 최종 추천될 비디오들의 순위를 매기는 작업을 한다. Logistic 회귀를 사용하며 손실함수로 단순한 클릭여부가 아니라 시청시간을 가중치로 한 값을 반영한다. (weighted logistic)

![image](https://user-images.githubusercontent.com/91870042/158180544-291b7c23-4c4e-4aee-bc70-c72baea6e97e.png){: .align-center width="70%"}
<p align="center"><i>2단계 Ranking - (1) Candidate Generation 단계의 결과를 입력으로 전달</i></p>

입력단에서는 `user actions feature`을 같이 사용한다. user action에는 다음과 같은 행동들이 있다.

- 유저가 특정 채널에서 얼마나 많은 영상을 봤는지
- 유저가 특정 토픽의 동영상을 본 지 얼마나 지났는지
- 영상의 과거 시청 여부등을 입력
- 유저가 어떤 언어를 사용하는지

사실 이러한 것들의 반영이 중요하기 때문에 딥러닝 보다는 도메인 전문가의 역량이 많이 필요한 부분이다.

![image](https://user-images.githubusercontent.com/91870042/158180999-d7e46564-40ee-47ec-9cc8-66441403c1f6.png){: .align-center width="70%"}
<p align="center"><i>2단계 Ranking - (2) Dense 레이어를 통과하여 최종 서빙 진행</i></p>

Raking 네트워크를 통과한 뒤, 비디오가 실제로 시청될 확률로 매핑하는 과정을 거친다.

$$ P(watch)\in[0,1] $$

손실함수로는 앞에서 말했던 것처럼 weighted cross entropy 를 사용하며 이는 비디오를 시청시간으로 가중치를 둔다. 해당 손실함수를 사용하면 낚시성, 광고성 컨텐츠를 업로딩하는 어뷰징 같은 것들을 감소시키는 효과를 받을 수 있다. 클릭은 했지만, 그 시청시간이 매우 짧기 때문에 학습에 있어서 반영이 되지 않기 때문이다.

### 요약 및 결과

- 딥러닝 기반 2단계 추천을 처음으로 제안한 논문이다.
    - **Candidate Generation**: 유저에게 적합한 수백개의 후보 아이템을 생성한다.
    - **Ranking**: 더 많은 feature를 사용해서 최종 추천 아이템 10~20개를 제공한다.
- Candidate Generation: 기존 CF 아이디어 기반으로 **다양한 피쳐를 사용해 추천 성능을 향상**시킨다.
    - 유저: watch/query 기록, demographic, geographic 피처, // 기존 CF는 user id 정보만 사용
    - 아이템: example age사용
- Ranking: 과거에 많이 사용된 선형/트리 기반 모델보다 제안 딥러닝 모델이 더 뛰어난 성능을 보인다.
    - Rich Feature: CG에서 사용한 피쳐 외에 더 많은 피쳐를 사용하여 Ranking
    - 단순 CTR 예측이 아닌, Expected Watch Time을 예측한다.

