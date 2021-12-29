---
title:  "[부스트캠프 Pre-Course] AI수학: 경사하강법"
excerpt: "미분의 개념과 그래디언트 벡터에 대한 설명"

categories:
  - boostcamp
tags:
  - [AI, Naver, BoostCamp, Python, Math]
toc: true
toc_sticky: true
 
date: 2021-12-08
last_modified_at: 2021-12-08
---

# <span style = "color: #00adb5">미분</span>
`미분(differentiation)`은 변수의 움직임에 따른 함수값의 변화를 측정하기 위한 도구로 최적화에서 제일 많이 사용하는 기법이다. 미분은 다시, 변화율의 극한으로 정의할 수 있다.

$$f'(x)=\lim_{h\rightarrow 0}\frac{f(x+h)-f(x)}{h}$$

미분은 손으로 직접 계산하는 대신, Python에서 미분을 계산해줄 수 있다.

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

![image](https://user-images.githubusercontent.com/91870042/145066068-166385e6-c230-4a20-91aa-e96b02e71f84.png){: .align-center}

한 점에서 접선의 기울기를 알면, 어느 방향으로 점을 움직여야 함수값이 `증가`하는지/`감소`하는지 알 수 있다. 결론적으로 말하자면, 함수값을 `증가`시키고 싶다면 미분값을 더하고, `감소`시키고 싶다면 미분값을 뺀다.
- 미분값이 `음수`인 경우  
\\(x+f'(x) < x\\): 왼쪽으로 이동하여 함수값이 증가한다.  
\\(x-f'(x) > x\\): 오른쪽으로 이동하여 함수값이 감소한다.

- 미분값이 `양수`인 경우  
\\(x+f'(x) < x\\): 오른쪽으로 이동하여 함수값이 증가한다.  
\\(x-f'(x) > x\\): 왼쪽으로 이동하여 함수값이 감소한다.

<br>

# <span style = "color: #00adb5">미분의 사용처</span>
미분값을 더하면 `경사상승법(gradient ascent)`이라 하며 함수의 `극대값`의 위치를 구할 때 사용한다. **목적함수를 최대화 할 때 사용한다**

![image](https://user-images.githubusercontent.com/91870042/145067697-352bc3e6-8890-4f4a-bcda-f0542cd261ff.png){: .align-center}

미분값을 빼면 `경사하강법(gradient descent)`이라 하며 함수의 `극소값`의 위치를 구할 때 사용한다.
**목적함수를 최소화 할 때 사용한다**

![image](https://user-images.githubusercontent.com/91870042/145067345-562566a4-aec8-464f-bf97-f0399fd4f88d.png){: .align-center}

극값에서는 미분값이 0이므로, 더 이상 업데이트가 되지 않는다. 그러므로 목적함수 최적화가 자동으로 끝난다.

![image](https://user-images.githubusercontent.com/91870042/145067487-17e5c197-5c58-40e9-a814-f2e02c8cc465.png){: .align-center}

<br>

# <span style = "color: #00adb5">경사하강법: 알고리즘</span>
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
컴퓨터로 계산할 때, 미분이 정확히 0이되는 것은 불가능하므로 `eps`라는 값보다 작을 때 종료하는 조건이 필요하다.

```py
var = var - lr * grad
```
이 부분이 \\(x-\lambda f'(x)\\)를 계산하는 부분이다. `lr`은 학습률로서 미분을 통해 업데이트 하는 속도를 조절한다.

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
벡터가 입력인 다변수 함수의 경우 `편미분(partial differentiation)`을 사용한다.

\\[
    \partial_{x_{i}}f(x)=\lim_{h\rightarrow 0}\frac{f(x+he_{i}) - f(x)}{h}
\\]

> \\(e_{i}\\)는 i번째 값만 1이고 나머지는 0으로 채워진 단위벡터를 의미한다.

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

![image](https://user-images.githubusercontent.com/91870042/145070929-fe52c0af-d53c-4f56-8a98-35eff4d0ea3b.png){: .align-center}


![image](https://user-images.githubusercontent.com/91870042/145070990-27baa9f3-f3c5-436a-9c74-190b30c55934.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/145071055-a8b58948-2e65-45c9-b7fd-9d89d5500b58.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/145071124-18f5e586-4073-4e4f-8558-aa31d0103ba2.png){: .align-center}



Gradient Vector를 사용하여 주어진 함수의 최소값, 최대값을 구하는데 활용할 수 있고, 경사하강법과 경사상승법에 적용을 하면 최적화를 진행할 수 있다.


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

# <span style = "color: #00adb5">References</span>
[📘 부스트캠프 AI Tech 3기 Pre-Course: 경사하강법 - 순한맛](https://www.boostcourse.org/onlyboostcampaitech3/lecture/1203364?isDesc=false)