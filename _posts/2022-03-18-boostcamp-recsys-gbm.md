---
title:  "[Recommender System] Gradient Boosting Machine"
excerpt: "CTR 예측에 효과적으로 알려진 Gradient Boosting 기법의 원리를 이해하고, 대표적인 GBM 계열의 모델들에 대해 알아보자"

categories:
  - RecSys
tags:
  - [AI, Naver, BoostCamp, Recommender System]
toc: true
toc_sticky: true
use_math: true

date: 2022-03-18 01:00:00
last_modified_at: 2022-03-18 01:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Gradient Boosting Machine

CTR 예측에 효과적으로 알려진 Gradient Boosting 기법의 원리를 이해하고, 대표적인 GBM 계열의 모델들에 대해 알아보자

## 개요

GBM은 CTR 예측을 통해 개인화된 추천 시스템을 만들 수 있는 모델이며 8개의 오픈 CTR 데이터셋에 대해 다른 추천 모델보다 높은 성능을 보였다.

Boosting은 앙상블 기법의 일종으로, 의사결정 나무로 된 weak learner(정확도와 복잡도가 비교적 낮은 간단한 분류기)들을 연속적으로 학습하여 결합한 형태이다. 연속적으로 학습한다는 것을 이전단계의 weak learner가 취약했던 부분을 위주로 데이터를 샘플링하거나 가중치를 부여해 다음 단계의 learner를 학습하는 것을 말한다.

Boosting을 기반으로 하는 모델은 `AdaBoost` `Gradient Boosting Machine` `XGBoost` `LightGBM` `CatBoost` 가 있다.

## GBM

### Gradient Boosting

Gradient Boosting은 경사하강법을 사용해서 손실함수 값이 줄어드는 방향으로 weak learner 들을 반복적으로 결합함으로써 성능을 향상시키는 Boosting 알고리즘을 말한다. 분모에 파라미터가 아니라, 학습이 되는 F(x)가 들어갔다.

![image](https://user-images.githubusercontent.com/91870042/158930638-b1fc6be9-8e6d-4ef7-ad36-acf24aa7d3f9.png){: .align-center}

### Gradient Boosting as Residual Fitting

통계학점 관점에서 Gradient Boosting은 잔차를 적합하는 것으로 이해해볼 수 있다. 이 잔차를 학습하는 과정에 대해서 살펴보면, 이전 단계의 weak learner 까지의 잔차를 계산해서 이를 예측하는 다음 weak learner를 다시 학습시킨다.

![image](https://user-images.githubusercontent.com/91870042/158930832-6746a856-cf0b-46f1-9639-fed50db40aaf.png){: .align-center}

GBM은 대체로 Random forest보다 더 좋은 성능을 보이지만, 느린 학습속도와 과적합이 될 수 있는 단점이 존재한다.

## 대표적인 모델

Graient Boosting의 단점을 해결하기 위한 대표적인 모델 또는 라이브러리는 아래와 같이 3종류가 있다.

- XGBoost : Extreme gradient boosting 의 약자로 병렬처리 및 근사 알고리즘을 통해 학습속도를 개선하였다.
- LightGBM : MS에서 제안한 병렬처리 없이도 빠르게 Gradient Boosting을 학습할 수 있도록 하는 라이브러리다.
- CatBoost : 범주형 변수에 효과적인 알고리즘 등을 구현하여 학습 속도를 개선하고 과적합을 방지하고자 한 라이브러리다.