---
title:  "[Recommender System] Model Based Collaborative FilteringPermalink"
excerpt: "모델을 기반으로 한 협업필터링 MBCF"

categories:
  - RecSys
tags:
  - [AI, Naver, BoostCamp, Recommender System]
toc: true
toc_sticky: true
 
date: 2022-03-14 07:00:00
last_modified_at: 2022-03-14 07:00:00
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
