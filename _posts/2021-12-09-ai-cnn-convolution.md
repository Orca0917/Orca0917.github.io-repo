---
title:  "[부스트캠프 Pre-Course] 딥러닝 기초: CNN - Convolution"
excerpt: "CNN의 Convolution의 정의와 연산방법 그리고 기능"

categories:
  - boostcamp
tags:
  - [AI, Naver, BoostCamp, Python, Math]
toc: true
toc_sticky: true
 
date: 2021-12-08
last_modified_at: 2021-12-08
---

# <span style = "color: #00adb5">Convolution</span>
Convolution연산이라고 하면, 어떤 두 개의 함수 \\(f, g\\)에 대해서 잘 섞어주는 연산이라고 생각하면 쉽다. 그런 Convolution 연산은 예전부터 사용이 되어왔었고, CNN은 Convolution연산을 적용한 Nerual Network라고 이해하면 된다. 다양한 Convolution연산의 수식은 아래에서 확인할 수 있다.

- Continuous convoultion  
\begin{aligned}
    (f*g)(t)=\int f(\tau)g(t-\tau)d\tau = \int f(t-\tau)g(t)d\tau
\end{aligned}

- Discrete convolution  
\begin{aligned}
    (f*g)(t)= \sum_{i=-\infty}^{\infty}f(i)g(t-i)=\sum_{i=-\infty}^{\infty}f(t-i)g(i) \\\\\\
\end{aligned}

- 2D Image convolution  
\\(I\\): 전체이미지, \\(K\\): 적용하고자 하는 필터(커널)  
\begin{aligned}
    (I*K)(i, j) &= \sum_M\sum_n I(m, n)K(i-m, j-n) \\\\\\
    &= \sum_M\sum_n I(i-m, j-n)K(m, n)
\end{aligned}


## Convolution연산 과정
필터를 이미지위에 도장 찍듯이 하나씩 찍는 것을 말한다. 이후, 각 연산을 통해 나온 값들의 합이 출력의 한 셀로 저장된다.

