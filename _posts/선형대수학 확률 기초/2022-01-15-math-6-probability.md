---
title: "딥러닝에서 사용되는 확률분포, 조건부확률 기댓값과 몬테카를로 샘플링"
excerpt: "확률분포, 조건부확률, 기댓값의 개념과 몬테카를로 샘플링 방법에 대한 설명"

categories:
  - aimath
tags:
  - [AI, BoostCamp, Math]
toc: true
toc_sticky: true
 
date: 2022-01-20 00:00:00
last_modified_at: 2022-07-08
---

![image](https://user-images.githubusercontent.com/91870042/177921003-cf536a69-6c2f-4b53-bd9c-616fc8706bd3.png){: .align-center width="100%"}


이번 포스팅에서는 딥러닝과 확률과의 관계에 대해서 학습합니다! 왜 주 연산이 행렬곱인 딥러닝에 확률이나 통계가 사용되고 있는지 이번 확률 포스팅과 다음 통계 포스팅을 통해서 알아보고자 합니다. 현재 확률론 포스팅에서는 확률 변수와 확률 분포의 개념과 분포에 따른 이산확률분포, 연속확률분포 그리고 조건부 확률에 대해서 소개하겠습니다. 마지막으로 딥러닝에서 근사를 하기 위해서 많이 사용되는 몬테카를로 샘플링이 무엇인지도 살펴보겠습니다.

<br>

# 확률, 통계가 필요한 이유

딥러닝을 포함하여 기계학습의 전반적인 이론은 확률론을 기반하고 있습니다. 기계학습에서 사용되는 손실함수들의 작동원리도 데이터 공간을 통계적으로 해석해서 유도하게 되며 유도된 손실함수를 가지고 기계학습의 딥러닝 모형을 학습시키게 됩니다. 따라서 딥러닝을 잘 이해하기 위해서는 확률론의 이해가 필요합니다.

- 회귀 분석(regression analysis) : 이전에 살펴본 회귀분석의 손실함수로 `L2-노름`을 사용했었는데, 이는 **예측오차의 분산을 가장 최소화하는 방향**으로 학습을 유도합니다. 

- 분류 문제(classification problem) : 분류 문제에서 사용되는 `교차 엔트로피(cross-entropy)`는 모델 예측의 **불확실성을 최소화하는 방향**으로 학습을 하도록 유도합니다. 교차 엔트로피에 대한 더 자세한 설명은 [DL Basic : 크로스 엔트로피](https://killerwhale0917.github.io/dlbasic/DLBasic-4-loss-function/#-%ED%81%AC%EB%A1%9C%EC%8A%A4-%EC%97%94%ED%8A%B8%EB%A1%9C%ED%94%BC) 에서 확인할 수 있습니다.

각각 `분산`과 `불확실성`을 최소화하는 방향으로 학습을 한다고 했는데, 이 분산과 불확실성을 계산하는 방법도 알아야 합니다. 각각을 측정하는 방법은 통계학에서 제공하고 있기 때문에 위와 같이 기계학습을 이해하기 위해서는 통계학과 확률론에 대한 이해가 필요합니다.

<br>

## ⛄ 확률변수

Wikipedia에서 정의하는 확률변수는 **시행의 결과에 따라 값이 결정되는 변수**입니다. 그리고 이 확률변수가 특정 값이나 범위에 존재할 확률을 다음과 같이 표기합니다.

$$ \mathbb{P}(X=a) \qquad \mathbb{P}(a \le X \le b) $$

확률변수 $X$ 가 가질 수 있는 값의 범위가 이산적(countable)인지 연속적(uncountable)인지에 따라서 `이산확률변수`와 `연속확률변수`로 구분이 가능합니다. 확률변수에 대한 더 자세한 내용은 아래 확률분포를 다루고 나서 다시 진행하겠습니다.

<br>

## ⛄ 확률분포

Wikipedia에서 확률분포를 다음과 같이 정의합니다.

> 확률분포(probability distribution)는 확률 변수가 특정한 값을 가질 확률을 나타내는 함수를 의미한다. 예를 들어, 주사위를 던졌을 때 나오는 눈에 대한 확률변수가 있을 때, 그 변수의 확률분포는 이산균등분포가 된다. 확률 분포는 확률 변수가 어떤 종류의 값을 가지는가에 따라서 크게 이산확률분포와 연속확률분포 중 하나에 속하며, 둘 중 어디에도 속하지 않는 경우도 존재한다. - wikipedia

지금부터 데이터가 존재하는 데이터 공간과 이 데이터 공간에서 데이터를 추출하는 확률분포를 아래와 같이 표현하겠습니다.

- $X \times Y$ : 데이터 공간
- $\mathcal{D}$ : 데이터를 추출하는 확률분포
- $(\mathbf{x}, y)$ : 데이터. 데이터는 확률분포에서 추출하였기 때문에 $(\mathbf{x}, y) \sim \mathcal{D}$ 라고 표현할 수 있습니다.

지금 표현한 데이터 공간은 각 입력마다 라벨이 주어진 지도학습을 가정하고 있습니다. 만약 레이블이 없는 데이터 공간이라고 가정한다면 $Y$ 가 없는 $X$ 라고 표현하게 됩니다.

우리가 위에서 알고 싶은 것은 확률분포 $\mathcal{D}$ 입니다. 데이터 공간이 주어져있고 이를 잘 나타내는 확률분포를 알고 있다면 어떤 입력이 들어와도 그에 맞는 라벨을 출력으로 전달할 수 있습니다. 하지만 이 확률분포를 데이터로만 아는 것은 거의 불가능에 가깝습니다. 그렇기 때문에 기계학습을 사용해서 확률분포를 근사하도록 학습을 시키게 됩니다.

<br>

![image](https://user-images.githubusercontent.com/91870042/177926582-e2ca5d62-6af2-4885-8e24-e0e172509a99.png){: .align-center width="50%"}

다시 확률변수로 돌아와서, 확률변수는 이산형과 연속형이 있다고 했었습니다. 확률변수는 확률분포 $\mathcal{D}$ 가 이산형인지 연속형인지에 따라서 이산확률변수와 연속확률변수로 구분하게 됩니다. 

이때 주의할 것은 데이터가 실숫값에 위치해있다고 해서 연속형인 것은 아닙니다. 예를 들어, 단 2개의 데이터 1.1 과 1.2 가 있다고 하고 이 둘 중 하나를 선택하는 문제라면, 데이터가 실수이지만 이산형 확률분포를 가지게 됩니다.

아래에서는 이산확률변수와 연속확률변수에 대해서 더 자세하게 다뤄보겠습니다.

<br>

## 🔥 이산확률변수  

`이산확률변수`는 확률변수가 취할 수 있는 값이 유한하거나 또는 무한히 많더라도 하나씩 셀 수 있는 경우를 말합니다. 이산확률변수는 확률변수가 가질 수 있는 모든 경우의 수를 고려해 확률을 **더하여** 모델링을 진행합니다.

$$ \mathbb{P}(X\in A)=\sum_{\mathbf{x}\in A}\mathbb{P}(X=\mathbf{x}) $$

위의 수식에서 각 원소 $\mathbf{x}$ 에 대하여 특정 사건 $A$ 에 속해있는 확률을 구하고 싶을 때, $A$ 에 속한 확률변수 $\mathbf{x}$ 에 대한 확률을 더하여 계산하게 됩니다. 이 식을 **확률질량함수** 라고 부르며 확률변수 $X$ 가 $\mathbf{x}$ 를 가질 확률로 해석할 수 있습니다.

<br>

## 💧 연속확률변수  

`연속확률변수`는 확률변수가 연속적인 구간 내의 값을 취하는 경우를 말합니다. 그 예시로 높이, 무게, 온도, 거리, 수명 등의 측정 자료들이 존재합니다. 연속확률변수는 데이터 공간에 정의된 확률변수의 밀도 위에서 **적분**을 통해 모델링 합니다.

$$ \mathbb{P}(X\in A)= \int_{A}\mathbb{P}(\mathbf{x})\text{d}\mathbf{x} $$

적분을 하는 함수 $\mathbb{P}(\mathbf{x})$를 확률밀도함수라고 부르며, 밀도는 누적확률분포의 변화율을 모델링하며 **확률로 해석되어서는 안됩니다**.

<br>

## 🌊 결합분포

`결합분포(joint distribution)`는 2개의 확률 변수에 대해 함께 고려하는 확률분포를 말합니다. 이는 확률분포의 일종이기 때문에 결합확률분포라고도 합니다. 위에서 정의한 확률분포 $\mathcal{D}$ 에서 추출한 데이터 $(\mathbf{x}, y)$ 는 결합확률 분포를 따릅닌다. 한 번 아래의 그림을 통해 이해해 봅시다.

![image](https://user-images.githubusercontent.com/91870042/177928458-0b0b862a-479b-49c6-a549-89cd23a37147.png){: .align-center width="60%"}

위의 그림은 입력 $X$ 에 대해서 라벨(출력) 값 $Y$ 를 갖는 데이터 공간을 표현한 것입니다. 현재 이 데이터들만 보았을 때, 데이터가 존재하는 분포를 생각하면 연속확률분포로 생각이 들지만 칸막이를 하나 씌워서 이산확률분포 문제로 바꾸어보겠습니다.

![image](https://user-images.githubusercontent.com/91870042/177929153-48be657e-9527-4c9d-835f-96f4bbfeb76a.png){: .align-center width="60%"}

이제 각 칸에 속하는 입력에 따라 $Y$ 값의 등장 횟수를 확률로 표현할 수 있게 되었습니다. 예시로, 현재 존재하는 데이터의 수는 36개인데, 첫 번째 $X$ 칸에 데이터가 존재할 확률은 1/36로 나타낼 수 있는 것입니다. 이와 비슷한 `주변확률분포`에 대한 개념을 살펴보겠습니다.

### 주변확률분포

주변확률분포는 결합분포가 존재할 때, 하나의 확률변수에 대해서만 확률분포를 구하고 싶을 때 사용할 수 있습니다. 위의 데이터 예시에서 $X$ 에 대해서만 확률분포를 나타낸다고 했을 때, $Y$ 가 1인지 2인지는 상관하지 않고 분포를 그려보면 아래와 같습니다.

![image](https://user-images.githubusercontent.com/91870042/177931532-b1330939-7035-43f5-91e7-3e01f4f1b63f.png){: .align-center width="70%"}

위의 분포는 입력으로 주어지는 $\mathbf{x}$ 에 대한 주변확률분포로 $Y$ 에 대한 정보를 알려주지는 않습니다. 주변확률분포로 $Y$ 에 대해서 구하고 싶은 경우에는 반대로 $X$를 무시하고 확률을 계산하면 구할 수 있습니다. 다른 확률분포와 마찬가지로 주변확률분포는 이산형인지 연속형인지에 따라 급수나 적분을 통해서 값을 구할 수 있습니다.

$$
\mathbb{P}(\mathbf{x}) = \sum_y \mathbb{P}(\mathbf{x}, y) \qquad
\mathbb{P}(\mathbf{x}) = \int_\mathcal{Y} \mathbb{P}(\mathbf{x}, y) \text{d}y

$$

### 조건부확률분포

`조건부확률분포` $\mathbb{P}(x\,\vert\,y)$ 는 특정 클래스가 주어진 상황에서 데이터의 확률분포를 보여주며 데이터 공간에서 입력 $\mathbf{x}$ 와 출력 $y$ 사이의 관계를 모델링합니다. 다시 말해, 입력변수 $\mathbf{x}$ 에 대해서 정답이 $y$ 일 확률을 말합니다. 연속확률분포에서 주의할 것은 이것을 확률로 해석해서는 안 되고 밀도로 해석해야 합니다.

예를 들어, 만약 위의 분포에서 $Y=1$ 인 경우만 뽑아내면 $\mathbb{P}(X\,\vert\,Y=1)$ 이라 표현하며 다음 조건부확률분포를 얻어낼 수 있습니다. 조건부확률분포는 데이터 분석을 할 때 사용되며, 주어진 데이터 간의 관계를 보다 명확하게 모델링 할 수 있습니다.

![image](https://user-images.githubusercontent.com/91870042/177933211-26110fdf-98ca-443f-825b-bd6c5bd2a96d.png){: .align-center width="70%"}


<br>

# 조건부확률과 기계학습

저번 포스팅에서 softmax 함수의 사용은 출력 벡터를 확률로 해석을 하는데 사용된다고 하였습니다. 주로 로지스틱 회귀 문제에서 자주 사용되는데, 회귀 문제는 주로 연속확률변수의 밀도 함수를 사용하기 때문에 확률로 해석을 하기가 어렵습니다. 그렇기 때문에 로지스틱 회귀는 `조건부 기댓값` $\mathbb{E}\left[ y\,\vert\,\mathbf{x}\right]$ 을 사용합니다.

여기서 조건부 기댓값을 사용하는 이유는 식이 L2 노름을 최소화하는 함수와 식이 동일하기 때문입니다. 이를 통해 예측의 오차(분산)을 최소화하여 학습을 할 수 있습니다.

$$
\mathbb{E}_{y\sim \mathbb{P}(y\,\vert\,\mathbf{x})}\left[ y\,\vert\,\mathbf{x} \right] = \int_{\mathcal{Y}}y\;\mathbb{P}(y\,\vert\,\mathbf{x})\,\text{d}y
$$

분류문제에서 $\text{softmax}(\mathbf{W} \phi + \mathbf{b})$ 는 데이터 $\mathbf{x}$ 로 부터 추출된 특징패턴(잠재벡터) $\phi (\mathbf{x})$ 과 가중치 행렬 $\mathbf{W}$ 을 통해 조건부 확률 $P(y\,\vert\,\mathbf{x})$ 를 계산합니다. 이를 $\mathbb{P}(y\,\vert\,\phi (\mathbf{x}))$ 로 표현하기도 합니다.

<br>

## 🌂기댓값

기댓값은 데이터를 대표하는 통계량이며, 흔히 평균으로 이해되고 있습니다. 이 기댓값은 모델링하고자 하는 확률분포를 통해 다른 통계적 범함수(분산, 첨도, 공분산)를 계산하는데 사용이 될 수 있습니다.

$$
\mathbb{E}_{\mathbf{x} \sim \mathbb{P}(\mathbf{x})}[f(\mathbf{x})] = \sum_{\mathbf{x}\in \mathcal{X}}f(\mathbf{x})\mathbb{P}(\mathbf{x}) \qquad 
\mathbb{E}_{\mathbf{x} \sim \mathbb{P}(\mathbf{x})}[f(\mathbf{x})] = \int_{\mathcal{X}}f(\mathbf{x})\mathbb{P}(\mathbf{x})\text{d}\mathbf{x}
$$ 

다음은 기댓값을 통해 분산, 첨도, 공분산을 계산하는 식입니다. 각식을 위의 수식에서 $f(\mathbf{x})$ 에 대입하여 원하는 값을 얻어내는 것이 가능합니다.

$$
\begin{aligned}
  \mathbb{V}(\mathbf{x}) &= \mathbb{E}_{\mathbf{x} \sim \mathbb{P}(\mathbf{x})}\left[ (\mathbf{x} - \mathbb{E}\left[\mathbf{x}\right])^2 \right]\\\\

  \text{skewness}(\mathbf{x}) &= \mathbb{E}\left[ \left( \frac{\mathbf{x} - \mathbb{E}[\mathbf{x}]}{\sqrt{\mathbb{V}(\mathbf{x})}}\right) ^ 3\right]\\\\

  \text{Cov}(\mathbf{x}) &= \mathbb{E}_{\mathbf{x_1}, \mathbf{x_2} \sim \mathbb{P}(\mathbf{x_1, x_2})}\left[ \left( \mathbf{x_1} - \mathbb{E} \left[ \mathbf{x_1}\right]\right)\left( \mathbf{x_2} - \mathbb{E} \left[ \mathbf{x_2}\right]\right) \right]
  \end{aligned}
$$

<br>

# 몬테카를로 샘플링

기계학습의 많은 문제들은 확률분포를 명시적으로 모를 때가 대부분입니다. 확률분포를 모를 때 데이터를 이용하여 기댓값을 계산하려면 `몬테카를로 샘플링` 방법을 이용해야 합니다. 몬테카를로 샘플링은 반복되는 임의의 추출을 이용해서 어떤 함수의 값을 수리적으로 근사하는 것인데 1~100개의 수에서 임의로 3개씩 뽑아 평균을 구하고 이런 과정을 수없이 반복하면, 전체 평균과 근사해진다는 것입니다. 단, **샘플링은 독립적으로 이루어져야만 합니다**.

$$
\mathbb{E}_{\mathbf{x} \sim \mathbb{P}(\mathbf{x})}[f(\mathbf{x})] \approx \frac{1}{N}\sum_{i=1}^{N}f(\mathbf{x}^{(i)}), \quad \mathbf{x}^{(i)} \overset{\mathrm{i.i.d}}{\sim} \mathbb{P}(\mathbf{x})
$$

아래는 1~100 사이의 수를 임의로 10000번 뽑아 평균값을 구하는 파이썬 코드와 실행 결과입니다. 결과를 확인해 보면, 실제 평균값인 50과 근사한 값을 갖는 것을 알 수 있습니다. 따라서 확률분포를 몰라도 샘플링만 가능하다면 기댓값을 계산하는 것이 가능하기 때문에 많은 기계학습의 응용문제에서 몬테카를로 샘플링을 이용해 기댓값을 계산합니다.

```py
import numpy as np

arr = np.arange(0, 100)
avg_arr = np.mean(arr)

res = []
for _ in range(10000):
    sample = np.random.choice(arr, 5)
    res.append(np.mean(arr[sample]))
    
res = np.array(res)
print(avg_arr, np.mean(res))

>>> 49.5 49.35112
```
<br>

## 🌀 예제: 적분의 계산

다음 함수를 [-1, 1]의 구간에서 `몬테카를로 샘플링`을 이용해 적분 값을 계산해 보겠습니다. 

$$ \int_{-1}^{1}e^{-x^2}dx $$

구간 [-1, 1]의 길이는 2이므로, 적분 값을 2로 나누면 기댓값을 계산하는 것과 같습니다. 구간[-1, 1] 사이에서 데이터의 샘플링을 진행하게 되며 적분 값이 어떻게 근사하는지 수식으로 나타내면 다음과 같습니다.

$$ 
\frac{1}{2}\int_{-1}^{1}e^{-x^2}dx \approx \frac{1}{N}\sum_{i=1}^{N}f(x^{(i)}), \quad x^{(i)} \overset{\mathrm{i.i.d}}{\sim} U(-1, 1) 
$$

```py
import numpy as np

def mc_int(fun, low, high, sample_size = 100, repeat = 10):
  int_len = np.abs(high - low)
  stat = []
  for _ in range(repeat):
    x = np.random.uniform(low=low, high=high, size=sample_size)
    fun_x = fun(x)
    int_val = int_len * np.mean(fun_x)
    stat.append(int_val)
  return np.mean(stat), np.std(stat)

def f_x(x):
  return np.exp(-x**2)

print(mc_int(f_x, low=-1, high=1, sample_size=10000, repeat=100))

>>> (1.493875, 0.003913)
```

<br>

# References

[🎨 이미지 소스](https://unsplash.com/photos/X5UrOwSCYYI)

[🎨 이미지 소스 : 이산확률분포와 연속확률분포](https://excelsior-cjh.tistory.com/193)

[📘 로지스틱회귀 - wikipedia](https://ko.wikipedia.org/wiki/%EB%A1%9C%EC%A7%80%EC%8A%A4%ED%8B%B1_%ED%9A%8C%EA%B7%80)

[📘 결합분포 - wikipedia](https://ko.wikipedia.org/wiki/%EA%B2%B0%ED%95%A9%EB%B6%84%ED%8F%AC)

[📘 확률변수와 확률함수 - tistory](https://drhongdatanote.tistory.com/49)

[📘 확률변수 - wikipedia](https://ko.wikipedia.org/wiki/%ED%99%95%EB%A5%A0_%EB%B3%80%EC%88%98)

[📘 Cross entropy - wikipedia](https://ko.wikipedia.org/wiki/%EA%B5%90%EC%B0%A8_%EC%97%94%ED%8A%B8%EB%A1%9C%ED%94%BC)

[📘 [통계학] 08-2. 확률벡터(2) - 결합분포, 주변분포, 독립확률변수 - tistory](https://deep-learning-study.tistory.com/59)

[📘 [확률과 통계] 21. 연속확률변수의 결합확률분포 - naver blog](https://m.blog.naver.com/mykepzzang/220836634095)