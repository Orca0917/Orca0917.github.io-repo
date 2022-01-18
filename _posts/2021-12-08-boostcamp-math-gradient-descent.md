---
title:  "[AI Math]: 경사하강법 (1)"
excerpt: "딥러닝 학습의 향상을 위한 손실함수의 미분과 경사하강법에 대한 소개"

categories:
  - boostcamp
tags:
  - [AI, Naver, BoostCamp, Python, Math]
toc: true
toc_sticky: true
 
date: 2022-01-18
last_modified_at: 2022-01-18
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}
지금까지 딥러닝에 대해서 학습시키고, 그 학습시킨 결과와 정답과의 차이가 얼마나 있는지 판단하기 위해서 손실함수(Loss Function)을 사용해왔다. 그리고 손실함수의 값을 줄여야 더 높은 정확도를 갖게되었다. 

그렇게 하기 위해 손실함수의 결과값을 줄여야 했는데, 그 방법으로는 손실함수를 미분하고 그 값이 극소값으로 이동하게 만들면 된다. 따라서 미분이라는 과정이 필요하게 되고 이번에 그때 사용하는 미분과 경사하강법에 대해서 알아보자.

# 미분
`미분(differentiation)`은 변수의 움직임에 따른 함수값의 변화를 측정하기 위한 도구로 최적화에서 제일 많이 사용하는 기법이다. 

$$f'(x)=\lim_{h\rightarrow 0}\frac{f(x+h)-f(x)}{h}$$

위의 수식과 같이 어떤 기울기를 보통 계산을 할 때, 주어진 한 점 \\(x\\)와 그곳에서 \\(h\\)만큼 떨어진 \\(x+h\\)에서의 함수값의 차를 이동한 거리 \\(h\\)로 나누어서 계산할 수 있었다. 이것을 기울기라고 부르고, 미분은 이 기울기를 어느 한 점에 대해서 계산할 수 있게 \\(h\\)의 값을 0에 최대한 가깝게 만든다.

> 이것을 변화율의 '극한'이라고 부른다.

다행히, Python에서는 직접 미분을 손으로 계산하지 않아도 쉽게 계산해주는 라이브러리가 존재한다.

```py
import sympy as sym
from sympy.abc import x

sym.diff(sym.poly(x**2 + 2*x + 3), x)
```
```
Poly(2*x + 2, x, domain='ZZ')
```

## 그림을 통한 미분의 이해
미분은 함수 \\(f\\)위의 점 \\((x, f(x))\\)에서의 접선의 기울기와 동일하다.