![image](https://user-images.githubusercontent.com/91870042/145317033-bb0dff99-f641-40e9-b477-b8561ba3ade9.png){: .align-center}

## 2D Convolution
실제로, 이미지에 적용하고자 하는 필터(커널)이 어떤 것인지에 따라서 다양한 효과를 줄 수 있다. 그 중 대표적으로 `Blur`, `Emboss`, `Outline`의 효과를 줄 수 있다. 예를 들어서 어떤 이미지\\((I)\\)가 있고, 커널\\((K)\\)가 있다고 하자. 각 커널의 값에 1/9값이 들어가 있다면 출력되는 것은, 커널 크기만큼의 픽셀들의 평균일 것이다. 이렇게 되면, `Blur`효과를 부여할 수 있다.

## RGB Image Convoluiton
RGB이미지를 수학적으로 텐서로 표현할 수 있다. 필터 channel의 크기는 이미지의 Channel과 같기 때문에, 출력 channel은 1이 됨을 우리는 알 수 있다.이 출력 Channel을 크게하고 싶다면 여러개의 필터를 적용시켜서, 각 필터에 적용된 출력이 쌓이도록 만들어야 한다.

![image](https://user-images.githubusercontent.com/91870042/145317555-38c08e1e-dc59-4204-9cda-f0371c44fca5.png){: .align-center}

또한 우리는 Input Channel과 Output의 결과를 알면, 커널의 개수와 그 크기를 알 수 있다.

---

하지만 위와 같이 언제나 1번의 필터를 거치는 것은 아니다. 여러겹의 필터를 이용해서 여러번의 Convolution연산을 수행할 수 있다. 하지만 여기서 가장 중요하게 생각해야 하는것은 `parameter`의 수이다. 이 `parameter`의 수를 왜 줄여야 하는지는 아래 CNN에서 설명한다.

> parameter의 수 = 커널의 크기 X Input Channel 수 X Output Channel 수

![image](https://user-images.githubusercontent.com/91870042/145317773-c4ae21f8-1e42-42f6-b3f6-03ac1f1a650c.png){: .align-center}

<br>

# <span style = "color: #00adb5">CNN</span>
CNN의 연산과정을 보면, 그 안에는 다양한 레이어가 존재한다. 가장 먼저 `convolution layer`. convolution layer는 어떤 이미지에서 커널과의 convolution연산을 수행한 결과를 말한다. 그 이후 `pooling`이라는 과정을 거치는데, 이는 convolution layer에서 얻은 결과중 일부를 추출하는 과정이다. 여기서 선택할 수 있는데, 다시 `convolution`과 `pooling`을 통해 연산하는 방향이 있고, `Fully connected Layer`로 가는 방향이 있다.

`Fully Connected Layer`에서는 한 층의 모든 뉴런이 다음층의 모든 뉴런과 연결된 상태로, 2차원 배열의 이미지를 1차원의 평탄화 작업을 통해 이미지를 분류하는데 사용한다. 하지만 최근에는 parameter의 수를 줄이기 위해서 이 fully connected layer를 없애는 추세이다.

> 왜 parameter의 수를 줄여야 하나?

parameter의 수가 많아지면, generalization performance와 학습이 어려워진다는 단점이 있다. 다시 말해 아무리 학습을 잘 시켜도, 실제 데이터에 대해서는 효능이 나오지 않는다는 것이다. 결국에 우리가 원하는 것은 여러겹 Layer를 쌓되, Parameter의 수를 줄여가면서 쌓는것이다.

<br>

# <span style = "color: #00adb5">Convolution Arithmetic</span>

## Stride
영어사전에으로 해석하면 `Stride`는 보폭을 의미한다. 이 보폭은 커널을 움직이는 보폭을 말한다. 지금까지는 필터를 한 픽셀씩 움직이면서 움직였었다. 이를 `stride=1`인 경우를 의미한다. `stride=2`인 경우에는 다음 필터를 적용시키는 곳은, 2칸 옮긴 곳이다. 이를 1차원적으로 생각하면 다음과 같이 볼 수 있다.

![image](https://user-images.githubusercontent.com/91870042/145318454-b9c10eb0-b019-46bf-8019-8ed305a47c90.png){: .align-center}


## Padding
아무리 여러 Layer를 쌓는다고 하더라도, 여러번의 Convolution 연산을 거치면, 그 이미지 크기는 계속하여 줄어드는 성질이 있었다. 이러한 현상을 막기 위해서 `Padding`이라는 개념이 도입되었다.

`Padding`은 이미지 가장자리에 특정값으로 둘러싸는 것을 말한다. 일반적으로 zero padding은 주위에 0을 둘러 싸는것을 말한다.

![image](https://user-images.githubusercontent.com/91870042/145318602-243f0c2b-4f2c-42d7-b0cb-acc0c2f170cb.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/145318696-128d6308-26b7-45df-9638-2a0bed76b362.png){: .align-center}

## Convolution Arithmetic - Parameter
이제는 `parameter`의 수를 계산해볼 것이다.

![image](https://user-images.githubusercontent.com/91870042/145318825-3c1b24ce-fdba-4b4f-9b72-9d6445e2d3a4.png){: .align-center}

위의 그림에서 `padding`은 1이고, `stride`는 1이라고 했을 때, `parameter`의 수는 다음과 같이 계산할 수 있다. 먼저, \\(W_K\\) 는 커널의 너비, \\(H_K\\)는 커널의 높이, \\(C_I\\)는 입력 데이터의 채널 크기, \\(C_O\\)는 출력데이터 커널의 크기를 의미한다.

\begin{aligned}
    parameter = W_K \times H_K \times C_I \times C_O  
\end{aligned}

위의 수식을 이용해서 다음 모델의 parameter수를 계산해 볼 수 있다.

![image](https://user-images.githubusercontent.com/91870042/145319147-17fe4390-02e0-4b9d-bcbb-a715094fc800.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/145319203-192d476f-eb67-44c9-b875-af5be138d742.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/145319290-91de6ee1-038d-4cc4-ba6e-7d09b36b06d8.png){: .align-center}

## 1x1 Convolution
지금까지 알아본 `parameter`의 수를 줄이기 위해서 1x1 Convolution을 사용하기도 한다. 1x1 Convolution은 이미지 픽셀크기는 유지하되, 커널의 개수를 조절하면서, Channel의 결과값을 줄이는 것이 가능하다. `1x1 Convolution`을 사용하면, 더 깊게 층을 쌓으면서 parameter수를 줄이는 것이 가능해진다.

<br>

# <span style = "color: #00adb5">References</span>
[📘부스트캠프 AI Tech 3기 Pre-Course: CNN-Convolution은 무엇인가?](https://www.boostcourse.org/onlyboostcampaitech3/lecture/1203308?isDesc=false)