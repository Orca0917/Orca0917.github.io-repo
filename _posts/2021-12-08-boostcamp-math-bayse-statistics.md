---
title:  "[부스트캠프 Pre-Course] AI수학: 조건부확률과 베이즈정리"
excerpt: "조건부확률에서 이어지는 개념인 베이즈 정리와 인과관계 추론"

categories:
  - boostcamp
tags:
  - [AI, Naver, BoostCamp, Python, Math]
toc: true
toc_sticky: true
 
date: 2021-12-08
last_modified_at: 2021-12-08
---

# <span style = "color: #00adb5">조건부 확률</span>
베이즈 통계학을 이해하기 위해서는 먼저 조건부확률의 개념을 이해해야 한다.

\\(P(A\bigcap B) = P(B)P(A\|B)\\)


위의 식에서 조건부 확률 \\(P(A\|B)\\)는 사건 \\(B\\)가 일어난 상황에서 사건 \\(A\\)가 발생할 확률을 의미한다. 다시 말해 \\(P(B)\\)를 좌항으로 넘기면, 사건\\(B\\)가 일어난 상황에서, 사건\\(A\\)와 사건\\(B\\)가 일어난 상황을 말한다.

<br>

# <span style = "color: #00adb5">베이즈 정리</span>
베이즈 정리는 조건부 확률을 이용하여 `정보를 갱신하는 방법`에 대해서 알려준다. \\(A\\)라는 새로운 정보가 주어졌을 때, \\(P(B)\\)로 부터 \\(P(B|A)\\)를 계산하는 방법을 제공해주는 것이다.

