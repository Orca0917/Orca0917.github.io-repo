---
title:  "[Deep Learning Basic] Historical Review"
excerpt: "딥러닝에 대한 소개, 딥러닝의 역사"

categories:
  - DLBasic
tags:
  - [AI, Naver, BoostCamp]
toc: true
toc_sticky: true
 
date: 2022-02-07 00:00:00
last_modified_at: 2022-02-07 00:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Introduction
## 좋은 딥러너가 되기 위한 자격
- Implementation Skills: 구현 능력 (Tensorflow, Pytorch)
- Math Skills: 수학 능력 (Linear Algebra, Probability)
- Knowing a lot of recent Papers: 최근의 논문 / 연구 결과들을 많이 알고 있는 것

## AI, ML, Deep Learning의 관계
![image](https://user-images.githubusercontent.com/91870042/144704573-50928379-2c3a-435a-9f71-338be2efc9a9.png){: .align-center width="70%"}

1. Artificial Intelligence: 사람의 지능을 모방하는 것들
2. Machine Learning: 데이터를 통해 학습시키는 것
3. Deep Learning: Machine Learning 중에서 Neural Network모델을 사용해 학습하는 더 세부적인 분야

## 딥러닝의 기본 요소

### Data
> "The data that the model can learn from"

학습하기 위해서 필요한 데이터. 예를 들어서 개와 고양이를 분류하는 문제에 대해서는 개와 고양이의 수 많은 사진들이 필요하다. 이런 데이터는 이런 데이터는 **풀고자 하는 문제**에 의존하게 된다. 풀고자 하는 문제에 대한 분류는 다음과 같이 크게 5가지가 있다.
    
![image](https://user-images.githubusercontent.com/91870042/144704834-e01eec9f-ec99-4cc8-a8c8-62e41472eddd.png){: .align-center width="70%"}

- `Classification`: 주어진 데이터에 대해서 분류하는 문제 (개와 고양이 분류)
- `Semantic Segmentations`: 한 이미지에 대해서 픽셀로 분석하는 문제 (각 부분이 어떤 클래스에 속하는지 판단)
- `Detection`: 한 이미지 내에서 특정 사물에 대한 경계박스를 찾는 문제
- `Pose Estimation`: 사람의 행동이 주어졌을 때, 그 사람이 움직이는 정보를 Skeleton정보로 바꾸는 문제
- `Visual QnA`: 어떤 사진이 주어지고, 그 사진에 대한 질문이 주어졌을 때 답을 말하는 문제.

### Model
> "The model how to transform the data"

위에서 말한 수 많은 데이터를 이용해서 학습하기 위해 필요한 것. 예를 들어, 이미지를 라벨로 바꾸어주는 것이 있다. 어떤 사물의 사진이 사진이 주어졌을 때, 그 사진의 라벨을 설정하는 모델이다. 같은 데이터가 주어지더라도 모델에 따라서 더 좋은 결과, 나쁜 결과가 나올 수 있어 좋은 모델의 선택이 필요하다.

![image](https://user-images.githubusercontent.com/91870042/144704950-a55c4c49-6bca-46a7-9ed1-bcfe91971561.png){: .align-center width="70%"}

### Loss Function
> "The loss function that quantifies the badness of the model"

**손실함수**는 어떤 모델을 통해서 나온 결과 값과 실제 값이 얼마나 유사한지 판단하는 기준이다. 따라서 그 모델이 얼마나 좋은 모델인지 나쁜 모델인지 판단하는 척도가 된다. 문제 유형에 따라서 자주 사용하는 손실함수가 있고, 하지만 반드시 그 문제에는 해당 손실함수를 사용해야한다는 것은 아니다. 상황에 맞는 손실함수를 사용하는 것이 좋다.

- Regression Task(회귀 문제): `MSE` loss function(Mean Squared Error)
- Classification Task(분류 문제): `CE`(Crossentropy Loss)
- Probabilistic Task(확률 문제): `MLE`(=MSE, Maximum Likelihood Estimation)

### Optimization Algorithm
> "The algorithm to adjust the parameters to minimize loss"

알고리즘은 손실함수를 통해 얻어낸 값을 최대한 줄이기 위해 존재한다. 각 최적화 알고리즘의 특성을 이해하는 것이 무엇보다 중요하다.

- SGD
- Momentum
- NAG
- Adagrad
- Adadelta
- Rmsprop

![optim_algs](https://user-images.githubusercontent.com/91870042/152749857-2dbc6550-1906-4226-abe4-5dd6030a3659.gif){: width="49%"} ![optim_algs2](https://user-images.githubusercontent.com/91870042/152749846-be0675a3-7194-462a-8973-75042d2a392b.gif){: width="49%"}

알고리즘에 학습이 잘 안되게 하는 방법을 추가하기도 하는데, 이는 학습하지 않은 데이터에서도 잘 동작하기 위함이다. 방금 말한 최적화 알고리즘과 함께 Dropout, Early stopping, k-fold validation, Weight decay, Batch Normalization, MixUp, Ensemble, Bayesian Optimization과 같은 테크닉을 사용해서 학습데이터 뿐만아니라, 한 번도 보지 못한 테스트 데이터에서도 잘 동작하도록 만든다.


# Historical Review

## 2012: AlexNet
![image](https://user-images.githubusercontent.com/91870042/144705553-df38d1a2-71b3-46a6-892e-b26e8c3e9138.png){: .align-center width="70%"}

224 * 224사진의 크기가 들어왔을 때, 사진을 분류하는 방법이다. 사진이 주어졌을 때 가장 높은 정확도 분류하는 대회에서 우승한 방법인데, 해당 모델이 딥러닝을 사용하여 우승하고 나서부터는 많은 곳에서 사용하고 연구하기 시작했다. **AlexNet**이 나오고부터, 딥러닝이 실제적 두각을 보이기 시작하게 되었다.

## 2013: DQN
![image](https://user-images.githubusercontent.com/91870042/144705577-b40c4409-7d15-47bb-87f9-0a2ac781ddbb.png){: .align-center width="70%"}

알파고를 만든 강화학습 알고리즘로 잘 알려져있다. Q러닝을 이용한 강화학습에 딥러닝을 접목한 방법이다. 딥마인드에서 개발을 하였고 현재는 구글에 인수되어 구글 딥마인드가 되었다.

## 2014: Encoder / Decoder, Adam Optimizer
![image](https://user-images.githubusercontent.com/91870042/144705597-eda4e098-d43c-4261-991c-82992ceb1992.png){: .align-center width="70%"}

NMT(Neural Machine Translayer)문제를 해결하는 방법이다. 영어로 되어 있는 문장이 주어졌을 때, 다른 언어의 문장으로 번역해서 잘 보여주는 것을 연구하는 방법이다.

![image](https://user-images.githubusercontent.com/91870042/144705618-a3ae31d0-743d-4de0-8f9f-888bc44041aa.png){: .align-center width="70%"}

**Adam**은 Adaptive Momentum을 합친말로, 정말 많이 사용하는 옵티마이저이다. 높은 정확도의 결과가 자주 나오고 많은 유명한 기업에서도 일반적으로 사용하는 옵타마이저이다.

## 2015: Generative Adeversarial Network (GAN), Residual Networks
![image](https://user-images.githubusercontent.com/91870042/144705628-9e48435c-a4e5-48d1-a84d-204f839790ed.png){: .align-center width="70%"}

**GAN**은 비지도 학습에 사용되는 인공지는 알고리즘으로, *zero sum*게임 안에서 경쟁하는 2개의 신경 네트워크 시스템에 의해서 구현된다. 논문에서 술집이름이 나오는데, 술집의 술이 너무 맛있없어서 그자리에서 연구를 했던것이 GAN이라고 한다.

![image](https://user-images.githubusercontent.com/91870042/144705641-e63ce07c-48e0-424e-a53b-c9f0f72c47b0.png){: .align-center width="70%"}

딥러닝 네트워크 층을 깊게 쌓으면 학습이 잘 안되고, 성능이 좋지 않게 나오는 것으로 알고 있었는데, ResNet이 나온 이후로 부터는 성능이 좋게 나올 수 있게 만들어 줬다. 네트워크 레이어를 높게 쌓을 수 있는 전환점이 되었다.

## 2017: Transformer
![image](https://user-images.githubusercontent.com/91870042/144705655-27dc40dc-5713-4758-a237-b92b58b4ecc1.png){: .align-center width="70%"}

`Attention is All You Need`라는 논문으로 발표된 알고리즘이다. 당시에는 모든 분야에 사용하기 힘들 것이다라고 했는데, 지금에 와서는 Transformer가 대부분 대체하였고 Vision분야도 엿보고 있다. 이 Transformer가 어떤 장점을 보이고 왜 좋은 성능을 내는지에 대해서는 후에 다시 다룬다.

## 2018 : BERT
![image](https://user-images.githubusercontent.com/91870042/144705658-915f56b3-fbf5-4f25-b472-ef7a373358cd.png){: .align-center width="70%"}

BERT는 언어 알고리즘으로, 현재까지 특정 단어를 입력하면 다음 나올 단어를 예측해준다. 해당 모델을 사용해서 문장을 완성하면 그럴 듯한 글이 완성된다. 이 모델에서 사용하는 데이터를 얻기 위해서 많은 양의 문장이 있는 Wikipedia같은 것을 사용한다. 이곳에서 얻은 굉장히 다양한 말뭉치로 pre training을 하고 내가 원하는 곳에 fine tuning을 하여 원하는 결과 값을 얻어낸다.

## 2019 : BIG Language Models
![image](https://user-images.githubusercontent.com/91870042/144705659-ffebbe59-a457-4ce2-aa78-920f3579493a.png){: .align-center width="70%"}

BERT와 비슷한 언어 모델로, 1750억개의 수많은 파라미터로 연구하는 모델이다. 위와 같이 fine-tuning을 사용하여 문장, 프로그램을 완성시킨다.

## 2020 : Self Supervised Learning
![image](https://user-images.githubusercontent.com/91870042/144705666-31f3e42f-ab98-4fb1-a4bf-4a1f69e403f1.png){: .align-center width="70%"}

특정 이미지 분류문제를 해결함에 있어서 데이터가 한정되어 있다는 것으로 부터 나온 모델이다. 기존에는 동일한 데이터를 가지고, `Model`과 `Loss function`을 변경해가면서 개선시켜나갔다. 이번에는 어떠한 특정 데이터로 한정하지 말고(라벨을 모르는 데이터) 구글의 아무 이미지를 가져와서 학습시키는 데이터에 추가하는 것이다. SimCLR이 큰 성공을 이루게 되고 이후에 많은 연구들도 진행되고 있다.

추가로, *Self Supervised Data Sampling*이라는 연구가 최근에 진행되고 있는데, 이는 오히려 데이터를 만들어낸다. 학습데이터를 가지고 있는 시뮬레이터를 통해서 얻어내는 것이다. 다시 그 학습데이터를 이용해 더 좋게 개선시킬 수 있다.


# References

[📘 Deep Learning, Historical Review](https://gngsn.tistory.com/103)