---
title:  "[부스트캠프 Pre-Course] 딥러닝 기초: RNN"
excerpt: "Sequential model의 정의와 종류, RNN에 대한 정의와 종류"

categories:
  - boostcamp
tags:
  - [AI, Naver, BoostCamp, Python, Math]
toc: true
toc_sticky: true
 
date: 2021-12-08
last_modified_at: 2021-12-08
---

# <span style = "color: #00adb5">Sequential Model</span>
`RNN`은 `Sequence Model`이다. 입력과 출력을 시퀀스 단위로 처리하는 모델이다. 시퀀스 모델은 연속된 데이터를 의미하는데 마치 흔히 접하는 음성, 비디오, 행동(동작)과 같다. 이처럼 일상생활에서 접하는 대부분의 데이터는 Sequence 데이터이다.

## Sequential Data처리
### Naive Sequence Model
Sequential Data를 통해서 미래를 예측하고자 할 때, 과거의 데이터가 계속 쌓여 너무 커져버릴 수 있다. 예를 들어, 어떠한 단어들의 연속이 주어졌을 때, **과거에 나온 단어를 이용**해서 다음에 나올 단어를 예측하는 것이다. 이러한 방법으로 진행되면, 시간\\(t\\)가 진행됨에 따라서 데이터의 개수가 점점 커지게 된다. 이것을 `Naive sequence model`이라고 하며, 수식으로 표현하면 다음과 같다.

\begin{aligned}
    P(x_t|x_{t-1}, x_{t-2}, \dots, x_0)
\end{aligned}

### Autoregressive Model
위에서 고려해야하는 데이터의 개수가 시간이 지남에 따라 너무 많아지는 것을 해결하기 위해, 고정된 기간의 이전 데이터만 보는 방법이 고안되었다. 해당 모델을 `autoregressive model`이라고 한다.

봐야하는 과거의 기간을 \\(\tau\\) 라고 표기하면 고려해야 하는 데이터의 양이 많아지는 것을 방지할 수 있고, 수식으로 다음과 같이 표기한다.

\begin{aligned}
    P(x_t|x_{t-1}, x_{t-2}, \dots, x_{t-\tau})
\end{aligned}

### Markov Model
특히, \\(\tau = 1\\)인 경우. 즉, 바로 이전의 과거만을 참고하여 미래를 예측하는 모델을 `Markov model` 또는 `first order autogressive model`이라고 무른다. 하지만 매우 비효율 적인 것이, 다음처럼 비유 할 수 있다.

> 내일 수능을 보는데, 내일의 점수는 오늘 나온 시험성적에 의존해서 나온다.

실제로는, 몇 년간 공부한 내용을 바탕으로 수능 성적이 결정되지만, 바로 이전의 성적으로 점수를 예측하는 것은 비효율적이다. 하지만, `joint distribution`을 표현하기가 매우 간단해진다. 수식으로 표현해보자.

\begin{aligned}
    p(x_1, \dots, x_r) &= p(x_t|x_{t-1})p(x_{t-1}|x_{t-2})\dots p(x_2|x_1)p(x_1)\\\\\\
    &= \prod_{t=1}^{T}p(x_t|x_{t-1})
\end{aligned}

### Latent Autoregressive Model
위에서 나온 문제점들을 모두 종합해보자
1. 앞선 과거를 고려하기에는 그 데이터가 너무 방대하다
2. 과거를 보는 기간을 고정하기에는 그보다 먼 과를 반영할 수 없다.
3. 마찬가지로, 바로 앞의 과거만을 고려하면 앞선 과거를 반영할 수 없다.

위의 문제점을 해결하기 위해서 고안된 모델이 `Latent Autoregressive Model`이다. 이 모델은 과거의 데이터들을 요약해서 변수 \\(h_t\\)에 저장한다. 해당 변수를 `hidden state`라고 말을 한다. 만약, \\(h_t\\)에 대한 값을 예측하고 싶다면, \\(h_{t-1}]\\)의 값과, 새로 들어온 데이터 \\(x_{t-1}\\)을 통해서 연산한다. 수식으로 표현하면 다음과 같다.

\begin{aligned}
    \hat{x} &= p(x_t|h_t)\\\\\\
    h_t &= g(h_{t-1}, x_{t-1})
\end{aligned}

<br>

# <span style = "color: #00adb5">Recurrent Neural Network</span>
RNN과 MLP의 차이점은 자기 자신으로 돌아오는 구조가 하나 추가되어 있다는 것이다. 이것을 시간 순서대로 풀면 다음 사진처럼 나타낼 수 있다.

