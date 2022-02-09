---
title:  "[Deep Learning Basic] RNN: Recurrent Neural Network"
excerpt: "RNN과 LSTM, GRU에 대한 설명"

categories:
  - DLBasic
tags:
  - [AI, Naver, BoostCamp, Python, Math]
toc: true
toc_sticky: true

date: 2022-02-09 00:00:00
last_modified_at: 2022-02-09 00:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Sequential Model

- 길이가 가변적이기 때문에 입력으로 들어오는 값의 차원을 알수가 없다.
- 이전의 데이터가 들어왔을 때, 다음에 나올 것이 무엇인지 예측하는 모델
- 과거의 정보를 고려해서 미래를 예측하는데, 시간이 지날수록 고려해야하는 과거의 정보량이 많아지는 문제점이 있다.

![image](https://user-images.githubusercontent.com/91870042/153200831-9472c79a-eb75-458f-8794-dcb4a741cc23.png){: .align-center width="70%"}


- 너무 많은 과거의 정보를 고려해야하는 문제를 해결하기 위해서, 고정된 길이의 과거만큼만 고려해서 미래를 예측하는 방법론이 제시되었다.

![image](https://user-images.githubusercontent.com/91870042/153200866-6535f31f-4d7d-4399-8907-27e34700e405.png){: .align-center width="70%"}

- 또 다른, 비슷한 방법으로 “현재는 바로 이전의 과거에 의존적”이다 라는 가정으로 만들어진 방법이 있다. 이 방법을 사용하게 되면 joint distribution을 쉽게 표현할 수 있는 장점이 있다.
- 하지만, 말이 안되는게 예를들어 내일의 수능성적이 오늘 공부했던 것 만으로 결정되는 것은 아니다. 그렇기 때문에 바로 이전시점의 과거가 아닌 그 이전의 과거도 같이 고려를 해야한다.

![image](https://user-images.githubusercontent.com/91870042/153200894-aa5bde07-a13c-4df6-b6f6-e0d16d5fe009.png){: .align-center width="70%"}

- 더 이전의 정보를 효과적으로 저장하기 위한 hidden state인 $$h$$ 를 추가하였다. 이 $$h$$가 가리키는 것은 현재 이전의 과거 정보들을 모두 요약해서 가지고 있는 정보이다. 그리고 그 요약된 정보를 이용해서 미래를 예측하는 방법을 생각해냈다. → RNN의 기반!

![image](https://user-images.githubusercontent.com/91870042/153201026-c956e597-9d29-4c11-9d00-1653a0a35393.png){: .align-center width="70%"}

<br>

# Recurrent Neural Network

![image](https://user-images.githubusercontent.com/91870042/153200939-987a4528-b7e4-4fc9-a50c-15e65ad29b1e.png){: .align-center width="20%"}

- 이 그림이 가장 기본적인 RNN의 구조를 표현한 것이다. 어떠한 입력이 들어왔을 때, 이전의 정보를 같이 사용해서 다음을 예측하고, 그 정보를 다시 사용해서 다음의 미래를 예측하는 것이다.
- 위 그림을 조금 더 풀어서 보면, 많은 입력이 주어지는 Fully Connected Layer로 볼 수 있다.

![image](https://user-images.githubusercontent.com/91870042/153200966-d58999c4-6b94-420b-90be-ff5b05f7ff0b.png){: .align-center width="70%"}

- 여기서 발생할 수 있는 문제점은 옛날 정보가 너무 많은 연산을 거쳐서 넘어오는데, 여기서 그 정보가 살아남기 힘들게 된다.
- 옛날의 정보는 덜 고려하게 되고, 최근의 정보만 비교적 높게 고려되는 문제가 발생한다. 이 문제가 생겨나는 정확한 원인은 Gradient Vanihsing 또는 Gradient Exploding 문제이다.

![image](https://user-images.githubusercontent.com/91870042/153201087-ae96b65a-41f6-4792-9812-bfe53d956a1f.png){: .align-center width="70%"}

- 가장 마지막 수식에서 초기 정보 $$h_0$$이 현재까지 전달되기 위해서는 총 4개의 layer를 거쳐야만 한다. 동시에, 활성함수도 그만큼 거치는데 이때 기울기 소실/폭발 문제가 생긴다.

![image](https://user-images.githubusercontent.com/91870042/153201104-dc2fdde0-add6-46f8-8a81-bbe019005635.png){: .align-center width="70%"}

- 예를 들어, tanh나 sigmoid의 경우 입력으로 들어온 값을 0~1 사이의 값으로 squashing하는 성질을 갖는다. 이것이 계속 반복되다보면, 점점 그 값이 너무 작아져 기울기가 사라진다.
- 반대로, ReLU를 사용했다고 하고 처음에 나온 값이 0보다 크다면 그 값을 그대로 출력으로 내기 때문에 곱해져서 계속 커지게 되는 기울기 폭발현상이 일어난다.

<br>

# LSTM: Long Short Term Memory

- LSTM은 위에서 설명한 문제를 해결할 수 있는 구조이다.
- 활성함수로 tanh를 사용하는 기본적인 RNN구조를 표현하면 아래 그림처럼 나타낼 수 있다.

![image](https://user-images.githubusercontent.com/91870042/153201131-d50143a3-b255-4581-a3db-5e85e73a29b3.png){: .align-center width="70%"}

- 내부의 구조가 꽤나 단순한 vanila RNN에 비해서 LSTM은 내부 구조가 복잡하다.
- LSTM의 핵심은 3개의 Gate로 이루어져 있는 것이다. (Forget, Input, Output)

![image](https://user-images.githubusercontent.com/91870042/153201155-2f0b2a14-8a24-461c-92da-7554e02027d1.png){: .align-center width="70%"}

- 핵심아이디어는 이전 입력에서 들어온 정보를 그대로 보내주는 컨베이어벨트와 같은 것이 있다면, 이 컨베이어 벨트에서 중간에 어떤 것을 내리고, 또 다시 올리는 그런 역할을 하는 것이다.

![image](https://user-images.githubusercontent.com/91870042/153201171-b17d4a4d-7787-4919-9900-6650f5004d7f.png){: .align-center width="70%"}

- Forget Gate는 이전 시점의 hidden state $$h_{t-1}$$과 현재의 입력 $$x_t$$를 사용해 이전의 정보에서 어떤 것을 버릴지 “제거리스트”를 만든다. 그 리스트는 $$f_t$$가 가지고 있다.
- 나중에 이 $$f_t$$를 이용해서 이전 시점에서 넘어오는 정보로 부터 버릴 것을 걸러낸다.

![image](https://user-images.githubusercontent.com/91870042/153201201-ed71d841-de82-4f77-94a3-ca8fb5722559.png){: .align-center width="70%"}

- Input Gate는 이전 시점의 hidden state $$h_{t-1}$$과 현재의 입력 $$x_t$$를 사용해 어떤 것을 컨베이어 벨트위로 올릴지 결정한다.
- 먼저, $$\tilde{C_t}$$는 올릴정보에 대한 예비군을 선택한 것을 말하는데 Cell state candidate라고 부르기도 한다.
- $$i_t$$는 그 후보군중에서 어떤 정보를 추가할 것인지 결정하는 역할을 한다. 최종적으로 결정한 것을 다시 컨베이어 벨트위로 올려서 다음 시점의 입력으로 전달하거나 hidden state로 전달한다.

![image](https://user-images.githubusercontent.com/91870042/153201223-0ae37659-13bc-459e-8d7b-1d7482ca3c86.png){: .align-center width="70%"}

- update cell은 위에서 말한 각 과정을 컨베이어 벨트에 올리고, 제거하는 업데이트 역할을 수행하는 것을 말한다.

![image](https://user-images.githubusercontent.com/91870042/153201247-1bbcfacd-0a6a-4eb3-a070-e7da5cafa66d.png){: .align-center width="70%"}

- 사실 마지막에 다음 hidden state로 전달할 때, 현재의 컨베이어 벨트 모습을 그대로 전달하는 것은 아니다.
- 여기서도 마찬가지로 최종적으로 어떤 정보를 전달할지 시그모이드를 거쳐서 $$o_t$$를 만들어낸다.

## GRU: Gated Recurrent Unit

![image](https://user-images.githubusercontent.com/91870042/153201283-2600e225-5f69-43ca-81da-a424765cc062.png){: .align-center width="70%"}

- LSTM은 3개의 gate를 갖는 반면, GRU는 단 2개의 Gate를 갖는다.
    - Reset Gate
    - Update Gate
- LSTM과 달리 Cell state가 없고, hidden state 하나로 연산을 진행한다.
- Gate가 적다는 것은, 그만큼 각 과정에서 필요한 parameter의 수가 적다는 것을 말하고, parameter의 수가 적고, 연산의 output이 동일하다면, parameter의 수가 적은 GRU가 더 유리한 경우가 많다.