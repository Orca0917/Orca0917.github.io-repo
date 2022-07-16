---
title: "경사하강법 PART1 : 미분과 경사하강법의 기초 개념"
excerpt: "미분의 개념과 기울기를 통한 함수의 최적화 방법인 경사하강법에 대해 소개합니다. 또한 간단한 예제를 통해서 경사하강법의 알고리즘이 어떻게 구성되어 있는지도 함게 알아봅니다!"

categories:
  - aimath
tags:
  - [AI, BoostCamp, Python, Math]
toc: true
toc_sticky: true
 
date: 2022-01-18 12:00:00
last_modified_at: 2022-06-26
---

![image](https://user-images.githubusercontent.com/91870042/175805132-3156a925-8dc6-428e-83bc-0f0fe64db5d2.png){: .align-center width="100%"}

이번 포스팅에서는 딥러닝의 학습 방법인 경사하강법에 대한 기초개념에 대해 알아봅니다. 경사하강법은 현재 거의 모든 학습에서 사용이 되고 있기 때문에 반드시 알아둬야 하는 개념입니다. 그렇기 때문에 이번에는 경사하강법이 무엇이고, 어떻게 학습을 진행하는 것인지 이해하고, 그전에 먼저 알고 있어야 하는 미분의 개념에 대해서도 설명합니다. 이후에 여기서 배운 경사하강법은 손실 함수를 최적화시키는 곳에 사용이 됩니다. 다음 포스팅 경사하강법 2에서는 더 심화된 내용의 경사하강법을 다룰 예정입니다!

# 미분

`미분(differentiation)`은 변수의 움직임에 따른 함숫값의 변화를 측정하기 위한 도구로 최적화에서 제일 많이 사용하는 기법입니다. 고등학교에서 미분에 대한 개념을 배웠지만 이번에 다시 한번 미분이라는 것이 왜 기울기를 나타내는 것인지 이해해 보고자 합니다. 먼저 미분을 정의하는 수식을 살펴봅시다.

$$ f'(x) = \lim_{h \rightarrow 0} \frac{f(x+h)-f(x)}{h} $$

위의 수식에서 $\lim$ 뒤에 존재하는 분수는 $x$ 축의 값이 $h$ 만큼 이동하였을 때, 함숫값이 얼마나 이동했는지 알아내는 수식입니다. 바로 이 수식이 변화율 기울기를 나타내게 됩니다. 그렇게 $h$를 0에 최대한 가깝게 만들어주면 함수의 한 점에서의 기울기를 구할 수 있으며 이것을 변화율의 극한이라고 부릅니다. 한 번 예를 통해서 알아봅시다.

$$ 
\begin{aligned}
f(x) &= x^2 + 2x + 3 \\
f(x + h) &= x^2 + 2xh + h^2 + 2x + 2h + 3 \\
f(x + h) - f(x) &= 2xh + h^2 + 2h \\\\
\frac{f(x + h) - f(x)}{h} &= 2x + 2 + h
\end{aligned}
$$

위의 식에서 변수 $h$를 0에 가깝게 만들면, 남는 것은 $2x + 2$가 되며 이 값이 함수 $f(x)$에 대한 미분 값입니다. 고등학생 때까지는 미분 값을 계산하기 위해서 이렇게 일일이 손으로 계산을 해주어야 했지만, 다행히 Python에서는 직접 미분을 손으로 계산하지 않아도 쉽게 계산해 주는 `symbolic python`이라는 라이브러리가 존재합니다!

```py
import sympy as sym
from sympy.abc import x

sym.diff(sym.poly(x**2 + 2*x + 3), x)
# 출력: Poly(2*x + 2, x, domain='ZZ')
```

symbolic python을 사용하기 위해서는 `sympy` 패키지를 가져와서 `sympy.diff()`를 사용하여 미분을 해볼 수 있습니다. 이때, `x` 가 미분하고자 하는 변수임을 명시하기 위해 `sympy.abc`에서 $x$를 가져와야 합니다.

위의 미분을 그림으로 나타내면 아래와 같습니다. 미분이 가능하기 위해서는 먼저 함수의 모든 구간에서 미분이 가능하며 연속적인 그래프여야 하며, 그림은 두 점 $x$ 와 $x+h$에 대한 변화율을 나타낸 것입니다.

![image](https://user-images.githubusercontent.com/91870042/175806890-eefe0513-d188-462d-80be-9d40a3b2e87d.png){: .align-center width="60%"}

이제 이 $h$의 거리가 줄어들어 0이 되면, 함수 위의 한 접선의 기울기를 구할 수 있게 됩니다. 이렇게 미분을 통해서 접선의 기울기를 알게 되었으면, 이제 어떤 방향으로 움직여야 함숫값이 증가하는지 또는 감소하는지 알 수 있습니다.

함숫값을 증가시키기 위해서는 미분 값을 더하고, 감소시키고 싶다면 미분 값을 빼면 됩니다. 위의 그림처럼 볼록한 함수의 경우, 기울기가 음수일 때 원래의 값에 더해주게 되면 뒤로 가서 함숫값이 증가하게 됩니다. 반대로 빼주게 되면 앞으로 이동하기 때문에 함숫값이 감소할 수 있습니다.

![image](https://user-images.githubusercontent.com/91870042/175807076-cefabbea-0e01-4a7d-801e-cbf4eaac6928.png){: .align-center width="60%"}

미분값이 `음수`인 경우  
- $x+f'(x) < x$: 왼쪽으로 이동하여 함수값이 증가  
- $x-f'(x) > x$: 오른쪽으로 이동하여 함수값이 감소

미분값이 `양수`인 경우  
- $x+f'(x) < x$: 오른쪽으로 이동하여 함수값이 증가
- $x-f'(x) > x$: 왼쪽으로 이동하여 함수값이 감소

<br>

## 경사하강법

이제 기울기의 정보도 알고, 어떤 방향으로 이동해야 함숫값이 줄어들거나 증가하는지 알 수 있게 되어 경사하강법을 이해할 준비가 완료되었습니다. 사실 지금까지 한 것들이 모두 `경사하강법` 또는 `경사상승법`입니다. 함숫값을 극솟값으로 이동시키는 것이 경사하강법이며 반대로 극댓값으로 이동시키는 것이 경사상승법입니다.

이후에 딥러닝의 학습방법에서 손실 함수라는 개념이 등장하는데, 이 손실 함수의 값을 최소로 만들기 위해서 우리는 경사하강법을 사용합니다. 반대로 목적함수를 최대화 하고 싶은 경우에는 경사 상승 법을 사용하면 됩니다! 경사하강법은 이렇게 극값을 계속 찾아서 내려가다가 극값에 도달하면 기울기가 0이 되어 더 이상 값의 업데이트가 일어나지 않게 됩니다. 이때가 바로 경사하강법의 종료지점이 됩니다. 이 과정을 코드로 작성해 봅시다.

<br>

## 알고리즘

```py
# gradient: 미분을 계산하는 함수
# init: 시작점, lr: 학습률, eps: 알고리즘 종료조건

var = init
grad = gradient(var)
while abs(grad) > eps:
    var = var - lr * grad
    grad = gradient(var)
```

위의 코드에서는 `eps(epsilon)`이라는 새로운 단어가 등장하였습니다. 원래 경사하강법의 종료 조건은 미분 값이 0이 될 때라서 `abs(grad) != 0` 이 되어야 한다고 생각할 수 있습니다. 하지만 실제로 컴퓨터로 미분 값이 정확히 0이 되는 지점을 찾는 것은 거의 불가능하기 때문에 매우 작은 값 $\epsilon$ 을 설정하여 경사하강법의 종료 조건을 설정합니다.

또 하나 새롭게 등장한 것은 학습률(learning rate)입니다. 코드에서는 `lr`로 표기가 되어있으며 이 값은 왼쪽이나 오른쪽으로 움직일 때, 얼마나 이동할지를 결정하는 값입니다. 이 학습률을 조절할 때 주의해야 할 점은 너무 크게 되면 한 번에 너무 많은 거리를 움직여 극솟값에 도달하기가 힘들어질 수 있습니다. 반대로 너무 작게 되면 한 번에 이동하는 거리가 짧아서 많은 횟수를 업데이트해야 하는 문제가 발생합니다. 따라서 적절한 값을 설정하는 것이 중요합니다.


```py
# 실제 함수 설정을 통한 경사하강법
def func(val):
    fun = sym.poly(x**2 + 2*x + 3)
    return fun.subs(x, val), fun

def func_gradient(fun, val):
    _, function = fun(val)
    diff = sym.diff(function, x)
    return diff.subs(x, val), diff

def gradient_descent(fun, init_point, lr_rate=1e-2, epsilon=1e-5):
    cnt = 0
    val = init_point
    diff, _ = func_gradient(fun, init_point)

    while np.abs(diff) > epsilon:
        val = val - lr_rate * diff
        diff, _ = func_gradient(fun, val)
        cnt += 1

    print("함수: {}, 연산횟수: {}, 최소점: ({}, {})".format(fun(val)[1], cnt, val, fun(val)[0]))

gradient_descent(fun = func, init_point = np.random.uniform(-2, 2))

# >>> 함수: Poly(x**2 + 2*x + 3, x, domain='ZZ'), 연산횟수: 636, 최소점:(-0.9999, 2.0000)
```

## 편미분

지금까지는 수직선 위에서 이동방향이 왼쪽, 오른쪽 2가지인 경우만 고려를 했었습니다. 하지만 실제 딥러닝은 $n$ 차원의 공간에서 학습이 이루어지기 때문에, 다변수 함수에 대해서 미분을 할 수도 있어야 합니다. 벡터가 입력으로 주어지는 다변수 함수의 경우 `편미분(partial differentiation)`을 사용합니다. 편미분은 변수가 하나인 미분과 마찬가지로 특정 장소의 기울기를 구할 수 있으며 하나의 변수만을 고정시키고 나머지 변수는 상수 취급하게 됩니다.

$$ \partial_{x_{i}}f(\mathbf{x})=\lim_{h\rightarrow 0}\frac{f(\mathbf{x}+he_{i}) - f(\mathbf{x})}{h} $$

- $\mathbf{x}$ : $n$ 차원의 행 벡터
- $e_i$ : $i$ 번째 변수에 대해서만 변화율을 계산할 수 있도록 하는 단위벡터



$$ f(x, y) = x^2 + 2xy + 3 + \cos(x + 2y) $$

$$ \partial_x f(x, y) = 2x + 2y - \sin(x + 2y) $$

- $\partial_x$ : $x$ 를 제외한 나머지 변수를 상수취급하며, $x$에 대해서 편미분을 진행

위에서 배운 symbolic python 의 `sympy.diff()` 를 이용해 편미분도 동일하게 진행할 수 있습니다!

<br>

## 그레디언트 벡터

지금은 변수가 $x$, $y$인 다변수 함수에서 $x$에 대해 미분을 진행했지만, 반대로 $y$에 대해서도 편미분을 진행할 수 있습니다. 다시 말해, $n$개의 변수를 갖는 다변수 함수에서는 총 $n$ 번의 편미분이 가능하게 됩니다. 각 변수에 대해서 편미분을 진행한 결과 벡터를 우리는 `그레디언트 벡터(Gradient vector)`라고 하며 이 벡터를 사용해 경사하강법 또는 경사상승법에 사용할 수 있습니다.

$$ \nabla f = (\partial_{x_1} f, \partial_{x_2} f, \cdots, \partial_{x_d} f) $$

- $\nabla f$ : 다변수 함수 $f$ 에 대해 구한 그레디언트 벡터

그레디언트 벡터는 각 차원에서 어느 방향으로 이동을 해야 극솟값에 도달을 할 수 있는지의 정보를 담고 있습니다. 우리는 극솟값에 도달하기 위해 미분 값을 빼주었으므로 이 그레디언트 벡터에 음수를 취하게 되면 각 지점에서 극소점으로 향하는 화살표를 관측할 수 있습니다. 예를 들어 아래와 같은 3차원 공간의 함수가 있다고 해봅시다.

$$ f(x) = x^2 + y^2 $$

![image](https://user-images.githubusercontent.com/91870042/175808564-aafc6e30-85ac-4d1c-b2c7-0b9a97292b0b.png){: .align-center width="50%"}

위의 그래프에서 각 접선의 기울기가 극솟값에 도달하기 위해 어떤 방향으로 향하고 있는지 확인하면 아래와 같은 모습이 나옵니다. 아래 사진을 보면, 모두 그래프의 극솟값이 중앙 부분을 향하는 것을 알 수 있고, 극솟값 부분에서 멀어질수록 그 기울기는 더 크다는 것도 확인할 수 있습니다.

![image](https://user-images.githubusercontent.com/91870042/149929482-8cdc35d8-6b3e-4e86-8a39-ab59af6f53c2.png){: .align-center width="50%"}

이렇듯 Gradient Vector를 사용하여 주어진 함수의 최솟값, 최댓값을 구하는데 활용할 수 있고, 경사하강법과 경사상승법에 적용을 하면 최적화를 진행할 수 있습니다. 아래는 다변수 함수에 대해 경사하강법을 진행하는 알고리즘과 실제 함수를 적용한 예시입니다.


```py
Input: gradient, init, lr, eps, Output: var
# gradient: 그레디언트 계산하는 함수
# init: 시작점, lr: 학습률, eps: 알고리즘 종료조건

var = init
grad = gradient(var)
while norm(grad) > eps:
    var = var - lr * grad
    grad = gradient(var)
```

$n$ 차원 공간에서 경사하강법을 진행할 때 설정하는 종료 조건은 조금 다릅니다. 벡터의 기울기에 절댓값 대신에 L2-노름을 사용하여 종료 조건을 설정하게 됩니다.

**🤔 L2 노름을 사용하여종료 조건을을 설정하는 이유?**<br>
여기서 L2 노름을 사용하는 이유에 대해서 Google에도 원하는 답변을 얻을 수 없어서 생각을 해보았습니다. 아마 경사하강법을 통해 이동한 거리가 $\epsilon$ 보다 작다면극솟값에에 도달하였음을 의미하므로 종료하게 되는 것 같습니다.
{: .notice--warning}

```py
# Multivariate Gradient Descent
def eval_(fun, val):
    val_x, val_y = val
    fun_eval = fun.subs(x, val_x).subs(y, val_y)
    return fun_eval

def func_multi(val):
    x_, y_ = val
    func = sym.poly(x ** 2 + 2 * y ** 2)
    return eval_(func, [x_, y_]), func

def func_gradient(fun, val):
    x_, y_ = val
    _, function = fun(val)
    diff_x = sym.diff(function, x)
    diff_y = sym.diff(function, y)
    grad_vec = np.array([eval_(diff_x, [x_, y_]), eval_(diff_y, [x_, y_])], dtype = float)
    return grad_vec, [diff_x, diff_y]

def gradient_descent(fun, init_point, lr_rate=1e-2, epsilon=1e-5):
    cnt = 0
    val = init_point
    diff, _ = func_gradient(fun, init_point)

    while np.linalg.norm(diff) > epsilon:
        val = val - lr_rate * diff
        diff, _ = func_gradient(fun, val)
        cnt += 1

    print("함수: {}, 연산횟수: {}, 최소점: ({}, {})".format(fun(val)[1], cnt, val, fun(val)[0]))

pt = [np.random.uniform(-2, 2), np.random.uniform(-2, 2)]
gradient_descent(fun=func_multi, init_point=pt)

# >>> 함수: Poly(x**2 + 2*y**2, x, y, domain='ZZ'), 연산횟수: 606, 최소점:([4.95901570e-06, 2.88641061e-11], 2.45918366929856E-11)
```
<br>

# References

[🎨 이미지 소스](https://unsplash.com/photos/_HnJfS6WhA8)

[📘 Gradient Descent - Wikipedia](https://en.wikipedia.org/wiki/Gradient_descent)
