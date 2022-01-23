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

![image](https://user-images.githubusercontent.com/91870042/150675796-8c1d48cd-b956-4ec4-a935-949086bc7d9f.png){: .align-center}

$$ h_i = \sigma \left ( \sum_{j=1}^{p}W_{ij}x_j \right ) $$

지금까지 배우 다층신경망(MLP)은 각 뉴런들이 선형모델과 활성함수로 모두 연결된 구조였다. 위의 그림에서 확인할 수 있듯이, **각 성분 \\(h_i\\)에 대응하는 가중치 행 \\(W_i\\)가 필요**하였다. 그렇기 때문에 계산하는 i가 바뀌면 사용되는 가중치(W값)도 바뀌게 되어서 다른 결과값이 도출되었다. 하지만 `Convolution연산`은 이와 달리 **커널을 입력벡터 상에서 움직여가면서** 선형모델과 합성함수가 적용되는 구조이다.

## Convolution 연산
![image](https://user-images.githubusercontent.com/91870042/150675808-a4bf14bf-7916-4da4-beb0-a8f69e029578.png){: .align-center}

$$ h_i = \sigma \left ( \sum_{j=1}^{p}V_jx_{i+j-1} \right ) $$

Convolution연산은 모든 \\(i\\)에 대해 적용되는 커널이 \\(V\\)로 같고, 커널의 사이즈만큼 \\(X\\)상에서 이동하면서 적용된다.

다층신경망과 비슷하게 커널을 사용하는 연산도 선형변환의 한 종류이지만, **가중치 행렬(커널)이 입력벡터상에서 움직여 가면서 한다**는 것이 차이점이다. 덕분에 가중치 행렬의 크기가 대폭 줄어 parameter의 크기를 많이 줄일 수 있게 되었다.

## 수학적 의미
`convolution 연산`의 수학적인 의미는 `신호`를 `커널`을 이용해 **국소적으로 증폭 또는 감소시켜서 정보를 추출 또는 필터링하는 것**이다. 아래 사진은 수식을 나타낸 것으로, 커널은 \\(f\\)로, 신호는 \\(g\\)로 표현되어 있다. 하지만 수식만으로 해석하는 것은 매우 어렵다.

- 연속형  

  $$ [f * g](x)=\int_{\mathbb{R}^d}f(z)g(x-z)dz = \int_{\mathbb{R}^d}f(x - z)g(z)dz = [g*f]*(x) $$

- 이산형  

  $$ [f * g](i)=\sum_{a\in \mathbb{Z}^d}f(a)g(i-a) = \sum_{a\in \mathbb{Z}^d}f(i-a)g(a) = [g*f](i) $$

사실 CNN에서 사용하는 수식은 다음과 같다. (**Cross-correlation 연산**)

- 연속형  

  $$ [f * g](x)=\int_{\mathbb{R}^d}f(z)g(x+z)dz = \int_{\mathbb{R}^d}f(x + z)g(z)dz = [g*f]*(x) $$

- 이산형  

  $$ [f * g](i)=\sum_{a\in \mathbb{Z}^d}f(a)g(i+a) = \sum_{a\in \mathbb{Z}^d}f(i+a)g(a) = [g*f](i) $$

CNN에서 사용하는 연산은 사실 `-`연산이 아니라, `+` 연산을 사용하기 때문에 convolution연산이 아니고, `cross-correlation연산` 이라고 부른다. 하지만 관용적으로 convolution연산이라고 부른다.

### 그림을 통한 Convolution연산의 이해
커널은 정의역 내에서 움직여도 `변하지 않고(translation invariant)` 주어진 신호에 `국소적(local)`으로 적용한다.

> 파란색: 신호, 빨간색: 커널, 검은색: 결과

![Convolution_of_box_signal_with_itself2](https://user-images.githubusercontent.com/91870042/145153661-9317211c-8e9c-4d6d-acd6-7f5dbb940c12.gif){: .align-center}

![Convolution_of_spiky_function_with_box2](https://user-images.githubusercontent.com/91870042/145153690-5e436849-5ef5-4d0e-9335-1aad0e7436b9.gif){: .align-center}

GIF출처: [https://en.wikipedia.org/wiki/Convolution](https://en.wikipedia.org/wiki/Convolution)

위의 영상에서 알 수 있듯이, 정의역은 가만히 있지만, 빨간색 커널이 움직여가면서 연산을 수행하는 것을 볼 수 있다. 검정색선은 정의역에서 커널이 얼만큼 차지하는지를 나타내면서 그려진다. 노란색 부분은 국소적으로 적용된 연산을 말한다.

## 영상처리에서 convolution을 사용하는 방법
하나의 이미지에서 커널값을 조절해가면서 이미지를 변경하는 것이 가능하며 [여기](https://setosa.io/ev/image-kernels/)에서 직접해볼 수 있다. 커널값의 설정에 따라서 이미지의 noise를 제거할 수도 있고, 다양한 효과를 주는 것도 가능하다.

<br>

# 다양한 차원에서의 convolution
convolution연산은 1차원 뿐만아니라 다양한 차원에서 계산이 가능하다. 아래 수식에서 입력받는 인자의 수와 \\(i, j, k\\)의 값이 바뀌어도 커널 \\(f\\)의 값은 위치에 따라서 <u>변경되지 않는 것</u>을 확인할 수 있다.

## 1D-convolution

$$ [f*g](i)=\sum_{p=1}^{d}f(p)g(i+p) $$

## 2D-convolution

$$ [f*g](i, j)=\sum_{p,q}f(p,q)g(i+p, j+q) $$

## 3D-convolution

$$ [f*g](i, j, k)=\sum_{p,q, r}f(p,q, r)g(i+p, j+q, k+r) $$

<br>

# 2차원 Convolution 연산
2차원 Convolution연산은 커널을 입력벡터 상에서 움직여가면서 선형모델과 합성함수가 적용되는 구조이다. 이때 두 행렬간의 곱셈연산은 `행렬의 성분곱`을 수행한다. 행렬의 성분곱은 두 행렬의 동일한 위치에 있는 값끼리의 곱셈을 말한다.

![image](https://miro.medium.com/max/700/0*r-ArDpy2MwDyaodl){: .align-center}

위의 진행과정을 보면, 각 input 행렬에 대해서 임의의 커널과 성분곱 연산을 진행하고 원소들의 합을 결과로 저장하는 것을 확인할 수 있다.

Convolution 연산의 출력크기
---

위의 연산을 통해서 얻어낸 출력이 있을 때, 입력크기를 \\((H, W)\\), 커널 크기를 \\((K_H, K_W)\\), 출력크기를 \\((O_H, O_W)\\)라 하면, 출력의 크기는 다음과 같이 연산할 수 있다. 

$$ O_H = H - K_H + 1 \qquad O_W = W - K_W + 1 $$

채널이 여러개인 2차원 입력의 경우, 2차원 Convolution을 `채널 개수만큼 적용`한다고 생각하면 된다. 참고로, 3차원부터는 행렬이 아닌 텐서라고 부른다. 또한, 채널이 여러개인 경우 커널의 채널수와 입력의 채널수가 동일해야한다.

✔ CNN에서의 채널<br>
어떠한 사진에서 픽셀 하나하나는 RGB의 색으로 표현할 수 있다. 이 때 각각의 색상에 대해서 하나의 채널로 이해할 수 있고, 흔히 컬러사진은 3개의 채널로 구성되어 있다고 생각할 수 있다.
{: .notice--success}

![image](https://user-images.githubusercontent.com/91870042/145154774-2af0d34f-47fa-4f84-93f9-af7991d369ad.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/145155068-3d4514c5-1270-418a-8214-dba6bf6f0d4b.png){: .align-center}

위의 연산을 2개의 직육면체 `텐서`간의 곱으로 생각할 수 있다. 텐서는 채널이 여러개인 커널로 생각해보는 것이 가능하고, 모두 각 채널별로 연산을 진행하고 각 원소에 맞는 위치의 총합으로 결과가 나오기 때문에 z축의 크기가 1인 출력이 나온다.

만약, 텐서형태의 출력을 원한다면, 텐서형태의 커널의 수를 그 개수만큼 가지고 있으면 된다.

![image](https://user-images.githubusercontent.com/91870042/150676823-d0e2d066-a59e-4917-b0b3-59370188d24c.png){: .align-center}

<br>

# Convolution 연산의 역전파

Convolution연산은 **커널이 모든 입력 데이터에 공통으로 적용**되기 때문에 역전파를 계산할 때도 convolution연산이 나오게 된다. 이 때 눈여겨 볼 수 있는 점은, convolution연산의 미분값이 다시 convolution연산이 나오는 것을 볼 수 있다.

\begin{aligned}
  \frac{\partial}{\partial x}\[f\*g\](x) &= \frac{\partial}{\partial x} \int_{\mathbb{R}^d} f(y)g(x-y)dy \\\\\\
  &= \int_{\mathbb{R}^d} f(y) \frac{\partial g}{\partial x}(x-y)dy \\\\\\
  &= \[f\*g'\](x) 
\end{aligned}

위에서 말한 역전파에 대해서 설명하기 전에, 먼저 convolution 연산이 실제로 어떻게 수행되어서 결과가 나오는지 다시 한 번 그림으로 살펴보자

![image](https://user-images.githubusercontent.com/91870042/150677239-6f48fedf-0364-45d6-ad31-9beeba155b18.png){: .align-center}

해당 과정을 수식으로 표현하면 가장 처음에 살펴본 수식과 동일하게 나온다.

$$ O_i = \sum_{j}W_jx_{i+j-1} $$

이때 출력벡터 \\(O\\) 에 대해서 손실함수로 부터 오차를 계산하고 출력벡터 원소 각각에 대한 오차를 \\( \delta_1, \delta_2, \delta_3 \\) 라고 하면, 이 오차를 각각에 대한 출력벡터에 역전파를 진행시킨다. 

먼저, **입력에 오차를 전달하는 방법**은 각각의 출력벡터로 전달된 오차와 커널의 가중치가 곱해져서 전달이 된다. 아래 사진에서 \\(O_1\\) 의 경우 `순전파`가 일어날때, 입력인 \\(x_3\\)와 커널의 가중치 \\(W_3\\) 가 곱해져서 연산이 일어났기 때문에 `역전파`가 일어날 때도 오차에 \\(W_3\\) 가 곱해져서 전달이 된다. 이런 과정을 그림과 수식으로 나타내면 아래와 같다.

![image](https://user-images.githubusercontent.com/91870042/150677770-58b4275f-de0c-431a-ba85-f1a1e5516d6b.png){: .align-center}

이번에는 **커널에 오차가 전달되는 방법**에 대해서 살펴보자. 이번에는 각각의 오차가 입력인 \\(x_3\\) 와의 연산을 통해서 일어났기 때문에, 예시로, 가중치 \\(x_3\\) 로 부터 \\(W_1\\) 에 전달되는 오차는 \\(\delta_3x_3\\) 이다. 전체적으로 모든 입력에 대해서 각 커널에 전달되는 오차는 각 입력에 대해서 전달된 오차의 총합으로 다음과 같이 계산할 수 있다.

$$ \frac{\partial L}{\partial W_i}=\sum_{j}\delta_j x_{i+j-1} $$

예를 들어, 커널의 가중치 \\(W_1\\)에 전달되는 오차는 입력벡터에서 \\(x_1, x_2, x_3\\)에 의해 전달받아 \\(\delta_1 x_1+\delta_2 x_2+\delta_3 x_3 \\) 가 된다. 이는 결국 역전파에서 오차에 대한 `convolution`연산이 다시 일어남을 알 수 있다.

<br>

# References

[📘 CNN, Convolutional Nerual Network 요약](http://taewan.kim/post/cnn/)

[📘 Wikipedia: Convolution](https://en.wikipedia.org/wiki/Convolution)

[📘 CNN의 역전파(backpropagation)](https://ratsgo.github.io/deep%20learning/2017/04/05/CNNbackprop/)