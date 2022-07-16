---
title:  "[Data Visualization] Line plot"
excerpt: "matplotlib의 Line plot을 사용한 연속적인 데이터의 분석"

categories:
  - datavis
tags:
  - [AI, Naver, BoostCamp, Python, Matplotlib]
toc: true
toc_sticky: true
 
date: 2022-02-03 03:00:00
last_modified_at: 2022-02-03 03:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# 기본 Line plot
## Line plot
연속적이고 변화하는 값을 순서대로 점으로 나타내고, 이를 선으로 연결한 그래프로서 꺾은선 그래프라고도 말한다. 왼쪽에서 오른쪽으로 읽기 때문에 변화, 기울기, 차이로 해석할 수 있다. 그렇기에 시계열 분석에 특화된 그래프이다.

```py
fig, axes = plt.subplots(1, 2, figsize=(12, 7))

x1 = [1, 2, 3, 4, 5]
x2 = [1, 3, 2, 4, 5]
y = [1, 3, 2, 1, 5]

axes[0].plot(x1, y)
axes[1].plot(x2, y)

plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152350268-426b41ca-b6cf-4fe8-ba56-b1a00ff8aea8.png){: .align-center}

위의 그래프를 보면, 각 x절편 값과 y절편 값이 대응되어서 그려지는 것을 볼 수 있다. 이 성질을 이용해서 정N각형이나 원을 그리는 것도 가능하다.

```py
fig = plt.figure(figsize=(5, 5))
ax = fig.add_subplot(111, aspect=1)

n = 11 # n - 1각형 그리기
x = np.sin(np.linspace(0, 2*np.pi, n))
y = np.cos(np.linspace(0, 2*np.pi, n))

ax.plot(x, y)

plt.show()
```

## Line plot의 요소
너무 많은 선을 사용하게 되면, 중첩으로 인한 가독성이 하락하기 때문에 5개 이하의 선을 사용하는 것이 좋다. 각각의 선을 구분하기 위해서 **색상, 마커, 선의 종류**를 사용한다.

```py
fig, ax = plt.subplots(1, 1, figsize=(5, 5))

np.random.seed(97)
x = np.arange(7)
y = np.random.rand(7)

ax.plot(x, y,
        color='gold',
        marker='*',
        linestyle='--', 
       )

plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152350529-e5436be5-83df-407a-afea-035126411257.png){: .align-center}


## Line plot을 위한 전처리
시시각각 변동하는 시계열 데이터는 noise로 인해서 패턴 및 추세를 파악하기가 어렵다. 그렇기 때문에 noise의 인지적인 방해를 줄이기 위해서 **smoothing기법**을 사용한다. → `moving average`

### 실습: 미국의 주가정보
```py
stock = pd.read_csv('./prices.csv')
stock
stock['date'] = pd.to_datetime(stock['date'], format='%Y-%m-%d', errors='raise')
stock.set_index("date", inplace = True)
```

데이터의 시간(날짜) 형식이 모두 통일되어 있지 않기 때문에 `년-월-일` 로 통일을 해서 저장한다. 또한, index값이 시간이 될 수 있도록 `set_index` 를 사용한다.

직접 보고자 하는 기업의 주가를 확인
```py
apple = stock[stock['symbol']=='AAPL']
google = stock[stock['symbol']=='GOOGL']
```

