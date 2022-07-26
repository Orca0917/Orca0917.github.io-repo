---
title:  "[Deep Learning Basic] Modern Convolutional Neural Networks"
excerpt: "CNN의 발전과정"

categories:
  - DLBasic
tags:
  - [AI, Naver, BoostCamp, Python, Math]
toc: true
toc_sticky: true

date: 2022-02-12 01:00:00
last_modified_at: 2022-02-08 01:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# ILSVRC

ILSVRC(ImageNet Large Scale Visual Recognition Challenge)는 정말 많은 양의 이미지 데이터를 누가 더 잘 분류하는지 경쟁하는 대회이다. 이미지 데이터에는 일반적으로 1000개의 분류가 존재한다. 대회에서는 분류 뿐만아니라, Detection, Localization, Segmentation에 대해서도 다룬다.

<br>

# AlexNet
AlexNet은 CNN을 사용하여 총 8개의 layer를 가지고 2012년도에 우승한 모델이다.

![image](https://user-images.githubusercontent.com/91870042/152987191-f53436a5-7172-4395-bccf-9c77b5e4e9cc.png){: .align-center width="70%"}

위의 사진에서 입력 이미지에 대해서 Convolution 연산을 2번 수행했는데, 이는 당시 GPU의 메모리 한계로 인해서 GPU 2개를 사용했기 때문이다.

AlexNet이 성공한 주요한 이유

1. Rectified Linear Unit (ReLU) 활성화 함수의 사용  
    ReLU 활성함수의 사용으로, 선형모델의 장점인 기울기 소실문제를 해결할 수 있었고, 경사하강법을 이용해서 최적화하기가 쉬웠다.
2. 2개의 GPU 사용 (병렬화)  
3. Local response normalization, Overlapping Pooling  
   Convolution 연산을 진행할 때, 이전 커널과 겹쳐서 연산이 진행되게 만들었다.
4. Data Augmentation
5. Dropout

<br>

# VGGNet

VGGNet은 3x3 크기의 stride가 1인 Kernel을 사용해서 깊게 쌓은 모델이다. Fully Connected layer에서는 1x1 convolution 연산을 한다. AlexNet과 마찬가지로 Dropout을 적용하였다. 추가로, VGGNet은 쌓은 layer의 개수에 따라서 VGG16, VGG19로 구분이 된다.

VGGNet에서 3x3 크기의 커널을 사용해서 convolution 연산을 여러번 하는 이유에 대해서 알아보자.

![image](https://user-images.githubusercontent.com/91870042/152987686-5cb7aea9-90a4-4f20-8d33-f18d405dc741.png){: .align-center width="70%"}

VGGNet은 stride가 1인 3x3 커널을 사용하기 때문에 이를 연속해서 2번 적용하면, 5x5커널을 적용한 것과 동일한 크기의 결과를 낸다. 따라서 3x3커널을 사용해 더 깊게 만들어서 사용하는 것은, 전체 모델에서 사용되는 parameter의 수를 줄이기 위함이다.

<br>

# GoogLeNet

GoogLeNet은 ILSVRC에서 2014년에 우승한 모델이다.

![image](https://user-images.githubusercontent.com/91870042/152987844-3ce6e3cc-be10-43e4-b678-4083a85bcd34.png){: .align-center width="70%"}

위의 모델을 보면, 일부 layer가 비슷한 구조를 가지고 있고, 이 비슷한 구조가 여러번 반복되는 것을 확인할 수 있다. 이것을 Network In Network(NIN)이라고 부른다. GoogLeNet은 이와 함께, inception block을 사용했다.

## Inception Block

![image](https://user-images.githubusercontent.com/91870042/152988173-1e71ca75-8601-4602-bc9d-611344a4a549.png){: .align-center width="70%"}

Inception Block은 input 이미지로 부터, feature을 효과적으로 추출하기 위해서 다양한 Conovolution 연산을 수행한다. 여기에는 3x3 Max Pooling 과정도 포함되어 있는데, 이 때는 입력과 출력의 너비와 높이가 같아야 하기 때문에 Padding을 추가한다.

여기서 다시 연산과정을 줄이기 위해서 1x1 Convolution을 사용해서 필요한 parameter의 수를 감소시켰다.

![image](https://user-images.githubusercontent.com/91870042/152988578-a077c515-e6d6-4172-a5cb-0bcb6bec2ddb.png){: .align-center width="70%"}

<br>

# ResNet

딥러닝에 깊은 신경망을 사용할 수록, 해당 모델을 훈련시키기 어려운 문제가 생겼다. 학습이 잘 안되는 것을 overfitting으로 문제삼을 수도 없던것이, 훈련데이터에서도 테스트 데이터와 동일하게 학습이 잘 안되고 있었기 때문이다. 이 문제의 원인은 overfitting이 아닌 layer가 깊어짐에 따라서 생기는 기울기 소실문제 (Gradient vanishing, exploding problem)이었다.

![image](https://user-images.githubusercontent.com/91870042/152989021-b21c3f5c-f0af-40d6-b646-343af1913e67.png){: .align-center width="70%"}

ResNet은 이 문제를 skip connection 과정을 통해서 해결하고자 했다. 다음은 skip connection에 대해 표현하는 사진이다.

![image](https://user-images.githubusercontent.com/91870042/152989171-d64fbe1e-bee0-437a-b952-798e60af5c39.png){: .align-center width="70%"}

왼쪽에 있는 것이 일반적으로 알고있는 딥러닝의 구조이다. 처음 입력데이터에 대해서 가중치 레이어를 거쳐 비선형함수인 활성함수를 거치고 다시 가중치레이어에 들어가고 활성함수를 거치는 과정을 반복한다. ResNet은 기울기 소실의 문제를 해결하기 위해서 처음의 데이터를 활성함수에 더해서 같이 전달하는 방식을 사용했다.

위의 방법이 기울기 소실문제를 해결하는 것은 다음과 같다. 어떠한 입력에 대해 일반적인 딥러닝 과정을 거쳐서 나온 결과가 $$f(x)$$ 라고 했을 때, 그 둘을 더하면, $$f(x)+x$$가 된다. 이를 미분하면, $$f'(x)+1$$이 나오는데, 1이라는 값 덕분에 기울기 소실 문제를 해결시킬 수가 있다. 그 결과로, 더 많은 레이어가 쌓일 수록 오차를 줄일 수 있었다.

![image](https://user-images.githubusercontent.com/91870042/152992216-55331a53-328a-4f69-8347-4b125a7bd79c.png){: .align-center width="70%"}

ResNet은 여기에서 멈추지 않고, convolution 연산에 사용되는 parameter수를 줄이기 위해서 1x1 convolution 연산을 연산 앞뒤로 추가하는 bottleneck 구조를 사용하였다.

![image](https://user-images.githubusercontent.com/91870042/152992468-76c5af8c-6e1c-4006-b910-55378e5da90b.png){: .align-center width="70%"}

<br>

# DenseNet

DenseNet은 ResNet에서 사용되는 성분합 연산 대신에, concat연산을 한다. 문제는 계속 concat연산을 하게 되면, 채널이 점점 커지게 된다. 채널이 점점 커지면, 연산량도 동시에 늘어난다.

![image](https://user-images.githubusercontent.com/91870042/152992639-6a0ddc90-1af0-4fe1-8746-3d951c543b65.png){: .align-center width="70%"}

점점 늘어나는 연산량을 감소시키는 과정을 중간중간 포함해야하는데 이 과정을 수행하는 부분을 Transition Block이라고 한다. 이 Block에 대해서 조금 더 자세히 들여다보면 3가지 부분으로 이루어져 있다.

- Batch Normalization
- 1x1 Convolution
- 2x2 Average Pooling

이 Transition Block을 거치면 다시 parameter의 수가 줄어드는데, 다시 Dense Block을 거쳐서 연산하고 줄이고 반복한다.

![image](https://user-images.githubusercontent.com/91870042/152993041-46e2c2fc-f185-4d2e-b722-18cae3634c5f.png){: .align-center}
