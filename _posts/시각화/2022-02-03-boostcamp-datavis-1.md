---
title:  "[Data Visualization] 시각화의 요소"
excerpt: "세상에 존재하는 데이터셋과 각 데이터셋의 구성, 종류 그리고 시각화에서 사용하는 점, 선, 면에 대한 설명"

categories:
  - datavis
tags:
  - [AI, Naver, BoostCamp, Python, Matplotlib]
toc: true
toc_sticky: true
 
date: 2022-02-03 00:00:00
last_modified_at: 2022-02-03 00:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# 데이터 이해하기
## '데이터' 시각화
데이터를 시각화하기 위해서 먼저, **데이터**가 필요하다. 이 데이터는 다시 여러 종류로 나누는 것이 가능한데 어떤 관점에서 보느냐에 따라 서로 다른 분류가 이루어진다.

- Global : 데이터셋 관점
- Local : 개별 데이터의 관점

## 데이터셋의 종류
데이터셋에는 수많은 데이터셋들이 존재하고 그 중 몇가지 유형에 대해서 살펴보자. 이번에 살펴볼 유형은 총 6가지이며 그 종류는 다음과 같다.

`정형데이터`, `시계열데이터`, `지리데이터`, `관계형(네트워크)데이터`, `계층적데이터` `비정형데이터`

### 정형데이터
정형데이터는 일반적으로 CSV파일로 제공되는 데이터를 생각하면 된다. 행과 열이 교차되는 각 Cell인 개별적인 데이터를 가지고 시각화를 진행한다.
- row : 열, 1개의 item
- column : 각 데이터가 가질 수 있는 속성 (feature)

![image](https://user-images.githubusercontent.com/91870042/152306332-b438f162-0731-42a6-9a6b-58c90f33c349.png){: .align-center width="70%"}


정형데이터로는 평균, 분포와 같은 **통계적 특성**과 **feature**사이의 관계를 분석하거나 데이터 간의 관계 또는 비교를 시각화하는데 사용된다.

### 시계열데이터
시계열데이터(Time Series Data)는 시간 흐름에 따른 데이터를 말한다. 그 예로, 주가 또는 기온과 같은 정형데이터나 음성 및 비디오와 같은 비정형데이터가 있다. 시계열 데이터는 시각화하여 시간 흐름에 따른 추세(Trend), 계절(Seasonality), 주기성(Cycle)등을 살펴본다.

![image](https://user-images.githubusercontent.com/91870042/152306471-0929b1f6-a658-49f1-9886-68c8940f90a6.png){: .align-center width="70%"}

### 지리/지도데이터
지도 정보를 보고자 하는 정보간의 조화가 중요하며 실생활에서는 거리, 경로, 분포등 다양하게 지도에 적용되어서 사용되고 있다. 예를 들어, 지도에 표시되는 코로나 선별진료소의 위치와 어디에 밀집되어 많이 존재하는지 등 알아 볼 수 있는 것이 있다.

![image](https://user-images.githubusercontent.com/91870042/152306816-fc40dc4e-54cc-4202-aad1-569d1e5be2df.png){: .align-center width="70%"}

### 관계데이터
관계데이터는 객체와 객체간의 관계를 그래프로 표현함으로써 시각화한다. 객체는 노드, 관계는 간선으로 표현하며 중요도나 가중치를 그 크기나 색상, 수로 나타낸다.

![image](https://user-images.githubusercontent.com/91870042/152307546-eeeb5899-09d0-4a77-a19c-3c36f1d83148.png){: .align-center width="50%"}

### 계층적데이터
관계그래프 중에서도 회사의 조직도나 가계 족보와 같은 계층적 데이터가 존재한다. 이 계층적데이터는 그래프로도 표현이 가능하지만 계층구조를 강조하는 데이터의 시각화가 존재한다.

계층구조를 시각화 하기위해서 `tree`, `treemap`, `sunburst`를 사용한다.

![image](https://user-images.githubusercontent.com/91870042/152307996-1e746a33-0570-48a5-9a23-ae65a2d3ed07.png){: .align-center width="70%"}

## 데이터의 종류
- `수치형 데이터` (numerical data)
  - `연속형` (Continuous) : 길이, 무게, 온도
  - `이산형` (Discrete) : 주사위 눈금, 사람 수
- `범주형 데이터` (categorical data)
  - `명목형` (Norminal) : 혈액형, 종교
  - `순서형` (Ordinal) : 학년, 별점, 등급
  
<br>

# 시각화 이해하기
## 마크와 채널
> A **mark** is a basic graphical element in an image.  
> A visual **channel** is a way to control the appearance of marks, independent of the dimensionality of the geometric primitive.

대부분의 시각화는 **점, 선, 면**을 어떤식으로 배치하고 조화하는지의 문제이다. 점, 선, 면에서 변형을 줄 수 있는 것들을 채널이라고 한다.

## 전주의적 속성
**전주의적 속성**(Pre-attentive Attribute)은 주의를 주지 않아도 인지하게 되는 요소이다. 이 전주의적 속성을 사용하면 효과적으로 말하고자 하는 정보를 전달할 수 있다. 이 전주의적 속성을 <u>동시에 여러개 사용하게 되면 인지하기 어렵지만</u>, 적절하게 사용했을 때, 시각적 분리효과를 일으켜 인지시킬 수 있다.

![image](https://user-images.githubusercontent.com/91870042/152309070-9c61a6cb-0817-4334-b658-6c7d323c15ba.png){: .align-center width="70%"}

<br>

# References

[🔗 Img resource :: Preattentive Attributes](https://www.pinterest.co.kr/pin/52706258122469670/)

[🔗 Img resource :: 주식 예측 프로그램 : 시계열 데이터 개념, 뜻](https://jjeongil.tistory.com/657)

[🔗 Img resource :: '혼잡·혼잡·혼잡'…7000명대 확진에 선별진료소 대부분 '긴줄'](https://www.donga.com/news/Society/article/all/20211209/110710428/1)

[🔗 Img resource :: Examples of visualisation for hierarchical data](https://www.researchgate.net/figure/Examples-of-visualisation-for-hierarchical-data-A-Tree-radial-layout-B-Pack-layout_fig1_269187743)

[🔗 Img resource :: Generating A Twitter Ego-Network & Detecting Communities](https://towardsdatascience.com/generating-twitter-ego-networks-detecting-ego-communities-93897883d255)