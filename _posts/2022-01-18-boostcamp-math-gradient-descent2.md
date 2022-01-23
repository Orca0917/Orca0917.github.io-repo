---
title: "[AI Math] 경사하강법 (2)"
excerpt: "일반적인 경사하강법의 단점을 보완하는 확률적 경사하강법(SGD, Stochastic Gradient Descent)에 대한 소개"

categories:
  - aimath
tags:
  - [AI, Naver, BoostCamp, Python, Math]
toc: true
toc_sticky: true
 
date: 2022-01-18 13:00:00
last_modified_at: 2022-01-18 13:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# 경사하강법을 이용한 선형회귀 계수 도출
선형회귀의 목적함수(손실함수)는 L2노름을 이용하여 구할 수 있는데, 실제 값과 측정한 값의 거리를 이용해서 구한다.

$$ \| y - X\beta \|_2 $$

차이를 최소화하는 \\(\beta\\) 값을 찾기 위해 위의 손실함수의 특정 부분의 기울기 값인 `Gradient Descent vector`를 구해야 하는데 이를 구하기 위해서는 위의 손실함수를 \\(\beta\\) 에 대하여 미분해야한다.

$$\nabla_\beta \| y - X\beta \|_2 = (\partial_{\beta_1}\| y - X\beta \|_2, \dots, \partial_{\beta_d}\| y - X\beta \|_2)$$

위의 식을 말로 표현하자면, L2를 이용한 손실함수를 \\(\beta\\)에 대해서 다차원 미분을 한 `Gradient Descent vector`를 우변에 풀어서 쓴 것이다. 우변은 각각 손실함수를 각 차원인 \\(\beta_i\\)에 대해서 미분을 한 값을 담고 있다. 이제 어느 한 차원 \\(\beta_k\\)에 대해서 **편미분을 진행**해보자.

$$ \partial_{\beta_k}\|y-X\beta \|_2 = \partial_{\beta_k}\left\{ \frac{1}{n} \sum_{i=1}^{n}\left ( y_i-\sum_{j=1}^{d}X_{ij}\beta_j \right )^2\right\}^\frac{1}{2} $$

여기서 **\\(n \\)으로 나누어지는 이유**는 \\(n\\)개의 데이터를 가지고 계산되는 L2 노름이기 때문에 평균값을 구해주기 위해서이다. 위의 우변의 중괄호 내에 있는 식을 \\(\beta_k\\) 에 대해서 편미분을 진행하면 최종적으로 우리가 원하는 식을 구할 수 있다.

$$ -\frac{X^T_k(y-X\beta)}{n\|y-X\beta\|_2} $$

여기서 일반화하여 \\(k\\)를 지우고 생각한다면, 사실 **\\(X_\beta\\)를 계수 \\(\beta\\) 에 대해 미분한 결과인 전치행렬 \\(X^T\\) 만 곱해지는것과 동일**하다.

이제 위에서 구한 Gradient Descent vector 를 이용하여 손실함수를 최소화하는 \\(\beta\\) 값을 업데이트 하는 과정을 거치면 되는데 그 알고리즘은 다음과 같이 적을 수 있다.

$$ \beta^{(t+1)} \leftarrow  \beta^{(t)}-\lambda \nabla_\beta\|y-X\beta^{(t)}\|_2 $$

살펴보면, 현재 \\(\beta\\) 값에 대해서 미분을 진행하고, 그 값에 대해 \\(\lambda\\) (Learning rate) 만큼 곱해주어 뺄셈 연산을 해주었는데, 이는 경사하강법에서 배웠듯이 극소값을 향해서 값을 업데이트 하는 과정과 동일하다.

좀 더 나아가 이전에 구한식 까지 대입을 해보면 다음 식을 구하는 것이 가능하다.

$$ \beta^{(t+1)} \leftarrow  \beta^{(t)}+\frac{\lambda}{n}\frac{X_T(y-X\beta^{(t)})}{\|y-X\beta^{(t)}\|_2} $$

> 손실함수값을 제곱근 값을 사용하지 않고, 제곱 값을 사용하는 경우!

$$ \beta^{(t+1)} \leftarrow  \beta^{(t)}+\frac{2\lambda}{n}X_T(y-X\beta^{(t)}) $$

<br>

# 경사하강법을 이용한 선형회귀 알고리즘
지금까지는 아주 작은 `eps` 값을 이용해 학습을 진행시켰는데, 이번에는 특정 횟수만큼만 학습을 진행하였다. `eps` 값을 사용해도 되지만, 오늘날에는 경사하강법에서 학습 횟수로 사용한다. 하지만 학습횟수를 적절히 설정해야 하는것에 주의해야한다.
```py
for step in range(STEP):
    error = y - X @ beta
    grad = -transpose(X) @ error
    beta = beta - learning_rate * grad
```

