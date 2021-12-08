---
title:  "[부스트캠프 Pre-Course] AI수학: RNN"
excerpt: "시퀀스 데이터의 개념과 특징, 이를 처리하기 위한 RNN"

categories:
  - boostcamp
tags:
  - [AI, Naver, BoostCamp, Python, Math]
toc: true
toc_sticky: true
 
date: 2021-12-08
last_modified_at: 2021-12-08
---

# <span style = "color: #00adb5">시퀀스 데이터</span>
`시퀀스(sequence) 데이터`는 소리, 문자열, 주가등의 데이터를 말한다. 시계열 데이터는 시간 순서에 따라 나열된 데이터로 시퀀스 데이터에 속한다. 시퀀스데이터는 순서가 매우 중요한 요소 중 하나이다.

시퀀스 데이터는 독립동등분포(IID, Independent Identical Distribution)가정을 잘 위배하기 때문에 순서를 바꾸거나 과거 정보에 손실이 발생하면 데이터의 확률분포도 바뀌게 된다. 마치, 과거 정보 또는 앞뒤 맥락없이 미래를 예측하거나 문장을 완성시키는것이 불가능한것과 동일하다.

> 개가 사람을 물었다. 사람이 개를 물었다. 

이렇게 순서를 바꾸었을 때, 데이터의 확률분포가 바뀌거나, 과거의 정보를 가지고 미래를 예측할때, 예측분포의 성격이 많이 바뀔 수 있다. 그렇기 때문에 데이터의 순서를 바꾸어서 손실이 발생하면 안된다.

## 시퀀스 데이터 다루기
시퀀스 데이터의 순서를 바꾸면 안되기 때문에 그 데이터를 처리해야하는 방법이 필요하다. 

이전 시퀀스의 정보를 활용하면 앞으로 발생할 데이터의 확률분포를 다루기 위해 `조건부확률`을 사용할 수 있다. 이전 `베이즈의 법칙`에 따라 수식을 다음과 같이 표현할 수 있다.

\\(P(X_1,\dots,X_t)=P(X_t\mid X_1,\dots,X_{t-1})P(X_1,\dots,X_{t-1})\\)

