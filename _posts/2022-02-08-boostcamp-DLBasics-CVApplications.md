---
title:  "[Deep Learning Basic] Computer Vision Applications"
excerpt: "Computer Vision 분야에서 사용되는 Convolution Nerual Network"

categories:
  - DLBasic
tags:
  - [AI, Naver, BoostCamp, Python, Math]
toc: true
toc_sticky: true

date: 2022-02-08 02:00:00
last_modified_at: 2022-02-08 02:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Semantic Segmentation
- 이미지의 각 필셀이 어떤 Class에 속하는지 알아내는 문제
- 다른 말로 Dense Classification 또는 Per Pixel Calssification 이라고도 한다

![image](https://user-images.githubusercontent.com/91870042/153175969-a79f4d9e-5c58-4850-a394-6e08e6878d3b.png){: .align-center width="70%"}

<br>

# FCN: Fully Convolution Network

![image](https://user-images.githubusercontent.com/91870042/153178128-f4c759fc-e493-4927-abe4-7e7eb927e1fb.png){: .align-center width="70%"}


- 일반 CNN에서 있던 Flat layer, Dense layer를 없애고 Convolution Layer로 바꾼 것(Convolutionalize)
- input으로 이미지가 들어와서 label된 output이 나올 때 까지 모두 convolution layer로만 구성

Convolutionalize과정을 거쳐도 output의 크기나 사용되는 파라미터의 크기는 변함이 없다. 그럼에도 사용하는 이유는 다음 2가지가 있다.
  1. 출력을 Heatmap으로 나타낼 수 있다.
  2. 입력을 어떠한 크기로도 넣을 수 있다.

Convolution 연산을 많이 거치면 output의 차원이 너무 축소되는 단점이 있다. 우리가 하고 싶은 것은 이미지의 픽셀별 어떤 Class에 속하는지 찾아내고 싶은데 차원이 축소되어 픽셀 자체도 너무 뭉개져 버리는 현상이 있다. 이를 해결하기 위한 방법을 아래에서 다시 설명한다.

<br>

# Deconvolution (Convolution Transpose)
- Deconvolution, 말그대로 convolution 연산을 역으로 진행시키겠다는 것

![image](https://user-images.githubusercontent.com/91870042/153178413-c9e85572-22a9-40c6-88f7-297af98882ea.png){: .align-center width="70%"}

하지만, convolution 연산의 결과를 다시 역으로 돌려서 원래의 input을 얻는다는 것은 말도 안된다. 예를 들어서, 어떤 커널에 대해서 성분곱을 하고 모두 더해서 값을 결과로 냈다고 해보자. 그 값이 3+5=8이라고 한다면 이 8이라는 값만 가지고 2+6 으로부터 온것인지, 4+4로부터 온것인지 알 수 있는 길이 없다.

사실, Deconvolution 연산이라는 것은 Convolution과는 전혀 다른 연산이다. Transposed Convolution 연산이 더 맞는 말이다. 아래는 convolution 연산을 통해서 얻어낸 3x3 output을 5x5로 되돌리는 과정이다.

![gif](https://i.stack.imgur.com/f2RiP.gif){: .align-center width="70%"}

- 3x3 image를 Convolution 연산을 수행할 수 있도록 Upsampling 과정을 진행한다. 일반적으로 이때, 빈 공간에 대해서는 0으로 값을 채운다.
- 그렇게 7x7 이미지로 만들고, 3x3 filter를 stride=1로 움직여 5x5의 이미지를 얻는다.

이 연산을 이용해서 FCN의 차원 축소 문제를 해결한다. 하지만 매우 정확하지는 않기 때문에 성능이 그렇게 뛰어난 편은 아니다.

<br>

# Detection
- 원본 이미지로 부터 추출한 이미지가 어떤 label을 갖는지 맞추는 문제
- 이를 해결하기 위한 가장 1차적인 생각으로 여러개의 이미지를 임의로 추출하고 학습시키는 것이다.

<br>

# R-CNN
- R-CNN은 위에서 말한 가장 1차적인 방법으로 문제를 해결하고자 했다.

![image](https://user-images.githubusercontent.com/91870042/153179882-631fa536-e060-4e25-b883-92b6c1a91016.png){: .align-center width="70%"}

1. 이미지를 입력으로 받는다.
2. 입력받은 이미지로 부터 약 2000개 정도의 Region Proposasls 를 뽑아낸다.
3. 위에서 뽑아낸 2000개의 이미지 각각을 AlexNet에 통과 시킨다.
4. 선형 Support Vector Machine을 사용해서 이미지를 분류한다.

✔ Support Vector Machine(SVM)  
SVM은 기계학습의 분야 중 하나로서 패턴 인식, 자료 분석을 위한 지도학습 모델이며 주로 분류와 회귀 분석을 위해서 사용한다. - Wikipedia
{: .notice--info}

<br>

# SPPNet
SPPNet(Spatial Pyramid Pooling Network)은 기존의 CNN구조가 입력 이미지가 고정된 것에 의문을 가져 나온 모델이다. 이전까지 CNN을 통과시키기 위해서 이미지를 고정된 크기로 자르거나, 비율을 늘리거나 줄여서 학습시켰다. 이렇게 되면 detection하는데 있어서 본래의 이미지와 달라지는 문제점이 생긴다.

사실 Convolution Layer는 sliding window를 기반으로 동작하기 때문에, 이미지의 크기에 상관없이 돌아간다. 이미지의 고정이 필요한 것은 fully connected layer 가 고정된 크기의 입력을 받기 때문이다.

그래서 나온 생각이 Fully Connected Layer를 통과하기 전에, 피처맵들을 동일한 크기로 조절해주는 pooling을 하면 된다는 것이다.

![image](https://user-images.githubusercontent.com/91870042/153182964-b89c7b02-2705-4709-a6f6-5d6b299b2890.png){: .align-center width="70%"}

1. 전체 이미지를 CNN에 통과시켜서 feature map(output)을 얻어낸다.
2. Selective Search를 통해서 찾는 RoI(Region of Interests, 있음직한 패치)들은 각각 그 크기와 비율이 모두 다르다. 여기에 SPP를 적용시켜 고정된 크기의 feature map을 얻는다.
3. SPP를 적용한 feature map을 Fully Connected Layer에 통과시킨다.

<br>

# Fast R-CNN
![image](https://user-images.githubusercontent.com/91870042/153183201-ce33b9c3-a623-4905-9337-a5bd21f08dfb.png){: .align-center width="70%"}

1. 전체이미지를 CNN에 통과시켜 feature map을 추출한다.
2. Selective Search를 통해 찾은 각각의 RoI에 대해서 RoI Pooling 진행
→ 고정된 크기의 feature vector 얻기
3. feature vector는 fully connected layer를 통과시키고 2개의 branch로 나뉜다.
4. softmax를 통과해 해당 RoI가 어떤 물체인지 분류하기 (SVM 사용x)
5. bounding box regression을 통해 selective search로 찾은 박스의 위치 조정하기

<br>

# Faster R-CNN

- 기존의 Fast R-CNN의 구조를 그대로 가져오되, Selective Search를 제거하고 Region Proposal Network를 이용해서 RoI를 계산
- 이에 따라, GPU를 통한 RoI의 연산이 가능해졌고, 학습을 통해 정확도를 높일 수 있었다.

![image](https://user-images.githubusercontent.com/91870042/153183913-157f71cd-3342-44ab-9e3e-0c26a3febd7c.png){: .align-center width="70%"}

1. 이미지를 Convolution Layer에 통과시켜서 Feature Map을 추출한다.
2. 이 Feature Map을 Region Proposal Network에 전달해서 RoI를 얻어낸다.
3. RoI Pooling 진행 후, 분류를 통해서 Object Dectection을 수행한다.

<br>

# YOLO

- YOLO(v1)은 정말 빠르게 object detection을 하는 알고리즘이다.
  - baseline: 45fps, smaller bersion: 155fps
- Faster R-CNN과 다르게, Bounding Box와 Class의 분류를 동시에 진행한다.

![image](https://user-images.githubusercontent.com/91870042/153185406-eeb960a2-76cf-4bd9-810b-54112beb836a.png){: .align-center width="70%"}

1. 이미지가 들어오면, SxS의 그리드로 나눈다.
    - 찾고 싶은 물체의 중앙이 어떤 하나의 셀에 포함되어 있다면, 그 셀은 물체의 bounding box와 어떤 물체인지 같이 예측해야 한다.
2. 각각의 셀은 B개의 bounding box를 찾는다. 그리고 그 값이 쓸모 있는지 알아낸다.
3. 동시에, 그 물체가 어떤 Class에 속하는지 알아낸다.

<br>

# References 

[📘 갈아먹는 Object Detection](https://yeomko.tistory.com/category/%EA%B0%88%EC%95%84%EB%A8%B9%EB%8A%94%20Object%20Detection)