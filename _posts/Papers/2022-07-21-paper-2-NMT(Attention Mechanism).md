---
title: "[논문 리뷰] Neural Machine Translation - Attention Mechanism"
excerpt: "Neural machine translation by jointly learning to align and translate"

categories:
  - paper
tags:
  - [AI, Papers]
toc: true
toc_sticky: true
use_math: true

date: 2022-07-21
last_modified_at: 2022-07-21
---
![image](https://user-images.githubusercontent.com/91870042/180634544-37bc1ba4-d213-4781-8774-b8c857a7b041.png){: .align-center width="60%"}

현재 자연어 처리(NLP) 분야뿐만 아니라, 다른 분야 추천 시스템과 컴퓨터 비전 분야에서도 Transformer 구조는 정말 많이 사용되고 있습니다. 또한 AI 분야를 조금만 공부하더라도 Transformer라는 단어를 한 번이라도 들어볼 수 있습니다. 이번 논문은 Transformer 구조에 사용되는 Attention 메커니즘 최초로 소개한 논문입니다.

<br>

# Introduction

이 논문이 해결하려고 한 주요 문제는 번역입니다. 특히 신경망 구조를 사용한 번역으로, `Nueral Machine Translation` 줄여서, NMT 문제라고 합니다. Attention 구조를 사용하기 전까지는 NMT에 주로 사용되던 방법은 Encoder와 Decoder의 구조를 사용한 번역입니다.

![image](https://user-images.githubusercontent.com/91870042/180630526-62d632b2-095e-4815-b7e5-3ce142ca3e3f.png){: .align-center width="50%"}

`Seq2Seq` 구조를 보면 바로 이해할 수 있는데, 주요 포인트만 설명하자면 "**입력 문장을 고정된 길이의 벡터로 압축시키고, 이 압축된 벡터를 통해서 번역하고자 하는 언어로 복원**시킨다" 라고 할 수 있습니다. 압축을 시키는 과정이 Encoder이며, 복원시키는 과정이 Decoder 입니다. 물론 더 자세하게는 추가되는 연산이 있습니다.

논문의 저자들은 바로 여기에 문제를 제기합니다. "**고정된 길이의 벡터로 압축하기 때문에 긴 문장의 경우 정보의 손실이 발생할 수 있다**". 실제로 Encoder-Decoder 구조의 모델에 더 긴 문장의 입력을 넣을수록, 그 성능을 감소하는 것을 확인할 수 있습니다.

앞서 말한 문제점을 해결하기 위해서 저자는 새로운 방법을 제시합니다. 기존의 Encoder-Decoder 구조에 확장을 한 형태인데, Decoder를 통해서 번역을 진행할 때마다 현재 생성된 단어와 가장 관련성이 높은 원래의 단어의 위치를 찾고 해당 단어와 압축된 고정길이 벡터를 사용해 다음에 올 단어를 예측합니다.

여기서 구별되는 가장 큰 차이점은 일반적인 Encoder-Decoder 구조는 전체 문장을 하나의 고정길이 벡터로 압축했다면, 저자들은 **여러 개의 고정길이 벡터**로 압축을 시켰습니다. 그리고 이 벡터들이 Decoder 과정에서 어떤 벡터가 새로 번역되는 값과 잘 맞는지 매칭할 때 사용됩니다.

<br>

# Background

다시 NMT로 돌아와 봅시다. NMT는 확률론적으로 아래와 같이 수식으로 표현할 수 있습니다. 영어로 된 입력 문장이 들어왔을 때, 이를 불어로 정확하게 번역한 $\mathbf{y}$가 나올 확률을 최대화하는 것입니다. 따라서 조건부 확률로 표현할 수 있고, 확률을 높이는 방향으로 출력 문장을 학습하게 만들 수 있습니다.

$$
\underset{\mathbf{y}}{\text{argmax}}\;\mathbb{P}(\mathbf{y}\,\vert\,\mathbf{x})
$$

- $\mathbf{x}$ : 입력 문장 (영어)
- $\mathbf{y}$ : 정답 출력 문장 (불어)

이렇게 해서 NMT 문제가 무엇인지 정의를 하였으며, 문제를 해결하기 위해서는 앞서 말했듯이 기존의 Encoder Decoder 구조를 사용해서 번역을 진행했었습니다. 이번에 논문으로 새로운 구조를 발표하는 것이긴 하나, 사실 기존의 Encoder Decoder를 사용한 구조도 꽤나 좋은 성능을 보여주고 있었습니다.

<br>

## RNN Enc-Dec

기존의 RNN을 사용한 Encoder와 Decoder의 구조를 수식으로 표현해 보겠습니다. RNN 구조에서 입력 $\mathbf{x} = (x_1, \cdots, x_{T_x})$을 받아 입력에 대한 hidden state를 만들고, 이 hidden state에 대해서 고정길이의 벡터 $c$를 생성하는 수식입니다.

$$
h_t = f(x_t, \; h_{t-1}) \\
c = q(\{h_1, \cdots, h_{T_x} \})
$$

- $h_t$ : $t$ 시간에 생성된 hidden state
- $c$ : hidden states 로 부터 생성된 벡터, context vector
- $f,\;q$ : 비선형 함수

그리고 이를 응용하여 조건부확률로 표기해 봅시다. 각 시점에 대해서 다음 나올 단어를 정답에 가깝게 만들어야 잘 번역했다고 할 수 있으므로, 각 시점에 대한 곱으로 나타낼 수 있습니다.

$$
\mathbb{P}(\mathbf{y}) = \prod_{t=1}^{T} \mathbb{P}(y_t \,\vert\, \{ y_1, \cdots, y_{t-1}\},\;c)
$$

식을 해석하면, ①이전의 출력과 ②hidden state로부터 생성한 context 벡터가 있을 때, 이 2개를 사용하여 올바른 다음 단어가 나올 확률들을 말합니다. 위의 조건부확률은 실제로 모델에서는 RNN을 사용하게 됩니다.

<br>

# Learning to align & translate

지금부터는 논문에서 소개한 새로운 구조에 대한 상세한 설명을 진행하겠습니다. 새로운 구조의 Encoder에서는 양방향(Bidirectional) RNN을 사용하였으며, Decoder 과정에서는 문장 내의 단어들을 탐색하면서 올바른 번역이 이루어지도록 만들어졌습니다.

## Decoder

새로운 구조를 사용한 조건부확률의 수식을 저자들은 다음과 같이 정의했습니다.

$$ 
\mathbb{P}(y_i \,\vert\, y_1,\,\dots,\,y_{i-1},\, \mathbf{x}) = g(y_{i-1}, s_i, c_i)\\
s_i = f(s_{i-1}, y_{i-1}, c_i)
$$

- $y_i$ : $i$ 번째 timestep 에 대한 출력
- $\mathbf{x}$ : 입력 문장
- $s_i$ : $i$ 번째 timestep 의 hidden state
- $c_i$ : 전체 문장에 대한 context vector. 바로 아래에서 연산과정에 대해 소개!

여기서 등장하는 $c_i$를 계산하는 수식은 다음과 같습니다.

$$
c_i = \sum_{j=1}^{T_x} \alpha_{ij}h_j \qquad \alpha_{ij} = \frac{\exp(e_{ij})}{\sum_{k=1}^{T_x}\exp(e_{ik})} \qquad e_{ij} = a(s_{i-1}, h_j)
$$

- $a$ : FFN(Feed Forward Network)
- $e_{ij}$ : alignment model, $j$ 번째 입력과 $i$ 번째 출력이 얼마나 잘 맞는지 나타내는 점수
- $h_j$ : Encoder에서 생성한 $j$ 번째 hidden state

context vector는 $e_{ij}$ 를 확률로 해석한 $\alpha_{ij}$ 와 hidden state $h_j$ 를 곱해줌으로써 연관이 된 것의 값은 더 높게, 연관이 적은 것은 적게 영향을 주어 전체 문맥을 통해서 다음 단어를 예측하도록 설계하였습니다.

![image](https://user-images.githubusercontent.com/91870042/180633366-517cee85-fcd7-4593-aae9-cd5da14e23e6.png){: .align-center width="30%"}

위의 그림은 지금까지 소개한 내용을 압축해놓아 표현한 그림입니다. 가장 밑에서 양방향 RNN의 모습을 표현하였고, 여기서 생성된 hidden state $h_i$ 와 시점 $t$ 에서 생성되는 단어와의 연관 정도 $\alpha_{t,i}$ 를 가중합하여 계산하는 것까지 모두 포함되어 있습니다.

이런 Decoder 구조가 가져오는 장점에 대해서 살펴봅시다. 가장 먼저 모든 문장을 **단 하나의 hidden state로 압축할 필요가 없다**는 것입니다. 각 시점마다의 hidden state를 두고 비교를 하면서 주의를 기울일 것만 집중해서 확인하는 attention 구조가 이런 고정길이 벡터의 문제점을 해결해 주었습니다.

<br>

## Encoder

가장 기본적인 RNN 구조, 바닐라 RNN 구조를 사용하게 되면 RNN이 생성하는 hidden state는 전체의 문맥의 정보를 담을 수 없고, 맨 앞에서부터 현재 시점 $t$ 까지의 정보만 담고 있을 수 있습니다. 하지만, 전체 문장의 정보를 담고, 현재 시점 $t$ 에 대한 정보만을 더 집중해서 저장하고 싶었기 때문에 논문은 Bidirectional RNN 구조를 사용하였습니다.

양방향 RNN 구조는 순방향 RNN의 hidden state와 역방향 RNN의 hidden state 결과를 concatenation 시켜서 저장합니다. 이를 통해 $h_i$ 는 앞서 오는 단어들과 뒤에 오는 단어들의 정보를 모두 가지고 있을 수 있게 되었습니다.

$$
h_j = \left[ \overrightarrow{h}_j^\intercal ; \overleftarrow{h}_j^\intercal\right]
$$

- $\overrightarrow{h}_j$ : 순방향 RNN에서 얻은 hidden state
- $\overleftarrow{h}_j$ : 역방향 RNN에서 얻은 hidden state

<br>

# Experimental Settings

이제 모델의 구조에 대한 설명은 끝났습니다. 아래에서는 제안된 attention 구조를 사용한 EncDec 모델과 일반 RNN을 기반으로 한 EncDec 모델의 실험 방법과 그 결과에 대해서 소개합니다. 평가에 사용되는 문제는 영어 문장을 프랑스어 문장으로 변환하는 번역 문제입니다.

모델을 학습시키기 위한 설정은 다음과 같습니다.

- SGD
- Adadelta
- minibatch (size: 80)

WMT`14에서 제공하는 영어-불어 말뭉치 데이터셋에서 실험 결과는 attention 구조를 사용한 것이 길이가 긴 문장에서도 강건한 성능을 보여주었습니다. 반면에 RNN EncDec 모델은 문장의 길이가 길어질수록 그 성능이 감소하는 모습을 보여주었습니다.

![image](https://user-images.githubusercontent.com/91870042/180633985-06d7153a-4e84-4389-80fd-301b04b76e45.png){: .align-center width="70%"}

Attention 구조를 사용한 RNNsearch도 학습을 한 방법에 따라 성능의 차이는 존재했습니다. 입력 문장이 약 30개의 단어로 구성되어 있다면, 성능이 이전과 유사했지만, 50개 이상의 단어로 구성된 데이터셋으로 훈련시킨 결과 BLEU 점수가 계속 높은 모습을 보여주었습니다.

<br>

# Results

## Quantitative results

데이터셋에 존재하는 수많은 문장에 대해서 RNNsearch가 모든 경우에서 더 뛰어난 성능을 제공했습니다. 또한 문법 기반 번역인 Moses와 거의 동등하거나 우월한 성능을 보여주었습니다. 여기서 다시 한번 저자들은 고정된 길이의 벡터가 갖는 문제에 대해서 소개하며 이것이 이번 모델의 핵심 중 하나라고 설명하였습니다.

<br>

## Alignment

논문에서 소개한 가중치 값 $\alpha_{ij}$ 를 gray scale로 시각화한 결과를 보여주면서 soft-alignment의 장점에 대해서 설명하겠습니다.

![image](https://user-images.githubusercontent.com/91870042/180634411-1d12b075-c62c-41e1-abcc-b5624cba48c8.png){: .align-center width="40%"}

위의 시각화 결과는 논문에서 제시한 시각화 결과 중 하나입니다. 위는 원래의 원본 문장이 어떤 단어들과 매칭이 이루어지는지 표현합니다. 흰색에 가까울수록 해당 단어가 번역하고자 하는 단어와 연관성이 높다는 것을 말하고 있습니다.

`Soft-alignment`의 장점으로는 하나의 단어가 여러 개의 단어와 매칭이 이루어질 수 있다는 것입니다. 이러한 구조가 해석을 하는 데 있어서 더 참고를 많이 할 수 있기 때문에 뛰어난 번역을 할 수 있습니다.

반면에 `Hard-alignment`는 하나의 단어가 하나의 단어와 매칭이 이루어집니다. 때문에 해당 단어를 해석하는데 다른 단어들과의 연관성을 살펴보기가 힘들어 더 정확한 번역 결과를 얻을 수 없습니다.

<br>

# Reference

[📜 NEURAL MACHINE TRANSLATION BY JOINTLY LEARNING TO ALIGN AND TRANSLATE](chrome-extension://efaidnbmnnnibpcajpcglclefindmkaj/https://arxiv.org/pdf/1409.0473.pdf)