## 예시
```py
X = np.array([[1, 1], [1, 2], [2, 2], [2, 3]])
y = np.dot(X, np.array([1, 2])) + 3

beta_gd = [10.1, 15.1, -6.5]
X_ = np.array([np.append(x, [1]) for x in X]) # intercept항을 추가
lr = 0.1

for t in range(5000):
    error = y - X_ @ beta_gd
    grad = - np.transpose(X_) @ error
    beta_gd = beta_gd - lr * grad

```

<br>

# 경사하강법의 한계
이론적으로는 미분이 가능하고, 볼록한 함수에서 **적절한 학습률(Learning rate)와 학습 횟수**를 선택했을 때 수렴이 보장되어 있다. 이전에 살펴본, 선형회귀의 L2노름의 손실함수는 회귀계수인 \\(\beta\\) 에 대해서 볼록함수이기 때문에 알고리즘을 충분히 많이 돌리게 되면 수렴함이 보장이 된다.

![image](https://user-images.githubusercontent.com/91870042/149970661-bbb15ad6-539e-4add-8cae-6ef86c148a3b.png){: .align-center}

하지만, 반대로 비선형 회귀 문제의 경우 손실함수가 볼록하지 않을 수 있기 때문에 수렴이 항상 보장되지는 않는다. 그렇기 때문에 이 문제를 해결할 다른 방법이 필요하다.

<br>

# 확률적 경사하강법
확률적 경사하강법(Stochastic Gradient Descent, SGD)는 위의 경사하강법의 단점을 보완한 방법이다. 가장 간단하게 개념을 설명하자면 100개의 데이터셋에서 일부를 랜덤으로 추출하여 학습시키는 방법이다. 

모든 데이터에 대해서 학습시키는 `경사하강법`과 달리, 확률적 경사하강법은 데이터를 임의로 선택할 때마다 그때의 손실함수 그래프는 달라질 수 있다. 그렇기 때문에 **국소적인 극소값**에서 이론상 벗어날 수 있게 된다. 

> 하지만, SGD가 모든 그래프에서 적용될 수 있는 만능은 아니다.

위에서 말한, 임의로 선택된 데이터를 `미니 배치`라고 부른다. 그렇기 때문에 `미니배치 확률적 경사하강법`이라 부르기도 한다. 이를 수식으로 표현하면 다음과 같다.

$$ \theta^{(t+1)} \leftarrow \theta^{(t)}- \widehat{\nabla_\theta L}(\theta^{(t)}) $$

아래는 미니배치 SGD의 기대값이 경사하강법의 결과와 근사한다는 것을 의미한다.

$$ \mathbb{E}[\widehat{\nabla_\theta L}] \approx \nabla_\theta L $$

추가로, 미니배치를 활용하면 일부를 가지고 parameter를 업데이트하기 때문에 연산 횟수를 줄여 더 효율적이다. 일반적으로 경사하강법에서는 \\(O(d^2n)\\)의 시간이 걸리는데, 그중 \\(b\\)개의 데이터를 추출하여 학습한다면, 그 시간은 \\(O(d^2b)\\)로 줄어든다. (b/n)만큼 감소.

## 경사하강법 vs. SGD
- SGD는 볼록이 아닌 손실함수에 대해서도 사용이 가능하기 때문에 경사하강법보다 머신러닝 학습에 있어서 더 효율적이다.
- 그래프의 차이  
SGD는 매번 그 그래프가 변화하기 때문에 극소값을 찾아가는데 있어 흔들리는 경향이 있다. 하지만, 적절한 미니배치의 크기와 학습단계를 설정한다면 경사하강법보다 빠른 시간내에 극소값에 근사하여 도달할 수 있다.

![image](https://user-images.githubusercontent.com/91870042/149974013-c1322f91-9b56-40c6-85b1-da157fa1e2a1.png){: .align-center}

실제로 아래 그래프를 확인하면, 적절하지 못한 배치크기를 설정하면 일반적인 경사하강법과 비슷한 시간이 소요된다.

![image](https://user-images.githubusercontent.com/91870042/149974460-060dce13-fd2a-4514-915c-25fed14231ae.png){: .align-center}

# References
[📘 SGD Wikipedia](https://en.wikipedia.org/wiki/Stochastic_gradient_descent)

[📘 회귀분석 시 상수에 대한 해석](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=jiehyunkim&logNo=199212512)

[📘 딥러닝 학습 전략인 경사 하강법의 문제점과 고급 해결방법](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=2011topcit&logNo=220563609607)