![image](https://user-images.githubusercontent.com/91870042/145143618-bd9caf72-9f4c-43d1-b121-359cf58c3283.png){: .align-center}

**베이즈 정리는 사전확률, 민감도(Recall), 오탐율(Ralse alarm)을 가지고 정밀도(Precision)을 계산하는 문제**이다.

![image](https://user-images.githubusercontent.com/91870042/145144002-acc4e8a1-36b6-4484-9238-db74eed896db.png){: .align-center}

1. \\(D\\): 새로 관찰하는 데이터
2. \\(\theta\\): 가설, 모델링하는 이벤트, 모델에서 계산하고 싶은 parameter

- `사후확률(posterior)`: 데이터가 주어져있을 때, 가설이 성립할 확률을 말한다.
- `사전확률(prior)`: 데이터가 주어지지 않은 상황에서, 가설\\(theta\\)의 주어진 확률이다.
- `가능도(likelihood)`: 현재 주어진 가설에서 이 데이터가 관찰될 확률이다.
- `Evidence`: 데이터 자체의 분포를 말한다.


## 예제
COVID-99의 발병률이 10%로 알려져 있다. COVID-99에 실제로 걸렸을 때 검진될 확률은 99%, 실제로 걸리지 않았을 때 오검진 될 확률이 1%라고 할 때, 어떤 사람이 질병에 걸렸다고 검진결과가 나왔을때 정말로 COVID-99에 감염되었을 확률은?

위의 그림에서 **\\(\theta\\)를 COVID-99 발병사건으로 정의**하고, **\\(D\\)를 테스트 결과라고 정의**해보자. 그러면 다음과 같이 표현할 수 있다.

- \\(P(\theta)=0.1\\): COVID-99의 발병률
- \\(P(D\|\theta)=0.99\\): 실제로 걸렸을 때, 검진될 확률
- \\(P(D\|\neg \theta)=0.99\\): 실제로 걸리지 않았을 때, 오검진될 확률

위의 정보들을 이용해서 Evidence값을 구해야한다.
> Evidence = P(A\|B)*P(B) + P(A\|¬B)*P(¬B)

- \\(P(D) = \sum_\theta P(D\|\theta)P(\theta) = 0.99\times 0.1 + 0.01 \times 0.9 = 0.108\\)
- \\(P(\theta \| D) = 0.1 \times \frac{0.99}{0.108} \approx 0.916\\)

## 조건부 확률의 시각화
![image](https://user-images.githubusercontent.com/91870042/145146703-1ba84253-0369-492b-a4dd-6e6a74691daf.png){: .align-center}

## 베이즈 정리를 통한 정보의 갱신
베이즈 정리를 통하여 새로운 데이터가 들어왔을 때, 앞서 계산한 **사후확률을 사전확률로 사용**하여 갱신된 사후확률을 계산할 수 있다.

![image](https://user-images.githubusercontent.com/91870042/145147004-2a1f6851-d5a7-4237-aa71-75bbf64b72a7.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/145147127-3a93acbf-dac8-4ca6-82f7-39ff808667c7.png){: .align-center}

이와 같은 방식으로 3번째 검사해도 양성이 나온다면, 그 정밀도는 99.1%까지 상승한다.

## 조건부 확률과 인과관계
> 조건부 확률은 유용한 통계적 해석을 제공하지만,  
> 인과관계를 추론할 때 함부로 사용해서는 안된다.

데이터가 많아져도 조건부 확률만을 가지고 인과관계를 추론하는 것은 불가능하다.

![image](https://user-images.githubusercontent.com/91870042/145147335-c00c7624-a199-4bbb-a398-35c0163ec8d3.png){: .align-center}

조건부확률만을 가지고, 예측모델을 만들었을 때, 다양한 시나리오 마다 예측정확도가 많이 차이나는 모습을 볼 수 있다. 이럴 경우, 인과관계를 사용하여 계산하면 여러 시나리오에도 예측정확도가 크게 변하지 않는 상황을 만들 수 있다.

인과관계를 알아내기 위해서는 `중첩요인(confounding factor)`의 효과를 제거하고 원인에 해당하는 변수만의 인과관계를 계산해야한다.

![image](https://user-images.githubusercontent.com/91870042/145147547-341bb919-1fa3-4b8f-8f28-c06b6687fd1c.png){: .align-center}

### 예시1: 키와 지능의 관계
중첩요인을 제거하지 않은 상태에서, 지능지수와 키의 연관성분석을 하면 놀랍게도 둘 사이의 연관성은 있다고 나온다. (R: 지능지수, T: 키)

그 이유는 나이라는 중첩지수를 제거하지 않았기 때문이다. 나이가 많아질 수록, 키가 커지고 지능도 높아지기 때문에, 둘 사이의 인과관계가 있다고 나오는 것이다. 이렇듯 인과관계를 알아낼 때는 `중첩요인`을 제거하는 것이 중요하다.

### 예시2: 신장결석의 크기와 치료법
![image](https://user-images.githubusercontent.com/91870042/145147727-33e54297-05be-4051-af8f-525bb8cbecdf.png){: .align-center}

전체적으로 봤을 때는 치료법B가 더 완치율이 높지만, 각각의 환자로부터 결과를 봤을 때는 치료법A의 완치율이 높다. 어떻게 이런일이 발생할까? 이러한 현상을 `simpson's paradox`라고 한다.
이 역설을 해결하기 위해서는 조건부 확률만을 가지고 계산해서는 안된다. 이 모델에서 중첩효과를 제거해야 가능하다.

신장 결석 크기에 따른 치료법의 선택을 중첩효과를 제거하는 방식에 사용해볼 수 있다. 전체적인 결과로 치료법B가 더 좋다고 나온 이유는 신장결석의 크기라는 숨겨진 변수를 전혀 고려하지 않은 상황이기 때문이다.

이렇게 중첩효과를 제거하고 계산을 했을 때, 치료법 A는 78%에서 83%로, 치료법B는 83%에서 77%로 나타나는 것을 볼 수 있다.

![image](https://user-images.githubusercontent.com/91870042/145147753-256a368d-1674-45f5-9637-36e2efa76ad4.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/145147797-3c7cc452-26fd-4777-b5f7-a46caca61472.png){: .align-center}

# <span style = "color: #00adb5">References</span>
[📘 부스트캠프 AI Tech 3기 Pre-Course: 베이즈 통계학 맛보기](https://www.boostcourse.org/onlyboostcampaitech3/lecture/1203366/?isDesc=false)

[📘 위키백과: 심슨의 역설](https://ko.wikipedia.org/wiki/%EC%8B%AC%EC%8A%A8%EC%9D%98_%EC%97%AD%EC%84%A4)
