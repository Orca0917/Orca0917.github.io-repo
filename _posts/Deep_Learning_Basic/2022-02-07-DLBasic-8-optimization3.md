---
title:  "Optimization PART 3 : Regularization"
excerpt: "일반화 성능을 높이기 위한 Early Stopping 기법, penalty, augmentation, dropout, 배치 정규화등 여러 기술 정리"

categories:
  - DLBasic
tags:
  - [AI, BoostCamp]
toc: true
toc_sticky: true
 
date: 2022-02-09
last_modified_at: 2022-07-26
---

![image](https://user-images.githubusercontent.com/91870042/180658653-9df2033f-5e9e-4178-8283-2ad17f08c4c7.png){: .align-center width="100%"}

# Introduction

딥러닝 학습의 Optimization 중 3번째 파트인 Regularization에 대해서 알아봅시다. Regularization은 모델이 학습데이터만으로도 여러 데이터에 대해서 모두 일반화 성능이 높을 수 있도록 하는 일종의 학습 규제입니다. 일부러 너무 학습이 안되도록 방해하기도 하며 학습데이터를 늘려서 더 많은 데이터를 사용해 높은 성능이 나오도록 유도하기도 합니다. 아래에서는 Regularization에 사용되는 기술들에 대해서 하나씩 소개합니다.

<br>

## Early Stopping

Early Stop 말 그대로 학습을 조기에 종료한다는 의미입니다. Optimization PART 1 게시글에서 학습이 진행되면 진행될 수록 학습 데이터셋에 대해서는 오류가 계속해서 줄어들지만, 과적합이 발생해서 다른 데이터셋에 대해서는 오류가 커질 수 있다고 했었습니다. 그렇다면, 학습 데이터셋과 평가 데이터셋 모두 줄어들다가 둘 사이의 간격이 벌어지는 시점에 학습을 종료하면 전체적으로 일반화되어서 학습이 완료된 모델을 얻을 수 있습니다. 이런 원리를 사용한 것이 Early Stopping 기법입니다.

![image](https://user-images.githubusercontent.com/91870042/180654929-2a02ba3f-7693-4f32-9ced-c272d82c8c5c.png){: .align-center width="70%"}

<br>

## Parameter Norm Penalty

Parameter Norm Penalty는 학습 파라미터에 규제를 거는 것을 의미합니다. 학습 파라미터에 규제를 걸지 않고 학습을 진행하다보면 특정 파라미터의 가중치가 너무 커지는 값을 갖는 경우가 생길 수 있습니다. 이럴 경우, 여러 데이터에 대해 모두 좋은 성능을 보이기는 힘들 수 있기 때문에 너무 커지지 않도록 파라미터의 크기에도 규제를 거는 것입니다. 그림으로 바라보면 다음과 같습니다.

![image](https://user-images.githubusercontent.com/91870042/180941068-e903ebe0-4405-437c-ae27-8fdd5dc33bd4.png){: .align-center width="35%"}

빨간 점들을 나타내는 곡선을 찾아내는 모델이 있다고 했을 때, 파라미터의 penalty를 부여하지 않은 겨우에는 파란 선과 같이, penalty 가 부여된 경우에는 초록색 선과같이 어느정도 일관성 있는 그래프를 그릴 수 있습니다. 파라미터에 규제를 거는 대표적인 방법에는 2가지가 있습니다. L1-노름을 사용한 규제와 L2-노름을 사용한 규제가 존재합니다.

L1-Norm 을 사용하는 경우에는 기존의 손실함수에 파라미터의 절댓값이 더해지고, L2-Norm을 사용하면 파라미터의 제곱값이 더해집니다. 최종적으로 학습할 때는 더해진 손실함수를 사용해 미분을 진행합니다.

$$
\text{total cost} = \text{loss}(\mathcal{D}\,; \mathbf{W}) + \frac{\alpha}{2} \|\mathbf{W}\|_2^2
$$

<br>

## Augmentation

Data Augmentation은 데이터 증강이라고 하며 간단히 이해하면 데이터의 수를 늘리는 것으로 이해할 수 있습니다. 주어진 데이터를 변형하여 데이터를 늘리는 기법이며 라벨이 변하지 않는 선에서 데이터에 변화를 주는 것이 여기에 해당합니다.

대표적으로 어떤 이미지가 주어졌다면, 해당 이미지를 뒤집거나, 회전시키고, 좌우 반전시키는 행위가 해당합니다. 고양이 사진이 있다고 하면 좌우 반전시켜도 고양이 사진이기 때문입니다.

![image](https://user-images.githubusercontent.com/91870042/180942522-75c74ade-db56-4ae9-a68f-38ea2d898ca0.png){: .align-center width="50%"}

딥러닝은 머신러닝과 다르게 데이터가 많으면 많을 수록 그 성능이 증가하기 때문에 데이터의 증강은 매우 중요한 요소입니다. 저도 실제로 여러 이미지 분류대회에서 데이터의 증강으로 큰 성능향상을 경험했던 경험이 있습니다.

<br>

## Noise Robustness

입력으로 들어오는 데이터 또는 가중치에 noise를 추가해서 일부러 학습이 어렵게 되도록 만드는 것입니다. 덕분에 더욱 강건한 모델을 만들 수 있고, 여러 입력 셋에 대해서도 모두 일반화된 출력을 계산할 수 있습니다. 아래는 MNIST 손글씨 사진에 노이즈를 추가한 이미지 입니다.

![image](https://user-images.githubusercontent.com/91870042/180946175-2b1ce775-31a9-4ad4-b10c-ce8bf6721d21.png){: .align-center width="70%"}

<br>

## Label Smoothing

label smoothing은 데이터 2개를 섞어서 2개의 라벨 값을 모두 갖거나 1개의 라벨값을 갖는 데이터를 말합니다. 대표적으로 Mixup, Cutout, Cutmix 등이 있으며 특히 CutMix는 네이버 Clova에서 발표한 기법으로 학습효과가 뛰어났습니다.

![image](https://user-images.githubusercontent.com/91870042/180946668-5b244444-a51a-4045-b281-f404940470e4.png){: .align-center width="70%"}

Label Smoothing은 실제로 학습 성능을 올리는데 크게 기여하며 실제 대회에 적용시켜보았을 때 큰 성능향상을 경험했었습니다/

<br>

## Dropout

신경망 모델은 여러 레이어들 사이에 네트워크가 연결되어 있는 형태를 가지고 있습니다. 모델이 복잡하면 복잡할 수록 뉴런의 수가 많아짐을 의미하고 과적합이 될 위험이 있습니다. 따라서 모델의 복잡도를 줄이기 위해 Dropout 기법을 사용해볼 수 있습니다.

![image](https://user-images.githubusercontent.com/91870042/180947164-61251d27-1381-442b-9f82-d30e6b3df2a1.png){: .align-center width="50%"}

Dropout은 모델에 존재하는 몇개의 뉴런에 신호를 전달하지 않고 학습하는 것을 말합니다. 예를 들어, Dropout 비율이 50%로 설정되었다면, 모델에 존재하는 뉴런을 50%확률로 사용하지 않는 것을 말합니다. 덕분에 각 뉴런이 더 강건한 입력의 특징들을 추출하여 학습하는 것이 가능합니다.

<br>

## Batch Normalization

배치 정규화는 학습의 효율을 높이기 위해서 도입된 개념입니다. 배치 정규화는 각 레이어의 출력값을 정규화 해주는데, 다음 레이어에 대한 입력분포가 일정해지면서 학습률을 크게 해도 효과적인 학습이 이루어지도록 도와줍니다.

주 연산은 해당 레이어에 속한 파라미터를 레이어의 파라미터들의 평균값과 분산을 이용하여 정규화를 시켜주는 것입니다. 정확한 수식은 아래와 같습니다.

$$
\begin{aligned}
\mu_B &= \frac{1}{m}\sum_{i=1}^{m}x_i\\
\sigma_B^2 &= \frac{1}{m} \sum_{i=1}^{m} (x_i - \mu_B)^2\\
\hat{x}_i &= \frac{x_i - \mu_B}{\sqrt{\sigma_B^2 + \epsilon}}
\end{aligned}
$$

주요 장점을 정리하자면, 학습속도가 개선되며 기울기 소실문제를 해결할 수 있습니다. 또한 과적합에 대한 위험을 줄여서 일반화 성능을 높이는 것도 가능합니다. 기회가 된다면 더 자세한 내용은 논문 리뷰를 통해서 소개해드리도록 하겠습니다.

<br>

# References

[🌏 자습해도 모르겠던 딥러닝, 머리속에 인스톨 시켜드립니다.](https://www.slideshare.net/yongho/ss-79607172/)

[🌏 [딥러닝] 옵티마이저(Optimizer)](https://velog.io/@freesky/Optimizer#optimizer-%EC%A2%85%EB%A5%98)

[🌏 딥러닝 튜토리얼 6-1강. SGD, 모멘텀, AdaGrad, Adam](https://koreanfoodie.me/178)

[🎨 Cover Image Source : Unsplash](https://unsplash.com/photos/L3d2KZJoiKk)


<!--

<br>

# References

[📘 [Deep Learning] 배치 정규화](https://eehoeskrap.tistory.com/430)

[📘 인공신경망 - Parameter Norm Penalies](https://munjeongkang.github.io/ANN4/)

[📘 신경망에서 과적합을 방지하기 위한 방법](https://velog.io/@yuns_u/%EA%B3%BC%EC%A0%81%ED%95%A9%EC%9D%84-%EB%B0%A9%EC%A7%80%ED%95%98%EA%B8%B0-%EC%9C%84%ED%95%9C-%EB%B0%A9%EB%B2%95Regularization-Strategies)

[📘 딥러닝을 위한 경사하강법 비교](https://www.koreascience.or.kr/article/JAKO202013261023095.pdf)

-->