![temp](https://user-images.githubusercontent.com/91870042/152350901-520baaac-f64c-47dd-92e1-22c16fe3f072.png){: .align-center}

```py
# 이동평균 구하기
google_rolling = google.rolling(window=20).mean()

# dpi: 해상도
fig, axes = plt.subplots(2, 1, figsize=(12, 7), dpi=300, sharex=True)

axes[0].plot(google.index,google['close'])
axes[1].plot(google_rolling.index,google_rolling['close'])

plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152351032-0cd1a7c8-00be-4b7f-bb67-331f4a02f4d5.png){: .align-center}


# 정확한 Line plot
## 추세에 집중
잉크의 양 비례법칙에서 벗어나서 Bar plot가 다르게 꼭 축을 0에 초점을 둘 필요가 없다 → 추세가 목적이기 때문!

너무 구체적인 꺾은선 그래프보다는 생략된 꺾은선 그래프가 나을 수 있다. 그렇기 때문에 `grid`, `annotate` 와 같은 정보를 제거하고 디테일한 정보는 따로 표로 제공하는 것을 추천한다.

![temp](https://user-images.githubusercontent.com/91870042/152351216-ec3645af-c416-4cd3-8cf9-67a67bccea55.png){: .align-center}


## 간격
x축의 간격이 규칙적이지 않다면 독자의 입장으로서 오해를 받을 수 있다.

- 그래프 상에서 규칙적일 때: 기울기 정보의 오해
- 그래프 상에서 간격이 다를 때: 없는 데이터에 대해 있다고 오해 (4가 없는데 있는 것과 같음)

그렇기 때문에, 규칙적인 간격이 아니라면 관측 값에 점으로 표시해서 오해를 줄이는 것이 좋다.

![image](https://user-images.githubusercontent.com/91870042/152351371-7431b717-5e23-49c0-814f-4d7b0fd7663b.png){: .align-center}

## 보간
보간은 점과 점사이를 잇는 것을 말한다. 데이터의 noise 또는 error가 많은 경우 위에서 말한 moving average(이동평균)이나 scipy의 smooth curve를 사용할 수 있다.

- `scipy.interpolate.make_interp_spline()`
- `scipy.interpolate.interp1d()`
- `scipy.ndimage.gaussian_filter1d()`

Presentation을 하기 위해서 보간은 좋은 방법일 수 있지만 없는 데이터를 있다고 착각하게 만들 수 있고 작은 차이를 무시하는 경향이 있다. 따라서 일반적인 분석에서는 사용하지 않는 것이 좋다.

## 이중 축 사용
하나의 플롯에 대해서 2개의 축을 사용하는 것. (daul axis)

이중축을 사용하게 되는 이유

- 같은 시간 축에 대해 서로 다른 종류의 데이터를 표현하기 위해
- 한 데이터에 대해서 다른 단위를 표현하기 위해 (ex. rad, deg)

위의 첫번째 원인과 같은 경우에는 이중 축을 사용하는 것은 안좋다. 범위가 다른데 범위를 임의로 조정하게 되면 상관관계가 있어보이는 단점이 발생하기 때문이다. 실제로 이중축을 사용하기 보다 그래프를 분리해서 보여주는 것이 좋을 수 있다.

![temp](https://user-images.githubusercontent.com/91870042/152351812-1985bad1-2b9f-4b98-a696-a3f74a6d99c4.png){: .align-center}


## ETC
팁1 : 범례 대신에 라인 끝에 레이블을 추가하는 것이 식별에 도움이 된다.

```py

fig = plt.figure(figsize=(12, 5))

x = np.linspace(0, 2*np.pi, 1000)
y1 = np.sin(x)
y2 = np.cos(x)

# Ax2
ax = fig.add_subplot(111, aspect=1)
ax.plot(x, y1,
       color='#1ABDE9',
       linewidth=2,)

ax.plot(x, y2,
       color='#F36E8E',
       linewidth=2,)

ax.text(x[-1]+0.1, y1[-1], s='sin', fontweight='bold',
         va='center', ha='left', 
         bbox=dict(boxstyle='round,pad=0.3', fc='#1ABDE9', ec='black', alpha=0.3))

ax.text(x[-1]+0.1, y2[-1], s='cos', fontweight='bold',
         va='center', ha='left', 
         bbox=dict(boxstyle='round,pad=0.3', fc='#F36E8E', ec='black', alpha=0.3))


ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)

plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152351980-553788ba-d508-4aab-b85b-e6f4e76f7d9b.png){: .align-center}

팁2: Min/Max 정보 또는 원하는 정보를 추가해주면 도움이 될 수 있다. (annotation)

```py
fig = plt.figure(figsize=(7, 7))

np.random.seed(97)

x = np.arange(20)
y = np.random.rand(20)

ax = fig.add_subplot(111)
ax.plot(x, y,
       color='lightgray',
       linewidth=2,)

ax.set_xlim(-1, 21)

# max
ax.plot([-1, x[np.argmax(y)]], [np.max(y)]*2,
        linestyle='--', color='tomato'
       )

ax.scatter(x[np.argmax(y)], np.max(y), 
            c='tomato',s=50, zorder=20)

# min
ax.plot([-1, x[np.argmin(y)]], [np.min(y)]*2,
        linestyle='--', color='royalblue'
       )
ax.scatter(x[np.argmin(y)], np.min(y), 
            c='royalblue',s=50, zorder=20)

plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152352132-c299b0cd-d0c0-4cb6-ae7d-15f97b9d7a4e.png){: .align-center}

팁3: 불확실성을 area plot을 통해서 표시. 색상은 대비가 강한색 또는 연한색을 사용한다.

![image](https://user-images.githubusercontent.com/91870042/152352709-48a07a44-4313-495d-bbe5-a43818f356c8.png){: .align-center}