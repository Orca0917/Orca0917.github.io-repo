---
title:  "[Deep Learning Basic] Generative Models Part1"
excerpt: "Conditional Independence와 Chain Rule"

categories:
  - DLBasic
tags:
  - [AI, Naver, BoostCamp, Python, Math]
toc: true
toc_sticky: true

date: 2022-02-10 00:00:00
last_modified_at: 2022-02-10 00:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Introduction

- “What I cannot create I do not understand” - Richard Feynman
- generative model을 학습한다: 그럴듯한 이미지나, 문장을 만드는 것?  
→ 이것이 생성모델의 전부가 아니다. 이것보다 더 많은 것을 포함하는 개념

![image](https://user-images.githubusercontent.com/91870042/153741211-ce4e4c90-c956-42e8-908f-908f028ae168.png){: .align-center width="70%"}


<br>

# Learning a Generative Model

![image](https://user-images.githubusercontent.com/91870042/153741215-9ada6402-e235-4234-80a5-1388d44faeb8.png){: .align-center width="70%"}

- 강아지의 이미지가 주어져 있다고 하자. 다음을 만족하는 확률분포 $$p(x)$$를 얻고 싶다.
    - **Generation**: train 데이터로 주어진 강아지 사진 이외에 사진을 만들어 내는 것.
     $$x_{new}\sim p(x)$$라면, $$x_{new}$$는 강아지와 같은 사진이어야 한다.
    - **Density estimation:** 이 Density estimation은 이미지가 강아지같으면, 강아지일 확률을 강아지가 아닐 확률보다 높게 설정하는 것을 말한다. (분류문제를 포함)
        - 이러한 모델을 **explicit model**이라고 한다. 입력에 대해서 확률값을 얻어낼 수 있는 모델
        - Generative 모델 안에서도 explicit model과 implicit model로 나뉘어진다.
    - **Unsupervised representation learning**: 이미지들이 갖는 공통점을 알고 있어야 한다. 이런것을 feature learning이라고 한다. ex. 귀가 2개, 꼬리가 있다, ... .
- 이제 문제는 $$p(x)$$를 어떻게 표현하는지다.

<br>

# Basic Discrete Distributions

- **Bernoulli distribution**: (biased) coin flip
    - 앞/뒤만 나오는 확률분포 
    
    $$D=\{앞면, 뒷면\}$$
    
    - $$P(X=\text{앞면})=p$$라 하면, $$P(X=뒷면)=1-p$$
    - 하나의 확률 값만 알면 나머지는 자동으로 알게 된다.
    - 베르누이 확률분포를 갖는 확률분포를 표현할 때는 $$X\sim \text{Ber}(p)$$라고 한다.
- **Categorical distribution**: (biased) m-sided dice
    - 1~m의 숫자가 나오는 확률분포 
    
    $$D=\{1,\dots,m\}$$
    
    - $$P(Y=i)=p_i$$라고 하면, $$\sum_{i=1}^{m}p_i=1$$이다.
    - $$m-1$$개의 확률값만 알면 전체 $$m$$개 각각의 확률을 알 수 있다. // 전체확률을 다 더하면 1
    - 카테고리 확률분포를 표현할 때는 $$Y\sim\text{Cat}(p_1,\dots,p_m)$$라고 한다.
    
    ## 예제1:  Modeling an RGB joint distribution (of a single pixel)
    
    ![image](https://user-images.githubusercontent.com/91870042/153741220-57172615-1a4d-4264-9531-daaa63e69ab0.png){: .align-center width="40%"}
    
    - r, g, b로 이루어진 색상을 표현하는 가짓수 = 256 × 256 ×256 
    $$(r,g,b)\sim p(R,G,B)$$
    - 각각의 확률을 계산하기 위해서 필요한 파라미터의 수 = 256 × 256 ×256 - 1
    → 하나의 픽셀에 대해서 필요한 파라미터의 수가 너무 많다.
    
    ## 예제2: Modeling an Binary(BW) Joint distribution (of a single pixel)
    
    ![image](https://user-images.githubusercontent.com/91870042/153741221-fb6012bc-c3a5-4c01-bdfa-b5567e831736.png){: .align-center width="50%"}

    - 하나의 이미지가 $$n$$개의 픽셀로 이루어져 있다면 가능한 경우의 수 = $$2^n$$
    - 각각의 확률을 계산하기 위해서 필요한 파라미터의 수 = $$2^n-1$$
    - 여전히 많은 파라미터를 갖고 있다. → 기계학습에서 파라미터의 수가 많으면 학습하기가 어렵다는 단점이 있다.
    

<br>

# Structure Through Independence

- 위의 binary 예제에서 이미지의 각 픽셀(흑백)이 모두 독립적이라고 가정
    - 사실 말이 안되는게, 6이라는 숫자가 있다면 한 픽셀의 주변 색상은 비슷한 경향을 가짐
- 가능한 경우의 수 = $$2^n$$
- 필요한 파라미터의 수 = $$n$$
    - 각각의 픽셀에 대해서 파라미터 하나만 있으면 되기 때문. 독립적이기 때문에 하나가 주어지면, 나머지 하나를 자동으로 알 수 있다. (하나의 픽셀을 켜고 끄고 확률을 알게 된다)
- “각각의 픽셀에 대해서 독립적이다” 라는 가정만 추가했는데 필요한 파라미터의 수가 급격하게 줄었다. 다시 말해, 이는 independent 하다는 가정이 너무 말도 안되는 가정이라는 것이다.
- 그러면, dependent와 independent 그 중간을 찾으면 적절하지 않을까..?
- 모두 독립적이라는 가정이 들어간 것을 **Fully Independent model**이라고 부른다.

<br>

# Conditional Independence

- 3가지 중요한 규칙
    - **Chain rule**: $$n$$개의 joint distribution을 $$n$$개의 conditional distribution으로 바꾸는 것
        
    ![image](https://user-images.githubusercontent.com/91870042/153741226-762f29d9-63c6-4bd2-9732-320a6f6255b4.png){: .align-center width="70%"}
        
    - **Bayes’ rule**:
    
    ![image](https://user-images.githubusercontent.com/91870042/153741231-d7416545-1da1-4f10-945f-c0fb47b557a5.png){: .align-center width="70%"}
    
    - **Conditional independence**: 임의의 확률변수 $$z$$가 주어졌을 때, $$x$$와 $$y$$가 독립적이다. 그렇기 때문에 조건부 확률에서 $$z$$가 존재한다면, 나머지 두 변수에 대해서 독립적이기 때문에 $$y$$를 생략할 수 있게 된다.
    
    ![image](https://user-images.githubusercontent.com/91870042/153741238-819f4bc2-6b48-475d-9646-50c83f280a23.png){: .align-center width="70%"}
    
- **Chain rule과 Conditional independece를 섞어서 chain rule의 길어지는 뒷 항을 줄일 수 있다**.
- 위의 Chain rule 식을 다시 들고와서 파라미터의 수를 순차적으로 계산해보자.

     $$p(x_1,\dots,x_n)=p(x_1)p(x_2\mid x_1)p(x_3\mid x_1,x_2)\cdots p(x_n\mid x_1,\cdots,x_{n-1})$$

    - $$p(x_1)$$: 1개의 파라미터 필요
    - $$p(x_2\mid x_1)$$: 2개의 파라미터 필요 → $$p(x_2\mid x_1 = 0), p(x_2\mid x_1 = 1)$$
    - $$p(x_3\mid x_1,x_2)$$: 4개의 파라미터 필요
    - 이렇게 계속 계산하면 $$1+2+2^2+\cdots+2^{n-1}=2^n-1$$의 파라미터가 필요하다.
    - 이전과 동일 한 이유는, 위의 예제와 달라진게 전혀 없기 때문이다.(가정이 들어간것도 없음)
- 이번에는 **Markov assumption**을 가정해보자. 여기서 Markov assumption은 i번째 입력이 i-1번째 입력에만 연관이 있고, 그 이전의 것(0 ~ i-2)들에는 모두 독립적인 것을 말한다.
    - Markov assumption을 들여오면, Chain rule의 식을 Conditional independence 규칙에 의해서 간단하게 만들 수 있다. (**dependent한것만 남기고 제외 가능**)
    
     $$p(x_1,\dots,x_n)=p(x_1)p(x_2\mid x_1)p(x_3\mid x_2)\cdots p(x_n\mid x_{n-1})$$

    - 이렇게 되면 필요한 파라미터의 수는 $$2n-1$$이다.
        - $$p(x_1)$$:  1개, 그 외 나머지 항에 대해서는 2개씩 필요
    - 따라서, Markov assumption을 활용하면, 필요한 파라미터의 수를 지수적으로 감소시킬 수 있다.
    - 이 conditional Independency를 활용하는 것을 **Auto-regressive Model**이라고 한다.

<br>

# Auto-regressive Model

![image](https://user-images.githubusercontent.com/91870042/153741247-3439a682-5b38-40e6-98c6-cf8e65610e74.png){: .align-center width="70%"}

- 위 그림처럼 28 × 28크기의 바이너리 픽셀값을 갖는 이미지가 있다고 해보자.
- 우리의 목표는 $$p(x) =p(x_1,\dots,x_{784})$$를 학습하는 것이다.
- 어떻게 이 $$p(x)$$에 대해서 어떻게 표현할 수 있을까?
    - joint distribution을 사용하고 있기 때문에, chain rule을 한 번 사용하면 다음처럼 나타난다.
    
    $$p(x_{1:784})=p(x_1)p(x_2\mid x_1)p(x_3\mid x_{1:2})\cdots$$

    - 이것을 auto-regressive model이라고 부른다.
        - Auto regressive모델은 $$i$$번째 입력이 $$i-1$$번째 입력에 대해서만 dependent 한것을 말함
        - 뿐만 아니라, $$i$$번째 입력이 $$0\sim i-2$$번째 입력에 대해서 독립적인 것도 말함.
    - 우리는 모든 확률변수에 대해서 순서를 매겨야할 필요가 있다. (픽셀의 각 값에 순서를 정해준다.) → 2차원 공간에서 픽셀의 순서를 매기는 방법은 여러가지 존재한다.
    

<br>

# NADE: Neural Autoregressive Density Estimator

![image](https://user-images.githubusercontent.com/91870042/153741252-2692b4ee-4c77-4584-8ca8-3ad7f752e19e.png){: .align-center width="70%"}

- $$i$$ 번째 픽셀에 대한 확률분포는 아래와 같이 표현한다.
    
    $$p(x_i\mid x_{1:i-1})=\sigma(\alpha_ih_i+b_i)\quad \text{where}\,\,\,h_i=\sigma(W_{<i}\,x_{1:i-1}+c)$$
    
    - 위의 그림을 보면 입력벡터 $$x_0$$가 있고, 가장 오른쪽 각 픽셀에 대한 확률이 있다.
    - 1번째 픽셀의 확률을 구할 때는 어떠한 입력도 필요하지 않는다.
    - 2번째 픽셀의 확률을 구할 때는 1번째 픽셀의 입력이 들어온다.
    - 3번째 픽셀의 확률을 구할 때는 1~2번째 픽셀의 입력이 들어온다.
    - $$n$$번재 픽셀의 확률을 구할 때는 $$1\sim n$$번째 픽셀의 입력이 들어온다.
    - 한가지 더 눈여겨 볼 점은, 각 픽셀의 확률을 계산하는 신경망에서 입력의 크기가 달라지기 때문에 그에 맞게 곱해지는 가중치행렬$$(h_i)$$의 크기도 모두 달라진다.
- NADE는 입력이 주어지면 **각각의 확률을 구할 수 있는 explicit model**이다.
    - 확률을 계산하는 방법 → 하나의 흑백이미지에 대한 784개의 바이너리 픽셀이 존재한다면, joint distribution을 Conditional Independecy에 의해 계산할 수 있다.
        
    ![image](https://user-images.githubusercontent.com/91870042/153741257-fb64244e-3bc7-400b-a201-78df534a84c1.png){: .align-center width="70%"}
        
    - 1번째 입력에 대한 확률분포를 알고, 다시 1번째 픽셀이 주어졌을 때, 2번째 픽셀의 확률분포를 알고, 1번째 픽셀과 2번째 픽셀이 주어졌을 때, 3번째 픽셀의 확률분포를 알고있으므로 모두 곱해서 확률값 하나를 얻을 수 있다.
- 만약, 바이너리 값이 아닌 연속적인 값의 확률변수를 사용한다면, **mixture of Gaussian**을 사용해야 한다.

<br>

# Pixel RNN

- = 이미지의 Pixel을 만드는데 RNN을 사용해보겠다.
- auto-regressive 모델에 RNN을 사용할 수도 있다.
- 예를 들어서, $$n\times n$$크기의 RGB이미지가 있다고 한다면, 다음과 같이 표현한다.

![image](https://user-images.githubusercontent.com/91870042/153741262-b55aa5ea-35b8-470f-bce0-83b626df253a.png){: .align-center width="70%"}

- 이전의 만들어낸 Pixel의 정보를 가지고 현재 Pixel을 예측하는데, 앞에 있는 것들은 사용하지 않게 MASK를 사용한다.
- 일반적인 Pixel RNN을 사용하면 현재 이전의 모든 정보를 사용해서 현재 픽셀값을 예측해야하는데, 연산량이 너무 많아져서 비효율적이게 되었다. 그래서 다음 2가지 방식이 고안되었다.
- Pixel RNN은 다음 2가지 ordering 방식에 따라서 구분할 수 있다.
    - Row LSTM: $$i$$번째 픽셀을 만들 때, 위쪽에 있는 정보를 활용
    - Diagonal BiLSTM:  $$i$$번째 픽셀 이전의 정보를 모두 활용
    
    ![image](https://user-images.githubusercontent.com/91870042/153741268-acd4d946-d1bc-4e6f-9206-879c39967028.png){: .align-center width="70%"}
    
- 먼저 Row LSTM은 어떤 픽셀값을 예측할 때, 그 이전의 모든 정보를 사용하는 것이 아닌, 위쪽 3개의 정보들을 이용해서 예측한다. 하지만 이렇게 되면 바로 이전의 값의 영향을 전혀 못받으니, 바로 왼쪽의 정보(이전의 정보)를 하나 더 추가한다.
- 그래서 사진에서 보면, 위쪽의 정보와 바로 이전의 정보를 이용해서 예측하는 것을 볼 수 있다.
- Diagonal BiLSTM에 들어가기 앞서서, Diagonal LSTM은 hidden state의 나의 왼쪽과 위쪽의 정보들을 가져와서 연산을 한다. 이렇게 되면 하나의 픽셀을 예측할때 사용하는 픽셀의 수를 줄일 수는 있지만, 오른쪽 픽셀들을 전혀 사용하지 못하는 단점이 있다.
- 이를 개선하기 위해서 만들어진게, Diagonal BiLSTM이다. BiLSTM은 왼쪽위로 계산을 한 번하고, 오른쪽 위로 계산을 한번해서 합쳐서 만든다. 사용하는 pixel의 수는 일반 pixelRNN의 수와 같지만 convolution 연산을 하기 때문에 필요한 parameter의 수를 줄인다.