![image](https://user-images.githubusercontent.com/91870042/149925166-a7254354-8897-465f-a9b2-f97fc4b5b27a.png){: .align-center}

한 점에서 접선의 기울기를 알면, 어느 방향으로 점을 움직여야 함수값이 `증가`하는지 `감소`하는지 알 수 있다. 결론적으로 말하자면, <u>함수값을 `증가`시키고 싶다면 미분값을 더하고, `감소`시키고 싶다면 미분값을 뺀다.</u>
- 미분값이 `음수`인 경우  
\\(x+f'(x) < x\\): 왼쪽으로 이동하여 함수값이 증가한다.  
\\(x-f'(x) > x\\): 오른쪽으로 이동하여 함수값이 감소한다.

- 미분값이 `양수`인 경우  
\\(x+f'(x) < x\\): 오른쪽으로 이동하여 함수값이 증가한다.  
\\(x-f'(x) > x\\): 왼쪽으로 이동하여 함수값이 감소한다.

<br>

# 미분의 사용처
미분값을 더하면 `경사상승법(gradient ascent)`이라 하며 함수의 `극대값`의 위치를 구할 때 사용한다. **목적함수를 최대화 할 때 사용한다**

미분값을 빼면 `경사하강법(gradient descent)`이라 하며 함수의 `극소값`의 위치를 구할 때 사용한다.
**목적함수를 최소화 할 때 사용한다**

![image](https://user-images.githubusercontent.com/91870042/149927360-d6bc41c1-7892-4bb3-9c79-f718bac89010.png){: .align-center}

극값에서는 미분값이 0이므로, 더 이상 업데이트가 되지 않는다. 그러므로 목적함수 최적화가 자동으로 끝난다.

<br>

# 경사하강법: 알고리즘
```py
Input: gradient, init, lr, eps, Output: var
# gradient: 미분을 계산하는 함수
# init: 시작점, lr: 학습률, eps: 알고리즘 종료조건

var = init
grad = gradient(var)
while(abs(grad) > eps):
    var = var - lr * grad
    grad = gradient(var)
```
컴퓨터로 계산할 때, 미분이 정확히 0이되는 것은 거의 불가능하므로 `eps`라는 값보다 작을 때 종료하는 조건이 필요하다.

```py
var = var - lr * grad
```
이 부분이 \\(x-\lambda f'(x)\\)를 계산하는 부분이다. `lr`은 학습률(learning rate)로서 미분을 통해 업데이트 하는 속도를 조절한다.

## 실제 적용 예시
```py
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

>>> 함수: Poly(x**2 + 2*x + 3, x, domain='ZZ'), 연산횟수: 636, 최소점:(-0.9999, 2.0000)
```

## 변수가 벡터인 경우
벡터가 입력인 다변수 함수의 경우 `편미분(partial differentiation)`을 사용한다. 편미분은 변수가 하나인 미분과 마찬가지로 특정 장소의 기울기를 구한다. 단, 여러 변수들 중 하나의 변수를 고르고 나머지 변수는 그대로 둔다. 
\\[
    \partial_{x_{i}}f(x)=\lim_{h\rightarrow 0}\frac{f(x+he_{i}) - f(x)}{h}
\\]

> \\(e_{i}\\)는 i번째 값만 1이고 나머지는 0으로 채워진 단위벡터를 의미한다.  
> 이 단위벡터를 이용하여 어떤 축을 기준으로 이동할지 결정한다.

```py
import sympy as sym
from sympy.abc import x

sym.diff(sym.poly(x**2 + 2*x + 3) + sym.cos(x + 2*y), x)
```
```
2*x + 2*y - sin(x + 2*y)
```

각 변수 \\(d\\)개 만큼 편미분을 계산한 그레디언트 벡터를 이용하여 경사하강법, 경사상승법에 사용할 수 있다.

![image](https://user-images.githubusercontent.com/91870042/145069901-59dc0a48-fcd9-416a-9ea9-a45270548905.png){: .align-center}

### 그레디언트 벡터
> \- 를 붙이게 되면, 극소점으로 향하는 화샬표를 볼 수 있다.

\\[
    f(x) = x^2 + y^2
\\]

위의 함수를 3차원 그래프로 그려보면 아래 결과가 나온다.

![image](https://user-images.githubusercontent.com/91870042/149929311-8a4e9f46-7a11-44aa-a575-4bc8f49f3bb0.png){: .align-center}

위의 그래프에서 각 접선의 기울기가 극소값에 도달하기 위해 어떤 방향으로 향하고 있는지 확인하면 아래와 같은 모습이 나온다. 아래 사진을 보면, 모두 그래프의 극소값이 중앙부분을 향하는 것을 알 수 있고, 극소값 부분에서 멀어질 수록 그 기울기는 더 크다는 것도 확인할 수 있다.

![image](https://user-images.githubusercontent.com/91870042/149929482-8cdc35d8-6b3e-4e86-8a39-ab59af6f53c2.png){: .align-center}

이렇듯 Gradient Vector를 사용하여 주어진 함수의 최소값, 최대값을 구하는데 활용할 수 있고, 경사하강법과 경사상승법에 적용을 하면 최적화를 진행할 수 있다.


```py
Input: gradient, init, lr, eps, Output: var
# gradient: 그레디언트 계산하는 함수
# init: 시작점, lr: 학습률, eps: 알고리즘 종료조건

var = init
grad = gradient(var)
while(norm(grad) > eps):
    var = var - lr * grad
    grad = gradient(var)
```

### 실제 적용 예시
```py
# Multivariate Gradient Descent
def eval_(fun, val):
    val_x, val_y = val
    fun_eval = fun.subs(x, val_x).subs(y, val_y)
    return fun_eval

def func_multi(val):
    x_, y_ = val
    func = sym.poly(x**2 + 2*y**2)
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
gradient_descent(fun = func_multi, init_point=pt)

>>> 함수: Poly(x**2 + 2*y**2, x, y, domain='ZZ'), 연산횟수: 606, 최소점:([4.95901570e-06, 2.88641061e-11], 2.45918366929856E-11)
```
<br>

# References
[📘 Gradient Descent - Wikipedia](https://en.wikipedia.org/wiki/Gradient_descent)