![image](https://user-images.githubusercontent.com/91870042/145170647-cdf6dc1e-d301-4ea2-933e-cfb10e29cbcd.png){: .align-center}

현재 정보인 \\(X_t\\)의 분포를 위의 수식을 통해서 얻어내면 \\(P(X_t\mid X_1,\dots,X_{t-1})\\)로 나타낼 수 있다. 위의 수식에서는 과거의 모든 정보를 사용한다고 나타내지만, 시퀀스 데이터를 분석할 때는 모든 과거 정보들을 활용할 필요는 없다. 예를 들어 다음문장을 예측한다고 할때, 모든 문장을 보지 않고 앞뒤 맥락을 통해서 예측할 수 있는것과 비슷하다.

이렇기 때문에 시퀀스 데이터를 다루기 위해서는 길이가 가변적인 데이터를 다룰 수 있는 모델이 필요하다. 조건부 확률에서 \\(X_t\\)를 구하고 싶다면, \\(X_{t-1},\dots,X_1\\)의 시점이 필요한다. 앞에서 설명했듯이, 모든 시점에 대해서 예측하는 것이 아닌, 가변적인 특정길이를 사용하여 예측할 수 있다. 아래 그림에서는 그 가변 길이를 \\(\tau\\)로 표현한다.

![image](https://user-images.githubusercontent.com/91870042/145172854-6bad782a-a1ab-4005-a988-1ab628836bae.png){: .align-center}

고정된 길이 \\(\tau\\)만큼의 시퀀스"만"사용하는 경우 `AR`\\((\tau)\\)(Autoregressive Model) `자기회귀모델`이라고 부른다. 하지만 \\(\tau\\)의 값은 사전이 미리 설정해야 하는 것이기 때문에 특정 사전지식이 필요하기도 한다.

문제에 따라서는 \\(\tau\\)의 값이 바뀔 수 있다. (어떤 문제는 가까운 시점의 자료만, 어떤 문제는 아주 먼 시점의 자료가 필요)

![image](https://user-images.githubusercontent.com/91870042/145173379-756307fc-d413-4d9c-a8b4-fb46fba9bb45.png){: .align-center}

또 다른 방법으로는 바로 이전 정보를 제외한 나머지 정보들을 \\(H_t\\)라는 잠재변수로 인코딩해서 활용하는 잠재 AR모델이 있다. 

![image](https://user-images.githubusercontent.com/91870042/145173990-a4b9acb9-2d63-44bc-87d7-3119a511b21a.png){: .align-center}

이 경우, `바로 이전 시점의 정보`와 `잠재변수 \\(H_t\\)`의 데이터를 가지고 현재시점과 미래시점을 예측할 수 있다. 이렇게 문제를 바라보면 고정된 길이의 시퀀스만 사용하게 된다. 이것을 활용해 가변적인 길이의 데이터를 고정된 길이의 데이터로 바꿔서 해결할 수 있다.

> 이렇게 잠재변수 \\(H_t\\)를 신경망을 통해 반복해서 사용하여 시퀀스 데이터의 패턴을 학습하는 모델이 RNN(Recurrent Neural Network)이다.

<br>

# <span style = "color: #00adb5">RNN</span>
가장 기본적인 RNN모형은 아래 그림과 같이 MLP와 유사한 모양이다. 하지만, 일반적인 MLP를 사용하면 이전 시점의 정보를 사용할 수 없다. 

![image](https://user-images.githubusercontent.com/91870042/145174245-522cd573-6a02-492c-9225-a51e5df16b25.png){: .align-center}

아래의 그림을 보게 되면 이전의 정보를 사용하지 않고, 현재 시점\\(t\\)에 대해서만 연산을 하는 것을 알 수 있다.그렇기 때문에 RNN에서 사용하기 위해서는 조금의 변형이 필요하다.

![image](https://user-images.githubusercontent.com/91870042/145179163-15b937be-bcaf-4baf-9feb-bb765169638a.png){: .align-center}


## RNN의 순전파
위의 모델은 과거 시점의 데이터를 다룰 수가 없다. 과거변수 \\(H_t\\)안에 담아야 하는데 RNN은 이전순서의 잠재변수와 현재의 입력을 활용하여 모델링하여 문제를 해결하였다.

![image](https://user-images.githubusercontent.com/91870042/145174725-7bea0017-3976-4cc0-9825-4c02b19e1125.png){: .align-center}

여기서 눈여겨 봐야하는 것은 가중치 행렬이 3개가 나온다
1. \\(W_{X}^{(1)}\\) : 입력데이터로 부터 선형모델을 통해 잠재변수를 만들어내는 가중치 행렬
2. \\(W_{H}^{(1)}\\) : 이전 시점의 잠재변수로부터 정보를 받아서 현재시점의 잠재변수로 인코딩하는 가중치 행렬
3. \\(W^{(2)}\\): 이렇게 만든 데이터 통해서 출력으로 만들어주는 가중치 행렬

위의 가중치 행렬은 시점\\(t\\)에 따라서 변화하는 행렬이 아니다. 시점\\(t\\)에 따라서 변화되는 것은, 입력, 잠재변수, 데이터만 있다.

## RNN의 역전파
RNN의 역전파는 잠재변수의 연결 그래프에 따라 순차적으로 계산하며 `BPTT`라고 한다. 아래그림의 빨간색 선이 전달 경로이다. 잠재변수는 2가지의 입력을 받게 된다. 하나는 `출력에서 오는 gradient`, 다른 하나는 `다음 시점의 gradient`이다. 다시 값을 전달 받은 \\(H_t\\)는 입력과 전 시점의 \\(H_{t-1}\\)로 값을 전달한다.

![image](https://user-images.githubusercontent.com/91870042/145175466-ec75dddb-dca0-4712-a132-d24345a62ea6.png){: .align-center}

### BPTT
BPTT를 통해 RNN의 가중치행렬의 미분을 계산해보면 아래와 같이 미분의 곱으로 이루어진 항이 계산된다.

![image](https://user-images.githubusercontent.com/91870042/145180958-87694b06-d8c2-4a78-b795-1f18430bfb25.png){: .align-center}

각 가중치의 행렬의 미분을 계산했을 때, 최종적으로 나오는 \\(\prod \\)은 시퀀스의 길이가 길어질수록 그 값이 매우 커지거나 작아질수 있어서 불안정해진다. 이러한 문제를 `기울기 소실 문제`라고 부른다. 따라서 모든 시점의 데이터를 가지고 학습시키기에는 어려움이 있다.

### BPTT 기울기 소실의 해결책
---

![image](https://user-images.githubusercontent.com/91870042/145176379-3c168837-93dc-4b63-bea4-f535542b723f.png){: .align-center}

시퀀스 길이가 길어지는 경우 BPTT를 통한 역전파 알고리즘의 계산이 불안정 해지므로 길이를 끊는 것이 중요하다. 이 해법을 `truncated BPTT`라고 한다. 하지만 이것으로 완전한 해결을 할 수 있는것은 아니다.

이러한 문제로 인하여 최근에는 기본적인 RNN모델을 사용하지 않고, `LSTM`과 `GRU`를 사용한다.

![image](https://user-images.githubusercontent.com/91870042/145176482-d21b3829-b4a9-40aa-bd11-940167f15b3f.png){: .align-center}

<br>

# <span style = "color: #00adb5">References</span>
[📘 부스트캠프 AI Tech 3기 Pre-Course: RNN 첫걸음](https://www.boostcourse.org/onlyboostcampaitech3/lecture/1203368?isDesc=false)