![image](https://user-images.githubusercontent.com/91870042/145337606-b5d167b4-c8ec-4e6c-9a1c-16c2018f481c.png){: .align-center}

## Short-term dependencies
위의 RNN구조의 문제점에 대해서 알아보자. 위에서는 과거의 데이터를 요약해서 hidden state은 \\(h_t\\)에 저장을 했었다. 그리고 hidden state를 다시 다음 미래를 예측하기 위해서 사용되었다. 

이런식으로 계속 반복되면, 먼 과거의 데이터는 반영이 되지만, 아주 적게 반영이 된다. 그에 비해서 가까운 과거의 데이터는 크게 반영이 된다. 지금의 문제를 수식을 통해서 어떻게 이루어지는지 알아보자.

![image](https://user-images.githubusercontent.com/91870042/145338097-44117a44-759c-4d67-baa6-59630359757a.png){: .align-center}

위의 사진을 보면, 처음 들어온 데이터 \\(h_0\\)는 \\(h_4\\)가 되기 위해서 4번의 `Activation Function`에 의해 연산되어야 한다. 이 과정에서 먼 미래의 데이터 일수록, 적게 반영이 되는 것을 알 수 있다. 이 과정에서 활성함수에 따라 다음 2 현상이 일어날 것을 예측할 수 있다.

- `Vanishing gradient`: Activation Function을 `Sigmoid`함수로 설정했을 때, 그 결과 값은 `0~1.0`사이의 값이 나온다. 이러한 값이 중첩되어서 곱해지게 되면 그 결과 값은 갈수록 작아지는 것을 알 수 있다.

- `Exploding gradient`: Activation Function을 `ReLU`함수로 설정했을 때, 그 결과 값은 `0 ~ ∞`사이의 값이 나온다. 1이상의 수가 중첨되어서 곱해직 되면, 그 결과 값은 갈수록 더 커지는 것을 알 수 있다.

<br>

# <span style = "color: #00adb5">LSTM: Long Short Term Memory</span>
먼저, 지금까지의 RNN구조(`Vanilla RNN`)를 다시 그림으로 나타내보자. 아래 그림에서는 과거에 연산되어 요약된 정보 \\(h_{t-1}\\)이 현재 시점의 데이터 결합되어 `tanh` 활성함수에 의해 다시 \\(h_t\\)로 요약되는 것을 볼 수 있다. 

![image](https://user-images.githubusercontent.com/91870042/145338380-d6a62ea9-d26b-46ac-a3ab-31e135c5d98e.png){: .align-center}

이번에는 LSTM의 구조에 대해서 보자. 이 구조가 해당 문제를 어떻게 해결하는지 알아보자.

![image](https://user-images.githubusercontent.com/91870042/145339011-c1622094-481a-4a2d-9139-f5f9cba13c29.png){: .align-center}

1. `previous cell state`: 과거 시점 \\(0~t-1\\)의 정보를 요약해서 가지고 있는 정보인 Hidden State를 말한다. 흔한 비유로 `컨베이어 벨트`에 비유할 수 있다.

2. `Update cell state`: `Forget Gate`와 `Input Gate`를 통해서 연산한 결과 값을 새로운 `cell state`로 업데이트를 해주는 부분이다.

    \begin{aligned}
        i_t &= \sigma(W_i \times [h_{t-1}, x_t] + b_i)\\\\\\
        C_t &= f_t * C_{t-1} + i_t * \hat{C_t}
    \end{aligned}
  

3. `Forget Gate`: 과거의 정보를 잊기 위한 게이트이다. 바로 이전의 hidden state인 \\(h_{t-1}\\)과 입력된 데이터\\(x_t\\)를 받아서 `Sigmoid`를 해준 값이 이번 gate의 출력값이다. `Sigmoid`함수의 출력 범위는 0~1이기 때문에 그 값이 0이라면 이전 상태의 정보는 잊고, 1이라면 이전 상태의 정보를 기억한다.

    \begin{aligned}
        f_t = \sigma(W_f \times [h_{t-1}, x_t] + b_f)
    \end{aligned}

  
4. `Input Gate`: 현재 정보를 기억하기 위한 게이트이다. 바로 이전의 hidden state인 \\(h_{t-1}\\)과 입력된 데이터\\(x_t\\)를 받아서 `Sigmoid`를 해주고, 또 다른 입력으로, `tanh`해준 값을 `Hadamard product`연산을 취해준다. 이 연산 결과값이 이번 게이트의 출력 값이다.

    \begin{aligned}
        i_t &= \sigma(W_i \times [h_{t-1}, x_t] + b_i)\\\\\\
        \hat{c_t} &= tanh(W_c \times [h_{t-1}, x_t] + b_C)
    \end{aligned}

  
5. `Output Gate`: 어떤 정보를 output으로 출력할지 결정하는 부분이다. 이 부분에 들어오는 데이터는 cell state를 바탕으로 필터링된 값이 들어온다. 여기에서 연산되는 값은, \\(h_{t-1}\\)과 입력된 데이터\\(x_t\\)를 받아서 `Sigmoid`를 해준값과, cell state의 값을 `tanh`한 값의 곱이다. 이렇게 되면, output으로 보내고자 하는 부분만을 출력할 수 있다.

    \begin{aligned}
        o_t &= \sigma(W_o \times [h_{t-1}, x_t] + b_o)\\\\\\
        h_t &= o_t * tanh(C_t)
    \end{aligned}


<br>

# <span style = "color: #00adb5">GRU: Gated Recurrent Unit</span>
LSTM의 변형으로, `reset gate`와 `update gate` 단 2가지만 존재하며, `cell state`가 없고, `hidden state`만 존재한다.

![image](https://user-images.githubusercontent.com/91870042/145341000-fc37a481-b394-496b-99ff-aa95f9453995.png){: .align-center}

<br>

# <span style = "color: #00adb5">References</span>

[📘 부스트캠프 AI Tech 3기 Pre-Course: Sequential Models - RNN](https://www.boostcourse.org/onlyboostcampaitech3/lecture/1203373?isDesc=false)

[📘 LSTM 이해하기](https://dgkim5360.tistory.com/entry/understanding-long-short-term-memory-lstm-kr)

[📘 RNN과 LSTM을 이해해보자!](https://ratsgo.github.io/natural%20language%20processing/2017/03/09/rnnlstm/)

[📘 Sequential Models - RNN](https://lemidia.github.io/development/boostcamp-week3-day15/)

[📘 Wikidocs: 순환신경망](https://wikidocs.net/22886)

