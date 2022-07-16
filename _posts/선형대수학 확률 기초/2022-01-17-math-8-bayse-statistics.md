---
title:  "조건부확률과 베이즈정리"
excerpt: "조건부확률에서 이어지는 개념인 베이즈 정리와 인과관계 추론"

categories:
  - aimath
tags:
  - [AI, BoostCamp, Math]
toc: true
toc_sticky: true
 
date: 2022-01-20 02:00:00
last_modified_at: 2022-07-14 00:00:00
---

![image](https://user-images.githubusercontent.com/91870042/179350313-7cdcdb3c-61f7-4ad4-bb36-8a1072d35cc4.png){: .align-center width="100%"}


통계에 이어서 조건부확률이 정확히 무엇인지 알아보고 조건부확률을 사용하는 베이즈 정리에 대해서 알아봅시다. 기계학습에서 베이즈 정리가 갖는 의미가 무엇이고 어떻게 사용될 수 있는지 코로나 바이러스를 예시로 들어 살펴보겠습니다. 마지막에서는 확률로 내린 결정이 틀린 오류와 유명한 심슨의 역설에 대해서 알아보겠습니다.

<br>

# 조건부 확률

베이즈 정리에 대해서 이해하기 전, 조건부확률이 무엇인지 알아봅시다. 조건부 확률은 어떤 사건이 조건부로 주어졌을 때, 다른 사건이 발생할 확률을 구하는 것입니다. 수학에서는 $\mathbb{P}(A\,\vert\, B)$ 라고 표현하며 이는 사건 $B$ 가 발생한 상황에서 사건 $A$ 가 발생할 확률을 뜻합니다.

조금 더 응용해서 사건 $B$ 가 발생했을 때, 사건 $A$ 가 발생하는 것이므로 이미 발생한 사건의 확률 $\mathbb{P}(B)$ 와 $B$ 가 발생한 상황에서 $A$ 가 발생하는 $\mathbb{P}(A\,\vert\,B)$ 를 곱하면 두 사건이 동시에 발생할 확률인 $\mathbb{P}(A \cap B)$ 가 됩니다.

$$ 
\mathbb{P}(A \cap B) = \mathbb{P}(B)\,\mathbb{P}(A\,\vert\,B) 
$$

이제 위의 식에서 조건부 확률만 남기고 사건 $B$ 가 발생할 확률을 좌변으로 넘기면, 사건 $B$ 가 발생할 확률 분의 사건 $A$ 와 $B$ 가 동시에 일어날 확률이 됩니다! 이제 조건부 확률이 무엇인지 알아보았으니 다음은 베이즈 정리가 어떻게 조건부 확률을 사용하여 정보를 갱신하는지 알아봅시다.

<br>

# 베이즈 정리

정보를 갱신하는 것은 아래에서 다시 설명하지만, $A$ 라는 새로운 정보가 주어졌을 때, 기존의 계산된 정보 $\mathbb{P}(B)$ 로 부터 $\mathbb{P}(B\,\vert\,A)$ 를 계산하는 방법을 제공해 줍니다.

$$
\mathbb{P}(B\,\vert\,A) = \frac{\mathbb{P}(A \cap B)}{\mathbb{P}(A)} = \mathbb{P}(B)\frac{\mathbb{P}(A \cap B)}{\mathbb{P}(A)}
$$

위의 식에서 기호만 조금 변경하여서 다른 말로 설명해 보겠습니다!

$$ \mathbb{P}(\theta \,\vert\, \mathcal{D}) = \mathbb{P}(\theta)\frac{\mathbb{P}(\mathcal{D} \,\vert\, \theta)}{\mathbb{P}(\mathcal{D})} $$

- $\mathcal{D}$: 관찰하는 데이터
- $\theta$: 가설, 모델링하는 이벤트, 모델에서 계산하고 싶은 parameter(모수)
- $\mathbb{P}(\theta \,\vert\, \mathcal{D})$ : 데이터가 주어졌을 때, 가설이 성립할 확률
- $\mathbb{P}(\mathcal{D} \,\vert\, \theta)$ : 현재 주어진 가설, 모수로 부터 데이터가 관측될 확률

<br>

위의 수식에서 살펴볼 수 있는 것은 총 4가지입니다. 이 4가지가 앞으로 베이즈 정리를 이해하는데 중요하기 때문에 하나씩 완전히 이해를 하고 넘어가야 합니다.

1. `사후 확률(posterior)`: 데이터가 주어져있을 때, 가설이 성립할 확률을 말합니다. 사후 확률이라고 부르는 것은, 데이터를 먼저 관찰하고 나서 확률을 계산하기 때문입니다. 위의 식에서 $\mathbb{P}(\theta \,\vert\, \mathcal{D})$ 에 해당하는 부분입니다.

2. `사전 확률(prior)`: 데이터가 주어지지 않은 상황에서, 가설, 이벤트 $\theta$ 가 발생할 확률을 말합니다. 위의 수식에서 $\mathbb{P}(\theta)$ 를 가리킵니다.

3. `가능도(likelihood)`: 현재 주어진 가설에서 이 데이터가 관찰될 확률입니다. 수식에서는 $\mathbb{P}(\mathcal{D} \,\vert\, \theta)$ 에 해당합니다.

4. `Evidence`: 데이터 자체의 분포를 말합니다. 데이터에 대한 확률분포 $\mathbb{P}(\mathcal{D})$ 입니다.

## 🤧 예제 : COVID-19

COVID-19의 발병률이 10%로 알려져 있다고 합시다. COVID-19에 실제로 걸렸을 때 검진될 확률은 `99%`, 실제로 걸리지 않았을 때 오검진 될 확률이 `1%` 라고 할 때, 어떤 사람이 질병에 걸렸다고 검진 결과가 나왔을 때 정말로 COVID-19에 감염되었을 확률은 어떻게 될까요?

위에서 배웠던 것들을 바탕으로 문제를 해결해 보겠습니다. 먼저 COVID-19는 하나의 이벤트이기 때문에 $\theta$ 라고 설정하겠습니다. 또한 COVID-19를 검진받는 사건은 $\mathcal{D}$ 라고 표현하면 아래와 같습니다.

- $\mathbb{P}(\theta) = 0.1$
- $\mathbb{P}(\mathcal{D}\,\vert\,\theta) = 0.99$
- $\mathbb{P}(\mathcal{D}\,\vert\,\neg\theta) = 0.01$

문제에서 최종적으로 구하고 싶은 것은, 조건부 확률 $\mathbb{P}(\theta\,\vert\,\mathcal{D})$ 입니다. 하지만, 부족한 Evidence 정보인 데이터의 분포 $\mathbb{P}(\mathcal{D})$ 를 구해야 합니다. 추가로 여기서 말하는 데이터의 분포 $\mathcal{D}$ 는 실제로 양성인 확률을 말합니다.

Evidence는 어떻게 구할 수 있을까요. 현재 주어진 조건을 통해서 충분히 구해낼 수 있습니다. Evidence 사건 $\mathcal{D}$ 은 양성일 확률을 말하기 때문에 **COVID-19에 걸렸을 때, 확진 판정을 받을 확률과 COVID-19에 감염되지 않았을 때, 확진 판정을 받을 확률을 더해주면** 됩니다.

$$ \mathbb{P}(\mathcal{D}) = \sum_\theta \mathbb{P}(\mathcal{D}\,\vert\,\theta)\mathbb{P}(\theta) = 0.99\times 0.1 + 0.01 \times 0.9 = 0.108 $$

$$ \mathbb{P}(\theta \,\vert\, \mathcal{D}) = 0.1 \times \frac{0.99}{0.108} \approx 0.916 $$

따라서 실제로 COVID-19 확진 판정을 받은 경우, 실제로 감염되었을 확률이 91.6%임을 말합니다. 이렇게 높은 정확도가 나올 수 있었던 것은 오탐률이 1%로 낮았기 때문입니다.

---

그렇다면 이번에는 오탐률을 `10%` 로 높여서 계산해보겠습니다. 이제 위에서 변경되는 식은 다음과 같습니다.

- $ \mathbb{P}(\mathcal{D}\,\vert\,\neg \theta) = 0.1 $

이를 바탕으로 Evidence를 재계산하고, 원하는 조건부 확률을 계산하면 정확도가 크게 감소함을 확인할 수 있습니다.

$$ \mathbb{P}(\mathcal{D}) = \sum_\theta \mathbb{P}(\mathcal{D}\,\vert\,\theta)\mathbb{P}(\theta) = 0.99\times 0.1 + 0.1 \times 0.9 = 0.189 $$

$$ \mathbb{P}(\theta \,\vert\, \mathcal{D}) = 0.1 \times \frac{0.99}{0.189} \approx 0.524 $$

위와 같이, 오탐률을 10%로 높인 결과 그 정확도는 52.4% 로 크게 감소하였습니다.

<br>

## 🤩 조건부 확률의 시각화

조건부 확률의 결과와 오류에 대한 설명을 하기 위해 Confusion Matrix 라는 표를 가져오겠습니다. Confusion Matrix는 오류를 사용한 평가 지표들에 많이 사용되기 때문에 익숙해지는 것도 좋습니다.😊

![image](https://user-images.githubusercontent.com/91870042/178907711-e50fb874-4502-456f-935d-a8814b73664b.png){: .align-center width="70%"}

위의 Confusion Matrix를 방금 같이 풀어본 예제에 대입하면 아래와 같습니다.

- True Positive: 양성 판정일 때, 실제로 감염된 경우
- True Negative: 음성 판정일 때, 실제로 감염되지 않은 경우
- False Positive: 양성 판정일 때, 감염되지 않은 경우 (1종 오류)
- False Negative: 음성 판정일 때, 실제로 감염된 경우 (2종 오류)

우리는 위의 분류에서 False Positive를 `1종 오류`, False Negative를 `2종 오류`라고 부릅니다. 예측의 정확도를 높이기 위해서는 1종 오류 또는 2종 오류를 줄여야 하는데, 어떤 것에 중점을 두고 오류를 줄일지 생각해야 합니다. 주로 문제의 유형에 따라서 집중할 오류를 선택하게 됩니다.

예를 들어, 어떤 병원에서 암에 대한 판정을 한다고 합시다. 1종 오류는 암이 있다고 판정했는데, 실제로 없는 경우이고, 2종 오류는 암이 없다고 판정했는데 실제로 있는 경우를 말합니다. 이 경우, 2종 오류에 대한 위험부담이 더 크므로 2종 오류를 줄여야 합니다

---

이번에는 Confusion Matrix에서 추가로 알아볼 수 있는 Precision, Recall, Accuracy 에 대해서 알아보겠습니다.

![image](https://user-images.githubusercontent.com/91870042/178911586-db9d0f69-49bd-4266-a5fc-76d349afa0e7.png){: .align-center width="70%"}

각 지표는 주로 어떤 AI 모델의 성능을 평가할 때 사용됩니다. 문제에서 최적화하고자 하는 평가 지표에 맞게 모델을 학습하게 되는데 예로, Precision 값을 높이고 싶다면 False Positive (FP)를 낮추어 1에 가깝게 만들게 됩니다.

<br>

## 😀 베이즈 정리를 통한 정보 갱신

베이즈 정리를 통하여 새로운 데이터가 들어왔을 때, 앞서 계산한 **사후 확률을 사전 확률로 다시 사용**하여 갱신된 사후 확률을 계산할 수 있습니다. 이 과정은 기계학습에서 데이터를 새로 관측할 때마다 사후 확률을 계산하여 어떠한 모델의 정확도를 높이는 것을 가능하게 합니다.

위에서 살펴본 `COVID-19`의 예시에서 베이즈 정리를 사용한 정보의 갱신을 통해, COVID-19 판정을 받은 사람이 2번째 검진에서도 양성이 나올 확률을 계산해 봅시다!

사후 확률로 계산된 `52.4%` 의 확률을 다시 사전 확률 $\mathbb{P}(\theta)$ 로 계산하면 다음과 같은 식이 나오게 됩니다.

$$
\begin{aligned}
\mathbb{P}(\mathcal{D}^{*}) &= \mathbb{P}(\mathcal{D}^{*} \,\vert\, \theta) \times \mathbb{P}(\theta) + \mathbb{P}(\mathcal{D}^{*} \,\vert\, \neg \theta) \times \mathbb{P}(\neg \theta)\\
&= 0.99 \times 0.524 + 0.1 \times 0.476 \approx 0.566
\end{aligned}
$$

$$
\mathbb{P}(\theta \,\vert\, \mathcal{D}^*) = 0.524 \times \frac{0.99}{0.566} \approx 0.917
$$

- $\mathcal{D}^*$ : 새롭게 갱신된 Evidence

위 계산 과정은 감염된 사람이 2번째 검사에서도 양성이 나올 확률이 `91.7%` 임을 이야기하며 3번째 검사에서도 양성이 나올 확률을 계산하면 확률은 `99.1%` 까지 올라갑니다.

<br>

## 😁 조건부 확률과 인과관계

조건부확률과 베이즈 정리를 통해 여러 유용한 통계적 해석을 할 수 있지만, 함부로 인과관계를 추론하는 것은 위험합니다. 또한 데이터가 많아져도 조건부 확률만을 가지고 인과관계를 추론하는 것은 불가능합니다.

인과관계를 사용했을 때의 장점은 테스트 때의 예측 정확도를 높이는 것은 힘들지만, 여러 데이터에 대해서도 어느 정도의 정확도를 보장하는 강건한 예측 모형을 만들 수 있습니다.

![image](https://user-images.githubusercontent.com/91870042/145147335-c00c7624-a199-4bbb-a398-35c0163ec8d3.png){: .align-center width="80%"}

그렇다면 어떻게 인과관계를 파악해 내야 할까요? 인과관계를 파악하기 위해 `중첩 요인`을 제거해야 합니다. 중첩 요인은 어떠한 두 사건에 영향을 주는 공통된 사건을 말합니다.

![image](https://user-images.githubusercontent.com/91870042/178915635-ad463ce5-dd0b-474b-a525-4b8511f17c6e.png){: .align-center width="35%"}

위의 그림에서 두 사건 $A$ 와 $B$ 사이의 인과관계를 알고 싶다고 합시다. 그 사건은 각각 **키**와 **지능**이라고 하고, 이 두 속성 사이의 인과 관계를 파악하면, 조건부확률에서는 둘 사이의 인과관계가 있다고 판단할 것입니다. 키가 증가하면 지능이 올라가는 모습을 띄기 때문입니다.

하지만, 실제로 키와 지능은 관련이 없습니다. 키가 크면 지능이 높고, 낮으면 지능이 낮다? 이런 규칙은 없습니다. 하지만 인과관계가 있음을 보이는 것은 중첩 요인인 **나이**라는 속성을 제거하지 않았기 때문입니다. 그렇기 때문에 인과관계를 알아낼 때는 반드시 중첩 요인을 제거해야 합니다.


### 심슨의 역설

인과관계에 대한 또 다른 유명한 예시인 심슨의 역설을 살펴보겠습니다. 어떤 병원에는 신장결석이 있는 환자들이 있고 이들에게 치료법에 따른 완치율을 측정해 보았습니다. 그 결과는 아래와 같았습니다.

![image](https://user-images.githubusercontent.com/91870042/178918082-0201267a-f7c1-4334-aead-bde817cad092.png){: .align-center width="80%"}

근데 결과가 좀 이상합니다. 신장 결석의 크기별 환자에게는 치료법 A 가 모두 효과적이었는데, 전체를 두고 보니 치료법 B의 효과가 더 좋다고 나옵니다. 이 결과 역시 중첩 요인을 제거하지 않았기 때문에 발생하였습니다. 그렇다면 이 중첩 요인을 제거하여 확률을 다시 계산해 봅시다.

$$
\begin{aligned}
\mathbb{P}^{\mathfrak{C}_a}(R=1) &= \sum_{z \in \{0, 1\}}\mathbb{P}^{\mathfrak{C}}(R=1\,\vert\, T=a, Z=z)\;\mathbb{P}^{\mathfrak{C}}(Z=z)\\
&= \frac{81}{87} \times \frac{(87 + 270)}{700} + \frac{192}{263} \times \frac{(263+80)}{700} \approx 0.8325
\end{aligned}
$$

- $R$ : 완치 여부 (1: 완치, 0: 미완치)
- $Z$ : 신장 결석 크기 (1: 큰 결석, 0: 작은 결석)
- $T$ : 치료법 (a: 치료법A, b: 치료법B)
- $\mathbb{P}^{\mathfrak{C}_a}(R=1)$ : 치료법A를 선택하여 완치를 할 확률

위에서 치료법 A 에 대해 중첩 요인을 제거하고 계산한 결과 모두에게 치료법 A가 효과적일 확률은 78%가 아닌 83%임을 알 수 있습니다. 이번에는 반대로 치료법 B에 대해 중첩 요인을 제거하고 계산해 보겠습니다.


$$
\begin{aligned}
\mathbb{P}^{\mathfrak{C}_b}(R=1) &= \sum_{z \in \{0, 1\}}\mathbb{P}^{\mathfrak{C}}(R=1\,\vert\, T=b, Z=z)\;\mathbb{P}^{\mathfrak{C}}(Z=z)\\
&= \frac{234}{270} \times \frac{(87 + 270)}{700} + \frac{55}{80} \times \frac{(263+80)}{700} \approx 0.7789
\end{aligned}
$$

이렇게 중첩 효과를 제거하고 계산을 했을 때, 치료법 A는 78%에서 `83%` 로, 치료법 B는 83%에서 `77%` 로 나타나는 것을 볼 수 있습니다. 따라서 모두에 대한 결과도 치료법 A가 뛰어남을 알 수 있습니다. 

<br>

이렇게 해서 확률, 통계, 조건부확률 그리고 베이즈 정리까지 3개의 포스팅에 걸쳐서 살펴보았습니다. 다음은 실제 딥러닝의 구조인 CNN과 RNN에 대해서 알아보겠습니다!

<br>

# References

[🎨 헤더 이미지 소스](https://unsplash.com/photos/lzxJTsmnTlQ)

[📘 위키백과: 심슨의 역설](https://ko.wikipedia.org/wiki/%EC%8B%AC%EC%8A%A8%EC%9D%98_%EC%97%AD%EC%84%A4)
