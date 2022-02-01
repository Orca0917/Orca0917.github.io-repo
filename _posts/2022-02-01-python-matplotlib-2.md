---
title:  "matplotlib: 데이터 시각화 (2)"
excerpt: "matplotlib의 "

categories:
  - matplotlib
tags:
  - [Python, matplotlib]

toc: true
toc_sticky: true
 
date: 2022-02-01 01:00:00
last_modified_at: 2022-02-01 01:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **Wikidocs의 Matplotlib Tutorial - 파이썬으로 데이터 시각화하기**를 읽고 정리하는 글입니다.<br>
더 자세한 설명은 해당 [문서](https://wikidocs.net/book/5011)에 더 상세하게 설명되어 있습니다.
{: .notice--info}

# 여러 곡선 그리기
`plot()`함수에 **X값, Y값, 스타일**을 한 묶음으로 그리고 싶은 곡선의 수 만큼 입력한다. 또는 `plot()`을 여러번 사용해서 지정할 수 있다.
```py
x = np.arange(0, 2, 0.2)

# plt.plot(x, x, 'bo')
# plt.plot(x, x**2, color='#e35f62', marker='*', linewidth=2)
# plt.plot(x, x**3, color='forestgreen', marker='^', markersize=9)

plt.plot(x, x, 'r--', x, x**2, 'bo', x, x**3, 'g-.')
plt.show()
```

![temp](https://user-images.githubusercontent.com/91870042/151973291-527a1364-983a-4331-abc4-c651f50e86e5.png){: .align-center}

<br>

# Grid: 격자판 표시
데이터의 위치를 더 명확하게 나타나기 위해서 `plt.grid(True)`를 사용해서 그래프에 격자판을 표시할 수 있다.

```py
x = np.arange(0, 2, 0.2)

plt.plot(x, x, 'bo')
plt.plot(x, x**2, color='#e35f62', marker='*', linewidth=2)
plt.plot(x, x**3, color='springgreen', marker='^', markersize=9)
plt.grid(True)

plt.show()
```

![temp](https://user-images.githubusercontent.com/91870042/151973570-6385f3ec-37f2-43d0-88de-88b8bf2e8b44.png){: .align-center}

기본적으로 생기는 격자판 이외에 X축을 기준으로 하는 세로선이나, Y축을 기준으로 만드는 가로선을 지정하고 싶다면, grid함수에 `axis='X'` 또는 `axis='Y'` 옵션을 지정한다.

```py
x = np.arange(0, 2, 0.2)

plt.plot(x, x, 'bo')
plt.plot(x, x**2, color='#e35f62', marker='*', linewidth=2)
plt.plot(x, x**3, color='forestgreen', marker='^', markersize=9)

plt.grid(True, axis='x')
plt.grid(True, axis='y')

plt.show()
```

![temp](https://user-images.githubusercontent.com/91870042/151973792-176976fb-e75b-4002-ab5f-fc8b10617a0f.png){: width="49%"} ![temp](https://user-images.githubusercontent.com/91870042/151973812-57ccdfe1-02fd-4b70-9529-9910fed6a76c.png){: width="49%"}

필요에 따라서는 생성되는 그리드 선의 종류나 색상을 선택할 수 있다.
- color: 선의 색상을 지정
- alpha: 선의 투명도를 지정
- linestyle: 선의 종류를 지정

```py
x = np.arange(0, 2, 0.2)

plt.plot(x, x, 'bo')
plt.plot(x, x**2, color='#e35f62', marker='*', linewidth=2)
plt.plot(x, x**3, color='springgreen', marker='^', markersize=9)
plt.grid(True, axis='y', color='yellow', alpha=0.5, linestyle='--')

plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/151974075-bbc7af9f-0914-4365-b3e0-e78cec74273a.png){: .align-center}

<br>

# ticks: 눈금 표시
`plot()`을 사용했을 때, 자동으로 생성되는 눈금을 직접 설정하고 싶다면, `plt.xticks()` 또는 `plt.yticks()`를 사용할 수 있다.

```py
x = np.arange(0, 2, 0.2)

plt.plot(x, x, 'bo')
plt.plot(x, x**2, color='#e35f62', marker='*', linewidth=2)
plt.plot(x, x**3, color='forestgreen', marker='^', markersize=9)
plt.xticks([0, 1, 2])
plt.yticks(np.arange(1, 6))

plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/151974583-5cd59e15-49fd-44eb-a892-e78c200a4af5.png){: .align-center}

추가로, 기본적으로 눈금은 숫자형으로 표시가 되는데, 눈금의 라벨을 `labels=`를 사용하여 지정할 수 있다.

```py
x = np.arange(0, 2, 0.2)

plt.plot(x, x, 'bo')
plt.plot(x, x**2, color='#e35f62', marker='*', linewidth=2)
plt.plot(x, x**3, color='springgreen', marker='^', markersize=9)
plt.xticks(np.arange(0, 2, 0.2), labels=['Jan', '', 'Feb', '', 'Mar', '', 'May', '', 'June', ''])
plt.yticks(np.arange(0, 7), ('0', '1GB', '2GB', '3GB', '4GB', '5GB', '6GB'))

plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/151974778-e365e92b-3d68-4704-b813-5d469124f474.png){: .align-center}

<br>

# title: 제목 설정

`plt.title`을 사용하여 해당 그래프의 제목을 설정할 수 있다. 제목을 설정하여 그래프가 나타내고 있는 것이 어떤 현황/지표인지 알려줄 수 있다.

```py
x = np.arange(0, 2, 0.2)

plt.plot(x, x, 'bo')
plt.plot(x, x**2, color='#e35f62', marker='*', linewidth=2)
plt.plot(x, x**3, color='forestgreen', marker='^', markersize=9)

plt.tick_params(axis='both', direction='in', length=3, pad=6, labelsize=14)
plt.title('Graph Title')

plt.show()
```

![temp](https://user-images.githubusercontent.com/91870042/151975147-891d4067-a691-4f74-ac0b-2436b1e6370c.png){: .align-center}

`loc=` 옵션을 사용해서 타이틀이 생성되는 위치도 조정할 수 있다.
- left
- right
- center : default;

<br>

# 수평선, 수직선 표시

그래프에서 표현되는 그래프에서 추가로 원하는 길이만큼 수직선이나 수평선을 그릴 수 있다.
- `axhline(y, xmin, xmax)`: 축을 따라서 수평선을 표시한다.
- `axvline(x, ymin, ymax)`: 축을 따라서 수직선을 표시한다.
- `hlines(y, xmin, xmax)`: 지정한 점을 따라서 수평선을 표시한다.
- `vlines(x, ymin, ymax)`: 지정한 점을 따라서 수직선을 표시한다.

```py
x = np.arange(0, 4, 0.5)

plt.plot(x, x + 1, 'bo')
plt.plot(x, x**2 - 4, 'g--')
plt.plot(x, -2*x + 3, 'r:')

# 수평선으로 그리기 (1)
plt.axhline(4.0, 0.1, 0.9, color='lightgray', linestyle='--', linewidth=2)
plt.hlines(-0.62, 1.0, 2.5, color='gray', linestyle='solid', linewidth=3)

# 수직선으로 그리기 (2)
plt.axvline(1.0, 0.2, 0.8, color='lightgray', linestyle='--', linewidth=2)
plt.vlines(1.8, -3.0, 2.0, color='gray', linestyle='solid', linewidth=3)

plt.show()
```

![temp](https://user-images.githubusercontent.com/91870042/151975947-0867a21a-3b60-4470-90b5-50db89f1fe18.png){: width="49%"} ![temp](https://user-images.githubusercontent.com/91870042/151976008-3ff46f3d-c916-404b-bd8c-81e7033dfa50.png){: width="49%"}

<br>

# bar: 막대그래프 (수직)

범주가 있는 데이터 값을 직사각형의 막대로 표현하는 그래프로서 `plt.bar(x, y)`을 사용해서 그린다.
```py
x = np.arange(3)
years = ['2018', '2019', '2020']
values = [100, 400, 900]

plt.bar(x, values)
# plt.bar(x, values, color=['r', 'g', 'b'])

plt.xticks(x, years)

plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/151976277-bb1bf5bb-909f-4ebd-83d5-b3ad76cc3c62.png){: width="49%"} ![temp](https://user-images.githubusercontent.com/91870042/151976417-bf87b2d7-10d0-42c5-a5a1-d588893a0075.png){: width="49%"}

막대 그래프의 폭을 비율(0 ~ 1)로 직접 설정할 수 있으며, 기본비율은 `0.8`이다.

```py
x = np.arange(3)
years = ['2018', '2019', '2020']
values = [100, 400, 900]

plt.bar(x, values, width=0.2) # 1
plt.bar(x, values, width=0.6) # 2
plt.bar(x, values, width=1.0) # 3
plt.xticks(x, years)

plt.show()
```

![temp](https://user-images.githubusercontent.com/91870042/151976770-d35ada11-85fa-4fd3-8948-db3f6b20a216.png){: width="32%"} ![temp](https://user-images.githubusercontent.com/91870042/151976812-43cb6717-8d5e-4213-87bc-defe45b3464a.png){: width="32%"} ![temp](https://user-images.githubusercontent.com/91870042/151976858-7091dff6-2839-4eae-9ef6-fc3c127088e1.png){: width="32%"}

<br>

# barh: 막대그래프 (수평)
`plt.barh(x, y, color=)`를 사용하면 수직 막대그래프와 유사한 수평 막대그래프가 생성되며 기본적으로 사용하는 옵션은 수직 그래프와 동일한 옵션을 사용할 수 있다.

```py
y = np.arange(3)
years = ['2018', '2019', '2020']
values = [100, 400, 900]

plt.barh(y, values)
plt.yticks(y, years)

plt.show()
```

![temp](https://user-images.githubusercontent.com/91870042/151977075-7b1e009d-f904-42a2-8cb0-d2ab7cfbe8cc.png){: .align-center}

<br>

# Scatter plot: 산점도 그래프(2D, 3D)

**산점도(Scatter plot)**는 두 변수의 상관관계를 2차원 평면상의 점으로 표현하는 그래프이다. 산점도를 그리기 위해서는 `plt.scatter(x, y, s=area, c=colors)`를 사용한다.

```py
np.random.seed(0)

n = 50
x = np.random.rand(n)
y = np.random.rand(n)

plt.scatter(x, y)
plt.show()
```

![temp](https://user-images.githubusercontent.com/91870042/151977991-36ae9dec-abc3-498c-96cc-c6f4b2e4ec1f.png){: .align-center}

위의 산점도 그래프에서 마커의 색상과 점의 크기를 지정하고 싶다면 scatter함수 내에 있는 `s`와 `c`값을 지정하면 된다. 각각 점의 크기와 색상을 지정하는 역할을 한다.

마커의 색상을 지정할 때는 일반적으로 그래프에서 사용하던 점의 크기를 `markersize`라고 한다면, 산점도 그래프에서 점의 크기는 `markersize` ** 2의 크기로 지정한다.

```py
np.random.seed(0)

n = 50
x = np.random.rand(n)
y = np.random.rand(n)
area = (30 * np.random.rand(n))**2
colors = np.random.rand(n)

plt.scatter(x, y, s=area, c=colors)
plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/151978156-c4a8a6af-3d33-467a-b4e6-5dd6d9d46fe3.png){: .align-center}

2차원 평면 이외에 3차원 공간에서도 산점도를 나타낼 수 있다. 3차원 산점도 그래프를 생성하는 기본문법은 `plt.scatter(x, y, z)`이다.

```py
from mpl_toolkits.mplot3d import Axes3D
import matplotlib.pyplot as plt
import numpy as np

n = 100 # 그래프에서 그릴 점의 개수
xmin, xmax, ymin, ymax, zmin, zmax = 0, 20, 0, 20, 0, 50 # 범위 설정
cmin, cmax = 0, 2 # 색상 값의 범위

# 각 축의 범위 내에 존재하는 임의의 실수 n개 생성
xs = np.array([(xmax - xmin) * np.random.random_sample() + xmin for i in range(n)])
ys = np.array([(ymax - ymin) * np.random.random_sample() + ymin for i in range(n)])
zs = np.array([(zmax - zmin) * np.random.random_sample() + zmin for i in range(n)])
color = np.array([(cmax - cmin) * np.random.random_sample() + cmin for i in range(n)])


fig = plt.figure(figsize=(6, 6))
ax = fig.add_subplot(111, projection='3d') # 3D로 지정하기 위해 projection='3d' 사용
ax.scatter(xs, ys, zs, c=color, marker='o', s=15, cmap='Greens') # cmap은 점의 색상 계열을 지정한다.

plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/151979534-61a7ccc7-3e97-411f-bf64-2070d2735699.png){: .align-center}

<br>

# histogram: 도수분포표

히스토그램은 도수분포표를 그래프로 나타낸 것으로서, 가로축은 계급, 세로축은 횟수 또는 개수를 나타낸다. 히스토그램 그래프를 그리기 위해서는 `plt.hist(values)`를 사용한다.

`bins` parameter를 지정해서 히스토그램의 가로축 구간의 개수를 설정할 수 있다. 이 구간의 개수를 설정하느냐에 따라서 히스토그램 그래프의 결과는 달라지기 때문에 적절하게 설정하는 것이 중요하다.

```py
weight = [68, 81, 64, 56, 78, 74, 61, 77, 66, 68, 59, 71,
          80, 59, 67, 81, 69, 73, 69, 74, 70, 65]

# (1)
plt.hist(weight)

# (2)
plt.hist(weight, label='bins=10')
plt.hist(weight, bins=30, label='bins=30')
plt.legend()

plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/151979876-8239a801-a96f-4b57-98e4-2fc690ae603b.png){: width="49%"} ![temp](https://user-images.githubusercontent.com/91870042/151980503-2242e0c3-af31-40a3-b540-efe242065889.png){: width="49%"}

## 누적 히스토그램
누적 히스토그램을 그리기 위해서는 하나의 parameter를 추가로 지정하면 된다. `cumulative = True | False`로 지정해서 누적 히스토그램인지, 일반 히스토그램인지 구별해준다.


```py
weight = [68, 81, 64, 56, 78, 74, 61, 77, 66, 68, 59, 71,
          80, 59, 67, 81, 69, 73, 69, 74, 70, 65]

plt.hist(weight, cumulative=True, label='cumulative=True')
plt.hist(weight, cumulative=False, label='cumulative=False')
plt.legend(loc='upper left')
plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/151980783-a44c543c-c844-40ed-8524-6cbc7951c604.png){: .align-center}

## 난수 분포 히스토그램
`numpy.random`에서 각 함수들이 어떤 분포를 가지고 있는지 직접 눈으로 확인해볼 수 있다. 일반적으로 많이 사용하는 `randn`, `standard_normal`, `rand`를 사용해서 각 그래프가 어떤 분포를 갖고 있는지 확인해보자.

```py
a = 2.0 * np.random.randn(10000) + 1.0
b = np.random.standard_normal(10000)
c = 20.0 * np.random.rand(5000) - 10.0

# Graph A: 표준편차 2.0, 평균 1.0을 갖는 그래프 생성
plt.hist(a, bins=100, density=True, alpha=0.7, histtype='step')
# Graph B: 표준정규분포
plt.hist(b, bins=50, density=True, alpha=0.5, histtype='stepfilled')
# Graph C: [-10.0, 10.0] 사이의 균일한 분포를 갖는 5000개의 값
plt.hist(c, bins=100, density=True, alpha=0.9, histtype='step')

plt.show()
```

![temp](https://user-images.githubusercontent.com/91870042/151981546-48d8223c-d952-4577-b5ca-249985838d78.png){: .align-center}

<br>

# References

[📘 Matplotlib Tutorial - 파이썬으로 데이터 시각화하기 - Wiki docs](https://wikidocs.net/book/5011)

