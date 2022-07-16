---
title: "경사하강법 PART2 : 경사하강법 심화와 확률적 경사하강법"
excerpt: "일반적인 경사하강법의 단점을 보완하는 확률적 경사하강법(SGD, Stochastic Gradient Descent)에 대한 소개"

categories:
  - aimath
tags:
  - [AI, BoostCamp, Python, Math]
toc: true
toc_sticky: true
 
date: 2022-01-18 13:00:00
last_modified_at: 2022-06-27 13:00:00
---

![image](https://user-images.githubusercontent.com/91870042/175859550-905acdf2-0e05-409a-8537-adc07946f849.png){: .align-center width="100%"}

저번 행렬 포스팅에서는 무어-펜로즈 역행렬을 통해서 선형회귀 계수를 도출했었습니다. 하지만 이번에는 선형모델이 아닌 경우에도 일반화하여 쓸 수 있는 경사하강법을 알아볼 예정입니다. 저번 문제인 선형회귀계수의 도출에서 경사하강법을 적용시켜보고, 경사하강법의 한계가 무엇인지 알아볼 것입니다. 그리고 이를 해결한 확률적 경사하강법의 개념에 대해서 소개합니다!

<br>

# 경사하강법 : 선형회귀계수

![image](https://user-images.githubusercontent.com/91870042/175783640-b59da61b-b7bc-4a57-9e75-5bbfcaab2976.png){: .align-center width="70%"}

먼저 저번 선형회귀 문제를 가져와봤습니다. 저희가 구하고자 하는 것은 빨간 점들을 잘 표현하는 하나의 초록색 직선을 찾는 것입니다. 이때, 그 직선을 저희는 다음과 같이 표현하였습니다.

$$ \mathbf{y} \approx \mathbf{X\beta} $$

- $\mathbf{y}$ : 빨간 점들의 $y$ 절편 값
- $\mathbf{X}$ : 빨간 점들의 $x$ 절편 값
- $\mathbf{\beta}$ : 선형회귀 계수

지금은 2차원 평면상에서 선형회귀 문제이기 때문에 $\mathbf{X}$는 $x$ 절편 값을 의미하였습니다. 하지만, 실제로 딥러닝은 훨씬 높은 차원에서 점을 표현해야 합니다. 그러기 때문에 $\mathbf{X}$는 점을 표현하는 행렬이 되며, $\mathbf{y}$는 우리가 예측해야 하는 값이 됩니다.

다시 돌아와서, 저번에 선형회귀 문제의 목적함수를 L2-노름의 거리를 사용하여 아래와 같이 정의했고, 아래 식을 최소화하도록 만드는 $\beta$를 찾는 것이 문제였습니다. 현재는 L2-노름을 그대로 사용하였지만, 이 **값을 제곱한 값으로 목적함수를 설정해도 좋습니다**.

$$ \| \mathbf{y} - \mathbf{X}\beta \|_2 $$

이제 이 목적함수를 최소화하기 위해서 경사하강법을 사용할 것이며, 그레디언트 벡터를 생성하면 아래와 같이 나타낼 수 있습니다. 여기서부터 복잡한 수식이 등장하지만, 하나씩 따라가는 것을 추천드립니다!! 😄

$$ \nabla_\beta \|\mathbf{y} - \mathbf{X}\beta \|_2 = (\partial_{\beta_1} \|\mathbf{y} - \mathbf{X}\beta \|_2, \dots, \partial_{\beta_d}\|\mathbf{y} - \mathbf{X}\beta \|_2)$$

식을 풀어서 쓰면 위와 같지만 이제 한 단계 더 들어가야 합니다. 바로 우측에 미분 값을 더 풀어서 작성해 보겠습니다. 모든 차원에 대해서 미분을 하면 너무 지저분해져서 $k$ 차원에 대해 미분을 한다고 가정하겠습니다!

$$ \partial_{\beta_k} \| \mathbf{y} - \mathbf{X} \beta \|_2 = \partial_{\beta_k}\left\{ \frac{1}{n} \sum_{i=1}^{n}\left ( y_i-\sum_{j=1}^{d}X_{ij}\beta_j \right )^2\right\}^\frac{1}{2} $$

여기서 L2-노름을 전개한 수식에서 이상한 부분이 하나 있습니다. 기존까지는 L2-노름에 대해 정의할 때, 각 원소를 제곱한 값을 더하여 제곱근을 씌워주었는데 여기서는 $n$으로 나누어준 것을 볼 수 있습니다. 그 이유는 **총 $n$ 개에 데이터에 대해 거리를 구하는 과정이기 때문에, 그 평균값을 사용**하고자 했기 때문입니다. 이어서 우측 항을 $\beta_k$ 에 대해서 미분을 진행해 보겠습니다.

✅ 식이 복잡하기 때문에 치환을 한 번 하고 편미분을 진행합니다. 여기서 $F^{\frac{1}{2}}$ 가 L2-노름과 동일하다는 것을 기억해둡시다.

$$ F = \frac{1}{n} \sum_{i=1}^{n}\left ( y_i-\sum_{j=1}^{d}X_{ij}\beta_j \right )^2 $$

$$ \partial_{\beta_k} \|\mathbf{y} - \mathbf{X}\beta\|_2 =\partial_{\beta_k} F^{\frac{1}{2}} $$

✅ 치환한 식에 대해서 편미분을 진행할 때, 먼저 $F$에 대한 편미분을 하고, 다시 이 $F$를 $\beta_k$에 대해 편미분합니다.

$$
\begin{aligned}
\partial_{\beta_k} F^{\frac{1}{2}} &= \frac{\partial_{\beta_k} F^{\frac{1}{2}}}{\partial_{\beta_k} F}\frac{\partial_{\beta_k} F}{1}\\&=\frac{1}{2F^{\frac{1}{2}}}\times \partial_{\beta_k}F
\end{aligned}
$$

✅ 위의 식에서 먼저 $\partial_{\beta_k}F$ 를 계산해봅시다. 

$$
\begin{aligned}
\partial_{\beta_k}F &= \partial_{\beta_k} \left ( \frac{1}{n}\sum_{i=1}^{n}\left ( y_i-\sum_{j=1}^{d}X_{ij}\beta_j \right )^2 \right )\\
&= \frac{1}{n} \sum_{i=1}^{n} \partial_{\beta_k} \left ( y_i-\sum_{j=1}^{d}X_{ij}\beta_j \right )^2\\
&= \frac{1}{n} \sum_{i=1}^{n} \partial_{\beta_k} \left ( y_i-X_{ik}\beta_k \right )^2\\
&=\frac{1}{n}\sum_{i=1}^{n} \left ( X_{ik}\beta_k-y_i \right ) \times 2X_{ik}
\end{aligned}
$$

✅ 다시 원래의 자리에 대입한 결과입니다.

$$
\begin{aligned}
\frac{1}{2F^{\frac{1}{2}}}\times\frac{1}{n}\sum_{i=1}^{n} \left ( X_{ik}\beta_k-y_i \right ) \times 2X_{ik}
\end{aligned}
$$

✅ 아까 말했듯이, $F^{\frac{1}{2}}$ 은 사실 L2-노름과 동일합니다.

$$
\begin{aligned}
\frac{1}{n\|\mathbf{y} - \mathbf{X}\beta \|_2}\times\sum_{i=1}^{n} \left ( X_{ik}\beta_k-y_i \right ) X_{ik}
\end{aligned}
$$

마지막으로 시그마 항만 풀어서 작성하겠습니다. 시그마 내에 첫 번째 $X$ 와 $\beta$ 가 곱해진 것은 $X$의 전치행렬과 $\beta$의 내적 연산과 동일하며, 뒤에 나오는 $y_i$에 대해서는 모두 더해주었기 때문에 -$\mathbf{y}$와 같습니다. 이를 모두 정리해서 간단하게 나타내면 아래와 같습니다.

$$
-\frac{\mathbf{X}^\intercal_{\cdot k}(\mathbf{y} - \mathbf{X}\beta)}{n\|\mathbf{y} - \mathbf{X}\beta \|_2}
$$

그렇다면, 구하고자 했던 gradient vector는 다음과 같이 나타낼 수 있습니다!

$$ 
\begin{aligned}
\nabla_\beta \|\mathbf{y} - \mathbf{X}\beta \|_2 
&= (\partial_{\beta_1} \|\mathbf{y} - \mathbf{X}\beta \|_2, \dots, \partial_{\beta_d}\|\mathbf{y} - \mathbf{X}\beta \|_2)\\
&= \left( -\frac{\mathbf{X}^\intercal_{\cdot 1}(\mathbf{y} - \mathbf{X}\beta)}{n\|\mathbf{y} - \mathbf{X}\beta \|_2}, \dots, -\frac{\mathbf{X}^\intercal_{\cdot d}(\mathbf{y} - \mathbf{X}\beta)}{n\|\mathbf{y} - \mathbf{X}\beta \|_2}\right)\\
&= -\frac{\mathbf{X}^\intercal(\mathbf{y} - \mathbf{X}\beta)}{n\|\mathbf{y} - \mathbf{X}\beta \|_2}
\end{aligned}
$$

이제 위에서 구한 gradient vector를 이용하여 손실 함수를 최소화하도록 $\beta$ 값을 업데이트하는 과정을 거치면 됩니다. 그 알고리즘은 다음과 같이 적을 수 있습니다.

$$
\beta^{(t+1)} \leftarrow  \beta^{(t)}-\lambda \nabla_\beta\|\mathbf{y}-\mathbf{X}\beta^{(t)}\|_2
$$

- $\beta^{(t)}$ : $t$ 번째 단계에서의 $\beta$ 값
- $\lambda$ : 학습률 (learning rate)

gradient vector의 값이 음수가 나왔기 때문에, 경사하강법의 과정에서 음수와 만나 덧셈 연산으로 바뀌게 됩니다. 아래의 식에서 $\beta^{(t)}$에 미분값이 더해지는 것을 확인할 수 있습니다.

$$ 
\beta^{(t+1)} \leftarrow  \beta^{(t)} + \frac{\mathbf{X}^\intercal(\mathbf{y} - \mathbf{X}\beta)}{n \|\mathbf{y} - \mathbf{X} \beta \|_2}
$$

지금까지 L2-노름을 목적 함수로 정의하고 경사하강법에 적용시킨 결과를 살펴보았습니다. 앞서서 L2-노름의 제곱을 목적 함수로 사용해도 되었는데, 동일한 연산과정을 거쳐 나온 수식은 아래와 같이 간단하게 나올 수 있습니다.

$$
\beta^{(t+1)} \leftarrow  \beta^{(t)} + \frac{2\lambda}{n} \mathbf{X}^\intercal \left(\mathbf{y}-\mathbf{X}\beta^{(t)}\right)
$$

<br>

## 🎈 경사하강법 알고리즘

지금까지는 아주 작은 값인 `eps(epsilon)` $\epsilon$ 을 이용해 학습을 진행시켰는데, 이번에는 특정 횟수만큼만 학습을 진행한 코드입니다. 물론 `eps` 값을 사용해도 되지만, 오늘날에는 다른 파라미터들을 조절하고 학습 횟수를 설정하여 학습하고 있습니다. 마찬가지로 학습 횟수를 적절히 설정해야 하는 것에 주의해야 합니다. 횟수가 너무 적으면 학습을 제대로 하지 못하는 문제점이 발생합니다.

```py
for step in range(STEP):
    error = y - X @ beta
    grad = -transpose(X) @ error
    beta = beta - learning_rate * grad
```

<br>

## 🎆 경사하강법의 한계

경사하강법은 이론적으로 미분이 가능하고, 볼록한 함수에서 **적절한 학습률(Learning rate)과 학습 횟수**를 선택했을 때 극솟값에 수렴함이 보장되어 있습니다. 이전에 살펴본, 선형회귀의 L2-노름의 손실 함수는 회귀계수인 $\beta$에 대해서 볼록함수이기 때문에 알고리즘을 충분히 많이 돌리게 되면 수렴함이 보장이 되었습니다. 하지만 단순히 볼록한 상황이 아닌 아래와 같은 상황을 살펴봅시다.

![image](https://user-images.githubusercontent.com/91870042/175888273-a69c3258-5e21-4adb-a43d-50f42b634718.png){: .align-center width="60%"}

$x^t$ 지점에서 학습을 시작하여 5번의 step을 지나 $x^{t+5}$ 지점에 도달하였다고 합시다. $x^{t+5}$ 지점에서의 기울기는 0이므로, 더 이상 학습을 진행하지 않고 종료됩니다. 하지만 진짜 구하고 싶은 최솟값은 $x'$ 지점입니다. 바로 이 지역적인 최솟값 (local minimum)에 갇히는 문제가 발생할 수 있어서 나온 새로운 경사하강 기법은 `확률적 경사하강법(Stochastic Gradient Descent, SGD)`입니다.

<br>

# 확률적 경사하강법

확률적 경사하강법은 위의 경사하강법의 단점을 보완한 방법입니다. 가장 간단하게 개념을 설명하자면 100개의 데이터 셋에서 일부를 랜덤으로 추출하여 학습시키는 방법이라고 할 수 있습니다.

이렇게 임의로 1개 또는 일부를 활용하여 값을 업데이트 해나가는 것이 전체 데이터를 사용하여 값을 업데이트하는 것과 기댓값이 유사하다고 확률적으로 보장됩니다. SGD는 데이터 1개를 사용하여 값을 업데이트 해내가는 것인데, 사실 데이터를 1개로 계속 학습을 시키면 수렴하는 데 오랜 시간이 걸리게 되어 비효율적이게 됩니다. 그렇기에 한 번에 일부의 데이터를 추출하여 사용하는 `미니 배치(mini batch)`를 사용하여 SGD를 하며 이를 mini-batch SGD라고 합니다. 경사하강법 수식을 살펴봅시다.

$$
\beta^{(t+1)} \leftarrow  \beta^{(t)} + \frac{2\lambda}{n} \mathbf{X}^\intercal \left(\mathbf{y}-\mathbf{X}\beta^{(t)}\right)
$$

$$
\beta^{(t+1)} \leftarrow  \beta^{(t)} + \frac{2\lambda}{b} \mathbf{X}^\intercal_{(b)} \left(\mathbf{y}_{(b)}-\mathbf{X}_{(b)} \beta^{(t)}\right)
$$

확률적 경사하강법의 장점을 정리하면 다음과 같습니다.

- 모든 데이터를 사용하지 않기 때문에, 미분 과정 연산량이 $b/n$ 만큼 줄어든다.
- 연산량이 줄어 학습시간도 줄게 되지만 기댓값이 동일하여 빠른 시간 내에 원하는 결과를 얻어내는 것이 가능하다.

<br>

## 🎇 최적화 과정

확률적 경사하강법에 대해서 조금 더 파헤쳐 봅시다. 기존의 경사하강법의 문제인 local minimum 문제를 해결하기 위해 나온 것이 확률적 경사하강법이었습니다. 사실 지금까지 말한 것은 경사하강법의 연산이 빠르다는 장점밖에 없었습니다. 그렇다면 확률적 경사하강법이 어떻게 local minimum을 탈출할 수 있었을까요?

확률적 경사하강법은 모든 데이터를 사용하지 않고, 일부만을 사용했습니다. **그렇기 때문에 매번 최적화 시키고자 하는 목적함수의 그래프 모양이 달라지게 됩니다**. 바로 이 성질 덕분에 local minimum에서 탈출하는 것이 가능합니다. local minimum에 빠지더라도, 선택된 미니 배치에서는 극솟값이 아닌 단순히 내려가는 지점일 수도 있습니다. 이를 한 번 그림을 통해서 나타내봅시다!

![image](https://user-images.githubusercontent.com/91870042/175893068-4acf1b31-c227-4c8b-aefe-3740f55fae2b.png){: .align-center width="60%"}

- $f(x)$ : 모든 데이터를 사용하였을 때의 목적함수
- $f_\theta(x)$ : 일부 데이터를 사용하였을 때의 목적함수

이해를 돕기 위한 조금 극단적인 예시입니다. 모든 데이터를 사용한 초록색 함수 위에서는 미분 값이 0이 되어 더 이상 업데이트가 불가능하지만, 일부를 사용한 SGD 목적함수 위에서는 계속 하강할 수 있는 상태가 됩니다. 이 성질이 지역적 극솟값이 아닌, 전체적인 극솟값 (global minimum)에 도달할 수 있게 해주는 것입니다.

경사하강법과 확률적 경사하강법이 각각 극솟값에 어떻게 도달하는지 그래프를 통해 알아봅시다. 일반적인 경사하강법은 기울기를 타고 내려가 부드러운 모습을 보여주는 반면, 확률적 경사하강법은 매번 목적함수가 바뀌기 때문에 경로가 불안정한 것을 알 수 있습니다. 하지만, 결과적으로 보았을 때 두 방법 모두가 유사한 지점에 도달하게 됩니다.

![image](https://user-images.githubusercontent.com/91870042/149974013-c1322f91-9b56-40c6-85b1-da157fa1e2a1.png){: .align-center width="60%"}

아래 그래프는 확률적 경사하강법과 일반 경사하강법의 학습 속도 차이를 보여줍니다. 파란 실선이 일반적인 경사하강법을 나타내며, 점선이 확률적 경사하강법을 나타냅니다. SGD는 하나의 값에 대해서만 보고 업데이트를 하기 때문에 비교적 오랜 시간이 걸리지만, mini-batch SGD는 적절한 배치 사이즈를 선택하였을 때 빠른 속도로 극솟값에 도달하는 것을 알 수 있습니다.

![image](https://user-images.githubusercontent.com/91870042/149974460-060dce13-fd2a-4514-915c-25fed14231ae.png){: .align-center width="40%"}

확률적 경사하강법은 하드웨어 입장에서도 이점이 있습니다. 어떤 이미지 1,000,000 장을 입력 데이터로 사용해 학습을 한다고 합시다. 이미지는 256 x 256의 컬러 사진이라고 한다면, 모든 이미지를 표현하기 위해서는 256 x 256 x 3(color channel) x 1,000,000 만큼의 공간이 필요합니다.

모든 데이터에 대해서 미분을 계산하기에는 하드웨어 입장에서도 버거운 일이기 때문에, mini-batch SGD는 일부만 올려서 연산량을 감소시켜주기 때문에 원활하게 수행하고 병렬처리도 진행할 수 있습니다.

<br>

# References

[🎨 이미지 소스](https://unsplash.com/photos/ICdZeEGz-0I)

[📘 SGD Wikipedia](https://en.wikipedia.org/wiki/Stochastic_gradient_descent)

[📘 회귀분석 시 상수에 대한 해석](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=jiehyunkim&logNo=199212512)

[📘 딥러닝 학습 전략인 경사 하강법의 문제점과 고급 해결방법](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=2011topcit&logNo=220563609607)