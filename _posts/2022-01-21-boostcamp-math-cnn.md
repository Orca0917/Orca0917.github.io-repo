---
title:  "[AI Math] CNN"
excerpt: "Convolution 연산과 다양한 차원에서의 연산방법과 역전파"

categories:
  - boostcamp
tags:
  - [AI, Naver, BoostCamp, Python, Math]
toc: true
toc_sticky: true
 
date: 2022-01-20 03:00:00
last_modified_at: 2022-01-20 03:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Convolution 연산

## 도입
![image](https://user-images.githubusercontent.com/91870042/145152531-68e147f2-0c7e-4f3e-908b-9e7e3d0c38ec.png){: .align-center}

지금까지 배우 다층신경망(MLP)은 각 뉴런들이 선형모델과 활성함수로 모두 연결된 구조였다. 위의 그림에서 확인할 수 있듯이, **각 성분 \\(h_i\\)에 대응하는 가중치 행 \\(W_i\\)가 필요**하였다. 그렇기 때문에 계산하는 i가 바뀌면 사용되는 가중치(W값)도 바뀌게 되어서 다른 결과값이 도출되었다. 하지만 `Convolution연산`은 이와 달리 **커널을 입력벡터 상에서 움직여가면서** 선형모델과 합성함수가 적용되는 구조이다.

## Convolution 연산
![image](https://user-images.githubusercontent.com/91870042/145152651-2c027ecb-ba49-4046-acc3-811b96742c9c.png){: .align-center}

Convolution연산은 모든 \\(i\\)에 대해 적용되는 커널이 \\(V\\)로 같고, 커널의 사이즈만큼 \\(X\\)상에서 이동하면서 적용된다.

다층신경망과 비슷하게 커널을 사용하는 연산도 선형변환의 한 종류이지만, **가중치 행렬(커널)이 입력벡터상에서 움직여 가면서 한다**는 것이 차이점이다. 덕분에 가중치 행렬의 크기가 대폭 줄어 parameter의 크기를 많이 줄일 수 있게 되었다.

## 수학적 의미
`convolution 연산`의 수학적인 의미는 `신호`를 `커널`을 이용해 **국소적으로 증폭 또는 감소시켜서 정보를 추출 또는 필터링하는 것**이다. 아래 사진은 수식을 나타낸 것으로, 커널은 \\(f\\)로, 신호는 \\(g\\)로 표현되어 있다. 하지만 수식만으로 해석하는 것은 매우 어렵다.

![image](https://user-images.githubusercontent.com/91870042/145152891-44e417b8-435a-4df1-8615-3505791e8b07.png){: .align-center}

CNN에서 사용하는 연산은 사실 convolution연산이 아니고, `cross-correlation연산` 이라고 부른다. 하지만 관용적으로 convolution연산이라고 부른다.

### 그림을 통한 Convolution연산의 이해
커널은 정의역 내에서 움직여도 `변하지 않고(translation invariant)` 주어진 신호에 `국소적(local)`으로 적용한다.

> 파란색: 신호, 빨간색: 커널, 검은색: 결과

![Convolution_of_box_signal_with_itself2](https://user-images.githubusercontent.com/91870042/145153661-9317211c-8e9c-4d6d-acd6-7f5dbb940c12.gif){: .align-center}

![Convolution_of_spiky_function_with_box2](https://user-images.githubusercontent.com/91870042/145153690-5e436849-5ef5-4d0e-9335-1aad0e7436b9.gif){: .align-center}

GIF출처: https://en.wikipedia.org/wiki/Convolution

위의 영상에서 알 수 있듯이, 정의역은 가만히 있지만, 빨간색 커널이 움직여가면서 연산을 수행하는 것을 볼 수 있다. 검정색선은 정의역에서 커널이 얼만큼 차지하는지를 나타내면서 그려진다.

## 영상처리에서 convolution을 사용하는 방법
하나의 이미지에서 커널값을 조절해가면서 이미지를 변경하는 것이 가능하다. 커널값의 설정에 따라서 이미지의 noise를 제거할 수도 있고, 다양한 효과를 주는 것도 가능하다.

<br>

# 다양한 차원에서의 convolution
convolution연산은 1차원 뿐만아니라 다양한 차원에서 계산이 가능하다. 아래 수식에서 입력받는 인자의 수와 \\(i, j, k\\)의 값이 바뀌어도 커널 \\(f\\)의 값은 변경되지 않는 것을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/91870042/145154007-70ac6cf2-bb79-496e-a28b-56b1975a6310.png){: .align-center}

## 2차원 Convolution 연산
2차원 Convolution연산은 커널을 입력벡터 상에서 움직여가면서 선형모델과 합성함수가 적용되는 구조이다. 이때 두 행렬간의 곱셈연산은 `행렬의 성분곱`을 수행한다. 행렬의 성분곱은 두 행렬의 동일한 위치에 있는 값끼리의 곱셈을 말한다.

![image](https://user-images.githubusercontent.com/91870042/145154326-7fcf378a-fb81-4935-8530-3b8ab402b023.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/145154504-00c387a4-12b1-4f34-9c2d-136108bc6ce4.png){: .align-center}

위의 연산을 통해서 얻어낸 출력이 있을 때, 입력크기를 \\((H, W)\\), 커널 크기를 \\((K_H, K_W)\\), 출력크기를 \\((O_H, O_W)\\)라 하면,출력의 크기는 다음과 같이 연산할 수 있다. 

- \\(O_H = H - K_H + 1\\)
- \\(O_W = W - K_W + 1\\)

채널이 여러개인 2차원 입력의 경우, 2차원 Convolution을 `채널 개수만큼 적용`다고 생각하면 된다. 참고로, 3차원부터는 행렬이 아닌 텐서라고 부른다. 또한, 채널이 여러개인 경우 커널의 채널수와 입력의 채널수가 동일해야한다.

![image](https://user-images.githubusercontent.com/91870042/145154774-2af0d34f-47fa-4f84-93f9-af7991d369ad.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/145155068-3d4514c5-1270-418a-8214-dba6bf6f0d4b.png){: .align-center}

위의 연산을 2개의 직육면체 텐서간의 곱으로 생각할 수 있다. 결과값은 각 채널에서 연산한 결과의 합이기 때문에 출력값은 높이가 1인 행렬로 표현이 된다. 출력도 텐서로 만들기 위해서는 커널의 수를 여러개로 만들면 된다. 

하나의 직육면체 커널당 출력은 2차원 행렬값이 나왔다면, 여러개의 커널을 가지고 곱셈을 하게 되면 출력도 텐서형태가 되는 것이다.

<br>

# Convolution 연산의 역전파
![image](https://user-images.githubusercontent.com/91870042/145159386-ee65ad15-b48f-4db8-b73a-520f27aaaa40.png){: .align-center}

Convolution연산은 **커널이 모든 입력 데이터에 공통으로 적용**되기 때문에 역전파를 계산할 때도 convolution연산이 나오게 된다. 이 과정은 위의 수식보다 아래의 첨부된 강의자료로 보면 훨씬 이해하기가 쉽다.

<!--
![image](https://user-images.githubusercontent.com/91870042/145159816-91f1a632-4fcb-4c1e-82fe-461e21be780f.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/145159709-bca6abd4-9163-4d73-a240-6a11c74d4214.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/145159837-99eb67a7-b513-4b26-b99b-6346b3ddfee4.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/145159861-720d0096-186c-4f3f-a0bc-31c69475796b.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/145159874-e9f66822-c551-4ff2-b595-23f2d3a59496.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/145159889-2d8b615b-949e-4224-9d48-68e6b1efdd41.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/145159917-bcf0803d-701a-4670-b089-801ee1889734.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/145159949-a2ed5d2c-a01a-4461-94a3-67400fdde6b8.png){: .align-center}

-->

<br>

# References

[📘 CNN, Convolutional Nerual Network 요약](http://taewan.kim/post/cnn/)

[📘 Wikipedia: Convolution](https://en.wikipedia.org/wiki/Convolution)