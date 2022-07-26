---
title:  "[Deep Learning Basic] Transformer"
excerpt: "Transformer에 대한 설명"

categories:
  - DLBasic
tags:
  - [AI, Naver, BoostCamp, Python, Math]
toc: true
toc_sticky: true

date: 2022-02-14 01:00:00
last_modified_at: 2022-02-09 01:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Sequential Model

- Sequential Modeling이 다루기 어려운 문제인 이유
    - 문장의 마지막 단어가 생략 / Trimmed sequence
    - 문장의 중간에 단어가 생략 / Omitted sequence
    - 문장의 어순이 뒤죽박죽 / Permuted sequence
- RNN은 입력이 순차적으로 들어가기 때문에, 이런 sequential data에 대해서 모델링하기 힘들다.  
→ 이것을 해결하고자 했던게 Transformer
    
<br>

# Transformer

- Attenion is All You Need, NIPS, 2017

![image](https://user-images.githubusercontent.com/91870042/153738991-96971592-0f21-4803-8d31-1dca8c1a3527.png){: .align-center width="70%"}

- RNN과 달리 재귀적인 구조를 사용하지 않고, self-attention 구조의 사용
- 모델의 자세한 부분을 생략하고, 기계번역을 Transformer로 사용하면 밑의 그림처럼 나타난다.

![image](https://user-images.githubusercontent.com/91870042/153738995-5935719b-ae4d-466c-8229-820a96d6d4ac.png){: .align-center width="70%"}

- 자세히 들여다보기 위해서 Transformer의 구조를 살짝 들여다보면 크게 Encoder와 Decoder로 나뉜다.

![image](https://user-images.githubusercontent.com/91870042/153739000-7cd6de4e-d508-41db-b50d-30062e0da2a1.png){: .align-center width="70%"}

- 여기서 조금 더 들어가면, Encoders Block은 여러개의 encoder를 쌓아올려서 만든 구조이다. 이 논문에 소개할 대는 6개의 encoder와 decoder를 쌓았다. 하지만 6개말고 언제든지 원하는 만큼 변경할 수 있다.

![image](https://user-images.githubusercontent.com/91870042/153739007-c3791b89-b76a-4198-871c-ccd77f60962b.png){: .align-center width="70%"}

- 하나의 문장이 입력으로 들어오면 6개의 encoder를 거치고, 다시 decoder를 거쳐서 output를 낸다. encoder와 decoder에 대해서 더 자세하게 알기 위해서 먼저, encoder의 내부를 뜯어보면 다시, `Self-Attention` 과 `Feed Forward Neural Network` 로 구성되어 있다.

![image](https://user-images.githubusercontent.com/91870042/153739011-3d8da40c-81c8-4216-b88d-c38c61a43c9a.png){: .align-center width="70%"}

- 인코더로 들어온 입력은 먼저 self-attetion layer을 거쳐서 feed forward nn layer로 들어가는 구조를 갖고 있다.
- self attetion layer에 대해서 더 자세히 다루기 앞서서, 어떤 문장에 대한 입력을 기계가 계산하기 쉽게 하도록 임베딩을 사용해 벡터로 변환하는 작업을 해야한다.

![image](https://user-images.githubusercontent.com/91870042/153739020-c81efb7f-3fe0-4dcf-843d-32f18d9ab339.png){: .align-center width="70%"}

- 이 임베딩된 각 단어들을 self-attention layer에 전달되게 된다.

![image](https://user-images.githubusercontent.com/91870042/153739025-80d02a8a-bf7f-4db9-896d-bca71b4e36d4.png){: .align-center width="70%"}

- self-Attention layer에 전달된 각 단어의 벡터 $$x$$ 는 레이어를 거친 벡터 $$z$$ 로 바뀌는데 일반적으로 신경망은 하나의 벡터에 대해서 여러가지 연산을 거친후 결과를 낸다.
- 하지만, Self-Attention layer에서는 하나의 단어벡터가 입력으로 들어가면 나머지 단어벡터에 대해서도 같이 고려해서 결과를 낸다.
- 그와 반대로, Feed Forward NN에서는 독립적이고 병렬적으로 연산이 진행된다.

![image](https://user-images.githubusercontent.com/91870042/153739035-83f8fce5-876b-4f03-81da-ec50396385c6.png){: .align-center width="70%"}

---

- 예를 들어, 이번에는 입력으로 2개의 단어 Thinking 과 Machines가 주어졌고, 이를 벡터로 변환까지 되었다고 하자. 그러면 하나의 encoder 내에서 2개의 layer에 의해 연산이 되고 결과를 뱉어낸다. 이 결과물은 다시 다음 encoder의 입력으로 들어간다.

![image](https://user-images.githubusercontent.com/91870042/153739042-46f5045d-f5c1-4d41-834e-bc58f9438d16.png){: .align-center width="70%"}

---

- Self Attention layer에서 수행하는 것을 하나의 예로 살펴보자.
    
    > *The animal didn’t cross the street because **it** was too tired*
    > 
    
    위의 문장에서 it 이 가리키는것이 무엇인지 어떤 것과 연결이 되는지 바로 self-attention layer에서 계산한다. 다시말해, it이 다른 단어들과 얼만큼의 연관성을 갖고 있는지 계산해준다.
    

![image](https://user-images.githubusercontent.com/91870042/153739050-613f3e4b-593e-4e5a-87b3-3ee68be7056f.png){: .align-center width="70%"}

- 다시 돌아와서, self-attention을 더 자세히 들여다 보면, 입력 벡터로부터 3개의 또다른 벡터를 만들어내게 된다. 이 각각의 벡터를 Query 벡터, Key 벡터, Value 벡터라고 한다.
- 이 3개의 벡터가 만들어지는 것은, 또 다른 각각의 layer에 대해서 3개의 독립적인 학습 가능한 행렬들을 각각 곱해서 만든다.
- 새롭게 만들어진 벡터는 기존의 벡터들보다 더 작은 사이즈를 갖는 것이좋다. 반드시 작아야 한다는 것은 아니지만, 줄이게 되면 multi-head attention의 계산복잡도를 일정하게 만들 수 있다.

![image](https://user-images.githubusercontent.com/91870042/153739056-446af061-9d2c-47f9-a29d-b6f6dd9f25ce.png){: .align-center width="70%"}

- $$x_1$$벡터에 대해서 학습가능한 쿼리행렬 $$W^Q$$를 곱해서 쿼리벡터 $$q_1$$을 얻어낸다. 마찬가지로 다른 입력벡터, 다른 key, value벡터에 대해서도 동일하게 진행한다.
- 그러면 이제 이 3개의 벡터가 갖는 의미에 대해서 알아봐야한다. 이는 attention내에서 생각과 계산할 때 도움이 되는 추상적인 개념이고, attention이 실제로 어떻게 계산되는지 알면 자연스럽게  역할을 알 수 있게 된다.
- self-attention layer 내에서 3가지 벡터를 준비하고 난 다음 단계는, 점수를 계산하는 것이다. 이 점수는 서로 다른 입력에 대해서 얼마나 유사도 또는 연관성을 갖고 있는지를 말한다.
- 점수가 계산되는 방식은 현재 단어의 쿼리 벡터 $$q_1$$과 점수를 매기려고 하는 다른 단어의 키 벡터 $$k_2$$에 대해서 내적으로 계산한다.

![image](https://user-images.githubusercontent.com/91870042/153739061-94299c6c-49da-4bce-8192-62f77c114465.png){: .align-center width="70%"}

- 하나의 단어에 대해서 다른 단어와의 점수를 계산하였다면, 이제 이 점수를 key벡터의 크기의 제곱근으로 나눈다. 좀더 수식적으로 표현하면, $$d_k$$를 key벡터의 크기라고 했을 때, $$\sqrt{d_k}$$로 나눈다.
- 나누고 난 값을 이번에는 확률론적 해석을 하기 위해서 softmax를 통과시켜 총 합을 1이 될 수 있게끔 만들어준다.

![image](https://user-images.githubusercontent.com/91870042/153739067-b4d24d6f-0151-429c-b645-5e5bc7000d45.png){: .align-center width="70%"}

$$
\text{softmax} = \frac{e^{z_j}}{\sum_{k=1}^{K}e^{z_k}}\quad \text{for j = 1, \dots, K}
$$

- 그 다음 단계는 이제 각 단어들의 value 벡터에 이 점수를 곱하는 것이다. 이렇게 곱하는 것은 우리가 집중하고 싶은 단어는 남기고, 관련이 없는 단어들은 무시하기 위해서이다.
- 곱해지고 난 벡터들에 대해서 모두 덧셈을 해주면, 최종적으로 attention layer의 하나의 단어에 대한 출력이 완성된다.

![image](https://user-images.githubusercontent.com/91870042/153739072-bdcf15c2-8ed7-43ca-9110-620ec5678f7f.png){: .align-center width="70%"}

- 지금까지 하나의 단어에 대해서 연산과정을 살펴봤다면, 이번에는 행렬에 대해서 계산을 알아봐야한다.
- 가장 첫번째 단계는 지금까지 했던것과 동일하게 입력행렬에 대해서 key, value, query 행렬을 만들어 주는 것이다.

![image](https://user-images.githubusercontent.com/91870042/153739080-fad7e1fc-ddb9-4d7c-a04b-9c4ee6a0ba5c.png){: .align-center width="70%"}

- 위의 그림에서 입력행렬 $$X$$의 각 행이 의미하는 것은 입력 문장의 각 단어에 해당된다.
- 위에서 연산한 나머지 과정들을 한번에 수식과 그림으로 표현하면 다음과 같다.

![image](https://user-images.githubusercontent.com/91870042/153739082-904ecdf6-86bc-4996-8bf7-a4bec0dd45b0.png){: .align-center width="70%"}

- 위에서 softmax를 계산할 때는 행단위로 연산을 진행한다.
- 또 하나의 중요한 점은 Query와 Key행렬은 내적을 진행하기 위해서 두 차원이 동일해야 하지만, Value 행렬은 그렇지 않아도 된다는 것이다.


## MHA: Multi Headed Attention

- 이번에는 위에서 본 self-attention layer에 “multi-headed” 구조를 포함시켜서 성능을 향상시킨다.

![image](https://user-images.githubusercontent.com/91870042/153739086-8ba016d6-a83b-4b4e-85d8-17ae953fe63e.png){: .align-center width="70%"}

- multi headed attention은 말그대로 attention연산을 하나의 입력에 대해서 여러번 수행시키는 것을 말한다.
- 이렇게 되면, 각 head에 대해서 query, key, value 행렬을 생성하기 위한 학습가능한 행렬이 필요하다.
- head가 1개인 self-attention 구조와 마찬가지로 연산을 하게 되면, 결국에 multi-headed-attention은 $$n$$ 개의 서로다른 $$z$$ 행렬을 output으로 얻게 된다.

![image](https://user-images.githubusercontent.com/91870042/153739091-924e247b-c086-438e-84f2-fb2f02038471.png){: .align-center width="70%"}

- 위의 그림처럼 8개의 head를 가지고 attention 연산을 수행했다면, 문제는 이 8개의 행렬을 바로 feed-forward layer로 보낼 수 없다는 것이다.
- 왜냐하면, feed-forward layer는 한개의 위치에 대해 오칙 하나의 행렬만을 input으로 받을 수 있기 때문이다. 그렇기 때문에 이 8개의 행렬을 가지고 어떻게든 1개의 행렬로 만들어내는 방법을 생각해내야 한다.
- 이 문제를 해결하기 위해서 MHA는 모든 행렬을 이어붙이고, 또다른 행렬곱을 통해 그 크기를 변형시킨다.

![image](https://user-images.githubusercontent.com/91870042/153739096-52d3341b-6be9-4921-b8bc-0cb42c4b98a8.png){: .align-center width="70%"}

- 변형된 output을 가지고 feed-forward layer로 보내면 그게 MHA의 끝이다.
- 다시 전체 과정을 한눈에 정리해보면 다음과 같다.

![image](https://user-images.githubusercontent.com/91870042/153739103-1609d08c-573f-4724-9412-68a07f50b765.png){: .align-center width="70%"}


- 지금까지 Attention 연산을 살펴보았는데, 사실 하나 간과하고 있던 점은 문장에서의 단어의 위치 (순서)에 대해서 전혀 고려하고 있지 않는다는 것이다.
- 실제로 어떤 말을 하게 되면 그 문장의 순서가 중요한 역할을 한다. 이 문제를 해결하기 위해서 단어를 벡터로 바꿔주는 임베딩에 `positional encoding` 이라는 하나의 벡터를 추가한다.
- 이 벡터를 추가함으로 써, 나중에 각 단어의 위치와 sequence내에서 위치 차이에 대한 정보를 알 수 있게 해준다.

![image](https://user-images.githubusercontent.com/91870042/153739107-e6f60126-98cb-464e-97ff-2d37816719b6.png){: .align-center width="70%"}

- 만약 위의 예시처럼 임베딩의 크기가 4라고 한다면, 실제 각 위치에 따른 positional encoding은 다음과 같이 나온다.

![image](https://user-images.githubusercontent.com/91870042/153739110-f7592e22-993a-4922-a6c2-4557db9f2db0.png){: .align-center width="70%"}

- 다음은 512개의 셀을 가진 벡터이며 각 셀은 -1 ~ 1 사이의 값을 갖는다. 여기서 첫번째 행이 말하는 것은 첫번째 단어에 대한 positional encoding에 해당된다.

![image](https://user-images.githubusercontent.com/91870042/153739116-8398c114-6b22-4fee-a57c-6a312934756b.png){: .align-center width="70%"}

- 위의 positional encoding 이미지에서 반으로 나뉜것 처럼 보이는 것은 서로 다른 2가지 함수에 의해서 생성되고 연결되었기 때문이다.
    - 왼쪽: sin 함수
    - 오른쪽: cos 함수
- 최근의 positinal encoding은 조금 바뀌어서 다음과 같은 형태를 띈다.

![image](https://user-images.githubusercontent.com/91870042/153739121-39f599ce-8530-4288-a1ba-21812fc9e8d5.png){: .align-center width="70%"}

---

- encoder에 대한 설명을 넘어가기 전에, 하나 알고가야 하는 것은 각 encoder 내에서 sublayer가 ResNet에서 사용된 Residual Connection으로 연결되어 있고, 이 후에는 layer-normalization 과정을 거친다.

![image](https://user-images.githubusercontent.com/91870042/153739126-f49bcff6-f9ef-4a60-8443-f96964a73f4e.png){: .align-center width="70%"}

- 위에서 벡터들과의 layer normalization을 더 자세히 들여다 보면 아래와 같다.

![image](https://user-images.githubusercontent.com/91870042/153739131-eb0b08a1-2d3c-4b8b-bf9c-d7857c8003aa.png){: .align-center width="70%"}

- encoder내에 있는 residual connection은 decoder부분에도 똑같이 적용되어 있다. 만약 2개의 encoder와 decoder가 있는 transformer구조라면 다음과 같이 되어있을 것이다.

![image](https://user-images.githubusercontent.com/91870042/153739136-0dcbc983-f22b-4896-9e61-fd1ada87f876.png){: .align-center width="70%"}

---

### Decoder 구조

![image](https://user-images.githubusercontent.com/91870042/153739143-8ab707f2-ef5d-46cb-a135-5af61bdf8531.png){: .align-center width="70%"}

- encoder의 마지막에서 나온 출력은 다시 2개의 벡터 $$K, V$$로 변형이 된다. 이 벡터들은 decoder의 encoder-decoder attention layer에서 decoder가 입력 시퀀스에서 적절한 위치에 집중할 수 있도록 도와준다.

![https://nlpinkorean.github.io/images/transformer/transformer_decoding_1.gif](https://nlpinkorean.github.io/images/transformer/transformer_decoding_1.gif){: .align-center width="70%"}

- decoding 단계에서는 하나의 번역된 단어를 출력으로 뱉어낸다. 이  뱉어내는 과정은 `EOS` (end of sentence)가 출력될 때 까지 반복해서 진행된다.
- decoder에서 출력으로 뽑아낸 단어는 다시 deocder의 맨 밑의 입력으로 들어가게 되고 이는 encoder와 동일하게 임베딩과 positional encoding 과정을 거쳐서 전달된다.

![https://nlpinkorean.github.io/images/transformer/transformer_decoding_2.gif](https://nlpinkorean.github.io/images/transformer/transformer_decoding_2.gif){: .align-center width="70%"}

- 이 decoder 내에서 존재하는 self-attention layer는 encoder의 self-attention layer와 다르게 동작한다.
- decoder에서는 output sequence에 대해서 현재 위치 이전에 있는 것에 대해서만 attend를 진행할 수 있다. 이 과정은 일반적인 attention 계산에서 softmax를 취하기 전에 현재 스텝 이후의 위치들에 대해서 -INF로 치환해주면 된다.

- encoder-decoder attention layer에서는 multi-head self-attention과 단 한가지의 차이점을 가지고 있다.
- 그 차이점은 Query 행렬을 밑의 layer에서 가져오고, key와 value행렬들을 encoder의 출력에서 가져온다는 점이다. decoder에서 가장 처음에 말한 부분이 이것이다.

- transformer의 가장 말단인 Linear과 softmax layer에서는 소수점으로 이루어진 벡터를 하나의 단어로 바꿔주는 일을 한다.
- 먼저, Linear Layer는 Fully-Connected 신경망으로 decoder가 마지막으로 뱉어낸 벡터값을 훨씬 더 큰 크기의 벡터인 logits 벡터로 투영시킨다.
- Softmax Layer에서는 logits 벡터의 각 원소를 확률로 해석해서 가장 높은 확률 값을 갖는 셀에 해당하는 단어를 결과물로 출력한다.

![image](https://user-images.githubusercontent.com/91870042/153739155-43465b7f-8e62-486c-8036-aed1b00ebd84.png){: .align-center width="70%"}

## ViT: Vision Transformer

- 최근에는 위의 Transformer를 사용해서 Computer Vision 분야에서도 사용한다.

![image](https://user-images.githubusercontent.com/91870042/153739159-b4c52e7a-f720-4e9d-8cbd-c5672e5c33b7.png){: .align-center width="70%"}

- 문장을 주면, 그 문장에 맞는 이미지를 그려주는 `DALL-E` 라는 모델도 있다.

![image](https://user-images.githubusercontent.com/91870042/153739162-2fdf2b26-38d8-4e22-b379-730e13710a58.png){: .align-center width="70%"}

<br>

# References

[📘 The Illustrated Transformer - Jay Alammar](https://jalammar.github.io/illustrated-transformer/)

[📘 트랜스포머(Transformer) - 딥 러닝을 이용한 자연어 처리 입문](https://wikidocs.net/31379)