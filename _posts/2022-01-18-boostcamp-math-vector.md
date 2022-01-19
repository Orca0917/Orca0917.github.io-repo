---
title: "[AI Math] 벡터"
excerpt: "벡터의 기본 개념과 연산, 노름에 대한 소개"

categories:
  - boostcamp
tags:
  - [AI, Naver, BoostCamp, Math]
toc: true
toc_sticky: true
 
date: 2022-01-18 10:00:00
last_modified_at: 2022-01-18 10:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}
<br>

# 벡터
벡터는 숫자를 원소로 가지는 `리스트` 또는 `배열`이다. 벡터는 숫자가 어느 방향으로 나열되어 있는지에 따라 `열 벡터`와 `행 벡터`로 나눌 수 있다.

## 벡터의 표현
벡터는 다음 2가지로 표현이 가능하다.

![image](https://user-images.githubusercontent.com/91870042/145021482-30a82ee2-670c-4d28-baee-7eb05a808ecc.png){: .align-center}

```py
x = [1, 7, 2]
x = np.array([1, 7, 2])
```

## 벡터의 차원
`벡터의 차원`은 벡터에 존재하는 수의 개수를 말한다. 일반적으로 프로그래밍에서는 행 벡터를 사용하여 벡터를 표현한다.
벡터는 공간에서 한 점을 나타낸다.

1차원 공간에서의 벡터는 수직선 위의 한 점이라고 나타낼 수 있다.

![image](https://user-images.githubusercontent.com/91870042/145009609-534ff1ac-cc47-47fb-aa65-bcf1ba4d8564.png){: .align-center}

2차원 공간에서의 벡터는 좌표평면의 한 점으로 나타낼 수 있다.
3차원 공간에서의 벡터는 \\((x, y, z)\\)로 점으로 나타낼 수 있다.

![image](https://user-images.githubusercontent.com/91870042/145011503-ce3f4ff3-0455-4479-8fd0-a4ebcbd1b352.png){: .align-center}

벡터는 지금까지 말했던 하나의 점을 원점으로 부터 표현하는 것을 말한다.(원점으로부터의 상대적인 위치를 표현한다.) 흔히 원점으로 부터 해당 점까지를 화살표로 표현을 한다.

![image](https://user-images.githubusercontent.com/91870042/145011597-a2c02d5f-bd84-461a-8707-3fdbcf8bb909.png){: .align-center}

## 벡터의 연산
어떤 벡터가 있을 때, 그 벡터에 숫자를 곱하게 되면 `길이만 변화`한다. 곱하는 행위를 `스칼라곱`이라고 부른다. 스칼라곱은 벡터의 길이를 변환시켜 주는 것으로서, 곱하는 수 \\(x\\)가 1보다 작으면 줄어들고, 1보다 크면 벡터의 길이가 더 길어진다. 하지만, \\(x\\)의 값이 음수라면, 반대방향으로 가게 된다.

![image](https://user-images.githubusercontent.com/91870042/145011780-1012f4da-d138-4dec-b048-29453dc4952d.png){: .align-center}

벡터끼리 `같은 모양`을 가지면 `덧셈`, `뺄셈`과 벡터의 곱셈연산 중 하나인 `성분곱`을 할 수 있다.

![image](https://user-images.githubusercontent.com/91870042/145011919-dd751116-7c79-4e41-9a0f-2a72dd991e1f.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/145011950-c69fdcfb-e82d-49cc-91fd-33c0374d8419.png){: .align-center}


```py
import numpy as np

x = np.array([1, 7, 2])
y = np.array([5, 2, 1])

x + y # array([6, 9, 3])
x - y # array([-4, 5, 1])
x * y # array([5, 14, 2])
```

두 벡터의 덧셈은 다른 벡터로 부터 상대적 위치 이동을 표현한다.

![image](https://user-images.githubusercontent.com/91870042/145012026-7c0bcae7-53bf-4efc-9cfd-c20c372e8e0a.png){: .align-center}

두 벡터의 뺄셈은 방향을 뒤집은 두 벡터의 덧셈과 동일하다.

![image](https://user-images.githubusercontent.com/91870042/145012084-78210ecd-b4cd-458d-8f37-1d9d84cbb463.png){: .align-center}
<br>

# <span style = "color: #00adb5">벡터의 노름</span>
벡터의 노름(norm)은 `원점에서부터의 거리`를 말한다. 노름을 계산하는 방법은 2가지가 있는데 각각, \\(L_1\\)-노름, \\(L_2\\)-노름이라고 부른다.

\\(L_1\\)-노름은 각 성분의 `변화량의 절대값`을 모두 더한다.

![image](https://user-images.githubusercontent.com/91870042/145017643-9ca10dd4-63a4-48a3-af9e-e1c0c5a7a33f.png){: .align-center}

```py
def l1_norm(x):
    x_norm = np.abs(x)
    x_norm = np.sum(x_norm)
    return x_norm
```

\\(L_2\\)-노름은 피타고라스 정리를 이용해 `유클리드 거리`를 계산한다.

![image](https://user-images.githubusercontent.com/91870042/145017708-620dbeda-443a-4d72-85d9-81a6d6166d69.png){: .align-center}

```py
def l2_norm(x):
    x_norm = x * x
    x_norm = np.sum(x_norm)
    x_norm = np.sqrt(x_norm)
    return x_norm
```

\\(L_2\\)-노름은 `np.linalg.norm`을 사용하여 계산을 할 수도 있다.

> 노름을 계산하는 2가지 종류가 있는 이유  
> 노름의 종류에 따라서 기하학적 성질이 달라지는데, \\(L_1\\)-노름은 절대 값의 합이 1인 좌표를 모으게 되면, 마름모 꼴의 도형이 나오게 되고, \\(L_2\\)-노름은 거리가 1인 좌표들을 모으면 원이 나오게 된다. 그 이유는 각 노름별로 명시하는 거리의 개념이 다르기 때문이다. 머신러닝에서는 각 성질들이 필요할 때가 있어서 두 가지 경우 모두 사용한다.  
> 주로 \\(L_1\\)-노름은 Robust학습과 Lasso회귀에 사용이 되고, \\(L_2\\)-노름은 Laplace근사와 Ridge회귀에서 사용이 된다.

![image](https://user-images.githubusercontent.com/91870042/145018394-908c60b3-a53d-4193-a3ec-663fb0fd0fdd.png){: .align-center}
<br>

# <span style = "color: #00adb5">두 벡터 사이의 거리</span>
두 벡터사이의 거리는 두 점 사이의 거리를 구하는 것과 동일하다. 각 종류의 노름에 따라서 서로가 명시하는 거리의 개념이 다르기 때문에 그 거리의 값이 다르게 나온다. 두 벡터 사이의 거리를 계산할 때는 벡터의 `뺄셈`을 이용하게 된다. 그 이유는 하나의 벡터를 빼게 되었을때 나오는 거리가 기존의 두 벡터의 거리와 동일하기 때문이다. 자세한 것은 아래의 그림을 참고하면 된다. 추가로 벡터의 뺄셈을 할때, 두 벡터의 순서를 거꾸로 해도 그 결과는 동일하다.

![image](https://user-images.githubusercontent.com/91870042/145018823-0c00a50a-8be1-4cd7-8f1c-750ead5cdc76.png){: .align-center}
<br>

# <span style = "color: #00adb5">두 벡터 사이의 각도</span>
각도는 **\\(L_2\\)-노름으로만 계산이 가능**하다. 여기서의 각도는 단순히 2차원 좌표평면상의 두 벡터의 각도가 아니라 임의의 \\(d\\)차원에서 두 벡터의 각도를 의미한다. \\(L_2\\)-노름으로 두 점사이의 거리를 계산하는 것이 가능하고, \\(x\\)벡터, \\(y\\)벡터를 알고 있기 때문에 `제2 코사인 법칙`에 의해서 두 벡터 사이의 각도를 계산하는 것이 가능하다.

![image](https://user-images.githubusercontent.com/91870042/145019319-33b5b545-4eca-4e5e-8f38-f654c2575fe0.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/145019596-f2727df4-355b-4e9b-a2ef-0dae45267660.png){: .align-center}

```py
def angle(x, y):
    v = np.inner(x, y) / (l2_norm(x) * l2_norm(y))
    theta = np.arccos(v)
    return theta
```
<br>

# 내적
내적은 `정사영(orthogonal projection)`된 벡터의 길이와 관련되어 있다. 어떠한 벡터 \\(x\\)가 있을 때, 해당 벡터의 정사영은 다른 벡터\\(y\\)에 그려진 벡터 \\(x\\)의 그림자와 동일하다. 그렇기 때문에 정사영된 벡터\\(x\\)를 \\(proj(x)\\)라고 한다면 \\(proj(x) = ||x||cos\theta\\)로 표현할 수 있다.

![image](https://user-images.githubusercontent.com/91870042/145020380-bb98a36e-82ed-46f2-b63f-bd70edc140fc.png){: .align-center}


내적은 정사영의 길이를 벡터\\(y\\)의 길이 \\(\|\|y\|\|\\)만큼 조정한 값과 동일하며 주로, 두 벡터의 `유사도`를 측정하는데 사용한다.

![image](https://user-images.githubusercontent.com/91870042/145020516-ff580bc6-a3a0-41bb-b5c9-520ed0915d8f.png){: .align-center}
<br>

