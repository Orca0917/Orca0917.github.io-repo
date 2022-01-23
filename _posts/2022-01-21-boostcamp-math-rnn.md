---
title:  "[AI Math] RNN"
excerpt: "시퀀스 데이터의 개념과 특징, 이를 처리하기 위한 RNN"

categories:
  - aimath
tags:
  - [AI, Naver, BoostCamp, Python, Math]
toc: true
toc_sticky: true
 
date: 2022-01-20 04:00:00
last_modified_at: 2022-01-20 04:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

RNN(Recurrent Neural Network)는 CNN과 다르게, `시계열 데이터`, `sequence 데이터`에 대해서 다룬다. 이때, 기존가지 독립동등분포(IID)로 들어오는 데이터와 달리 시계열 데이터같이 독립적이지 않게 들어오는 데이터가 있다. 이제 이 데이터에 대해서 어떻게 모델을 설계하고 학습할지 다룬다.

# 시퀀스 데이터
`시퀀스(sequence) 데이터`는 쉽게 말해 **순차적으로 들어오는 데이터**로서 소리, 문자열, 주가등의 데이터를 말한다. 시계열 데이터는 시간 순서에 따라 나열된 데이터로 시퀀스 데이터에 속한다. 시퀀스데이터는 순서가 매우 중요한 요소 중 하나이다.

시퀀스 데이터는 `독립동등분포`(IID, Independent Identical Distribution)가정을 잘 위배하기 때문에 순서를 바꾸거나 과거 정보에 손실이 발생하면 데이터의 확률분포도 바뀌게 된다. 마치, 과거 정보 또는 앞뒤 맥락없이 미래를 예측하거나 문장을 완성시키는것이 불가능한것과 동일하다.

✔ 독립동등분포(i.i.d)<br>
Wikipedia에서는 다음과 같이 정의한다. A collection of random variables is independent and identically distributed if each random variable has the same 
probability distribution as the others and all are mutually independent. <br>
해석하면 각 확률변수가 다른 확률분포와 동일한 확률분포를 갖고 모두 상호 독립적인 경우에 독립동등분포라고 한다. 결국, 어떠한 사건 A의 발생이 다른 사건(확률변수)에 영향을 미치지 않는 분포를 말한다.
{: .notice--success}

> 개가 사람을 물었다. 사람이 개를 물었다. 

이렇게 순서를 바꾸었을 때, 데이터의 확률분포가 바뀌거나 과거의 정보를 가지고 미래를 예측할 때, 예측분포의 성격이 많이 바뀔 수 있다. 그렇기 때문에 데이터의 순서를 바꾸어서 손실이 발생하면 안된다.

## 시퀀스 데이터 다루기
시퀀스 데이터의 순서를 바꾸면 안되기 때문에 그 데이터를 처리해야하는 방법이 필요하다. 

이전 시퀀스의 정보를 활용하면 앞으로 발생할 데이터의 확률분포를 다루기 위해 `조건부확률`을 사용할 수 있다. 결합확률 분포를 이전 `베이즈의 법칙`에 따라 수식을 다음과 같이 표현할 수 있다.

$$ P(X_1,\dots,X_t)=P(X_t\mid X_1,\dots,X_{t-1})P(X_1,\dots,X_{t-1}) $$

다시 동일한 베이즈 법칙을 이용해서 전개를 진행하면 다음과 같고, 마지막 줄의 product로 변환할 수 있다.

\begin{aligned}
  P(X_1,\dots,X_t)&=P(X_t\mid X_1,\dots,X_{t-1})P(X_1,\dots,X_{t-1})\\\\\\
  &=P(X_t\mid X_1,\dots,X_{t-1})P(X_{t-1}\mid X_1, \dots,X_{t-2})\\\\\\
  &=\prod_{s=1}^{t}P(X_s\mid X_{s-1},\dots,X_1) 
\end{aligned}

현재 정보인 \\(X_t\\)의 분포를 위의 수식을 통해서 얻어내면 \\(P(X_t\mid X_1,\dots,X_{t-1})\\)로 나타낼 수 있다. 위의 수식에서는 과거의 모든 정보를 사용한다고 나타내지만, <u>시퀀스 데이터를 분석할 때는 모든 과거 정보들을 활용할 필요는 없다.</u> 예를 들어 다음문장을 예측한다고 할때, 모든 문장을 보지 않고 앞뒤 맥락을 통해서 예측할 수 있는것과 비슷하다.

이렇기 때문에 시퀀스 데이터를 다루기 위해서는 **길이가 가변적인 데이터를 다룰 수 있는 모델**이 필요하다. 조건부 확률에서 \\(X_t\\)를 구하고 싶다면, \\(X_{t-1},\dots,X_1\\)의 시점이 필요한다. 앞에서 설명했듯이, 모든 시점에 대해서 예측하는 것이 아닌, 가변적인 특정길이를 사용하여 예측할 수 있다. 아래 그림에서는 그 가변 길이를 \\(\tau\\)로 표현한다.

### 자기회귀모델
![image](https://user-images.githubusercontent.com/91870042/145172854-6bad782a-a1ab-4005-a988-1ab628836bae.png){: .align-center}

고정된 길이 \\(\tau\\)만큼의 시퀀스"만"사용하는 경우 AR(\\(\tau\\))(Autoregressive Model) `자기회귀모델`이라고 부른다. 하지만 \\(\tau\\)의 값은 `하이퍼 파라미터`로 사전에 미리 설정해야 하는 것이기 때문에 특정 사전지식이 필요하기도 한다.

문제에 따라서는 \\(\tau\\)의 값이 바뀔 수 있다. (어떤 문제는 가까운 시점의 자료만, 어떤 문제는 아주 먼 시점의 자료가 필요)

### 잠재AR모델
또 다른 방법으로는 바로 이전 정보를 제외한 나머지 정보들을 \\(H_t\\)라는 잠재변수로 인코딩해서 활용하는 `잠재 AR모델`이 있다. 

$$ X_t \sim P(X_t \mid X_{t-1}, H_t) \quad H_t=X_t-2,\dots,X_1 $$

이 경우, `바로 이전 시점의 정보`와 `잠재변수` \\(H_t\\)의 2개의 **고정된 길이**의 데이터를 가지고 현재시점과 미래시점을 예측할 수 있다.이것을 활용해 가변적인 길이의 데이터를 고정된 길이의 데이터로 바꿔서 해결할 수 있다.

> 이렇게 잠재변수 \\(H_t\\)를 신경망을 통해 반복해서 사용하여 시퀀스 데이터의 패턴을 학습하는 모델이 RNN(Recurrent Neural Network)이다.

<br>

# RNN
가장 기본적인 RNN모형은 아래 그림과 같이 MLP(Multi Layer Perceptron)와 유사한 모양이다. 하지만, 일반적인 MLP를 사용하면 이전 시점의 정보를 사용할 수 없다. 

\begin{aligned}
  O &= HW^{(2)}+b^{(2)} \\\\\\
  H &= \sigma(XW^{(1)}+b^{(1)})
\end{aligned}

- H: 잠재변수
- \\(\sigma\\): 활성함수
- W: 가중치 행렬
- b: 편향

아래의 수식을 보게 되면 위의 수식과 동일하지만 특정 시점에 대한 연산을 수행하고 있다. 하지만 이전의 정보를 사용하지 않고 현재 시점 \\(t\\) 에 대해서만 연산을 하는 것을 알 수 있다. 그렇기 때문에 RNN에서 사용하기 위해서는 조금의 변형이 필요하다.

\begin{aligned}
  O_t &= H_tW^{(2)}+b^{(2)} \\\\\\
  H_t &= \sigma(X_tW^{(1)}+b^{(1)})
\end{aligned}

RNN의 구현 <잠재변수>
---
\begin{aligned}
  O_t &= H_tW^{(2)}+b^{(2)} \\\\\\
  H_t &= \sigma(X_tW_X^{(1)}+H_{t-1}W_H^{(1)}+b^{(1)})
\end{aligned}

1. \\(W_{X}^{(1)}\\) : 입력데이터로 부터 선형모델을 통해 잠재변수를 만들어내는 가중치 행렬
2. \\(W_{H}^{(1)}\\) : 이전 시점의 잠재변수로부터 정보를 받아서 현재시점의 잠재변수로 인코딩하는 가중치 행렬
3. \\(W^{(2)}\\): 이렇게 만든 데이터 통해서 출력으로 만들어주는 가중치 행렬

이전의 수식과 살펴보았을 때 다른점은, 잠재변수 \\(H_t\\)를 사용하여 다음 수선의 잠재변수를 인코딩하는데 사용한다는 점이 다르다. 이렇게 사용하면 이전시점의 정보들이 다음에도 영향을 미칠수 있도록 넘기는 것이 가능하다.

여기서 총 3개의 가중치 행렬이 등장하는데, 이는 시점 \\(t\\) 에 따라서 **변하지 않는** 가중치 행렬이다. 시점 \\(t\\) 에 따라서 변하는 것은 입력으로 들어오는 `입력벡터`와 업데이트 되는 `잠재변수` 뿐이다.

## RNN의 순전파

![image](https://user-images.githubusercontent.com/91870042/150681018-efc95e1a-d11d-4d2d-a12e-c0b0f1572dfa.png){: .align-center}

위의 사진은 활성함수를 `tanh`로 설정하여 모델링한 RNN이다. \\(t\\) 시점에 입력으로 들어온 데이터 \\(x_t\\)와, 이전시점의 데이터 \\(h_{t-1}\\)을 활용하여 새로운 잠재변수 \\(h_t\\)와 현재시점의 출력 \\(o_t\\)를 만들어내는 것을 확인할 수 있다.

> RNN은 이전 순서의 잠재변수와 현재의 입력을 활용하여 모델링한다.

## RNN의 역전파 (BPTT)
RNN의 역전파는 잠재변수의 연결 그래프에 따라 순차적으로 계산하며 `BPTT`라고 한다. 잠재변수는 2가지의 입력을 받게 된다. 하나는 `출력에서 오는 gradient`, 다른 하나는 `다음 시점의 gradient`이다. 다시 값을 전달 받은 \\(H_t\\)는 입력과 전 시점의 \\(H_{t-1}\\)로 값을 전달한다. 이렇게 전달을 하여 최종적으로 가장 처음 시점까지 전달이 될 수 있게 한다.

![image](https://user-images.githubusercontent.com/91870042/150681191-b82bc9fe-f56a-4829-983f-83ccaa87cb76.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/145180958-87694b06-d8c2-4a78-b795-1f18430bfb25.png){: .align-center}

하지만 여기서 문제가 발생한다. 바로 기울기 소실의 문제이다. 각 가중치의 행렬의 미분을 계산했을 때, 최종적으로 나오는 \\(\prod \\)은 시퀀스의 길이가 길어질수록 그 값이 매우 커지거나 작아질수 있어서 불안정해진다. 이러한 문제를 `기울기 소실` 또는 `폭주` 라고 부른다. 따라서 모든 시점의 데이터를 가지고 학습시키기에는 어려움이 있다.

### BPTT 기울기 소실의 해결책

![image](https://user-images.githubusercontent.com/91870042/145176379-3c168837-93dc-4b63-bea4-f535542b723f.png){: .align-center}

시퀀스 길이가 길어지는 경우 BPTT를 통한 역전파 알고리즘의 계산이 불안정해지므로 길이를 끊는 것이 중요하다. 이 해법을 `truncated BPTT`라고 한다. 하지만 이것으로 완전한 해결을 할 수 있는것은 아니다. 

이러한 문제로 인하여 최근에는 기본적인 RNN모델을 사용하지 않고, `LSTM`과 `GRU`를 사용한다.

![image](https://user-images.githubusercontent.com/91870042/150681560-0498b669-0dc2-4e73-9977-dfe2bae4716d.png){: .align-center}

<br>

# References

[📘 Independent and identically distributed random variables - wikipedia](https://en.wikipedia.org/wiki/Independent_and_identically_distributed_random_variables)

[📘 LSTM vs GRU Network: Which has better performance?](https://www.tutorialexample.com/lstm-vs-gru-network-which-has-better-performance-deep-learning-tutorial/)

[📘 Vanishing Gradient Problem](http://computing.or.kr/14804/vanishing-gradient-problem%EA%B8%B0%EC%9A%B8%EA%B8%B0-%EC%86%8C%EB%A9%B8-%EB%AC%B8%EC%A0%9C/)

[📘 Recurrent Neural Network(RNN)이란?](https://m.blog.naver.com/sw4r/221294142675)