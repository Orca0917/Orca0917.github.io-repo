---
title:  "matplotlib: 데이터 시각화 (3)"
excerpt: "matplotlib의 errorbar, pie chart, heatmap(matshow), subplot, colormap, text"

categories:
  - matplotlib
tags:
  - [Python, Matplotlib]

toc: true
toc_sticky: true
 
date: 2022-02-02 00:00:00
last_modified_at: 2022-02-02 00:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **Wikidocs의 Matplotlib Tutorial - 파이썬으로 데이터 시각화하기**를 읽고 정리하는 글입니다.<br>
더 자세한 설명은 해당 [문서](https://wikidocs.net/book/5011)에 더 상세하게 설명되어 있습니다.
{: .notice--info}

# errorbar: 오차막대
에러바(Errorbar, 오차막대)는 데이터의 편차를 표시하기 위한 그래프형태로서 `plt.errorbar(x, y, yerr=yerr)`을 사용해서 나타낼 수 있다. yerr의 값은 기본적으로, 데이터의 위 아래의 대칭으로 오차가 표시된다. 대칭값이 아닌 비대칭인 편차를 설정하려면 오차의 값을 [(아래 오차), (위 오차)]의 형태로 값을 대입한다.

```py
x = [1, 2, 3, 4]
y = [1, 4, 9, 16]
yerr = [2.3, 3.1, 1.7, 2.5]
# yerr = [(2.3, 3.1, 1.7, 2.5), (1.1, 2.5, 0.9, 3.9)]

plt.errorbar(x, y, yerr=yerr)
plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152116848-bb716ea8-dddf-42a3-81bb-63dc871029e1.png){: width="49%"} ![temp](https://user-images.githubusercontent.com/91870042/152117033-8da4cac2-6d95-448a-9505-090351b25cac.png){: width="49%"}


<br>

# pie chart: 원 그래프
파이 차트는 원그래프로서 범주별 구성 비율을 원형으로 나타낸 그래프이다. 부채꼴의 중심각을 구성 비율에 비례하도록 표현하며 `plt.pie()`를 통해서 나타낼 수 있다.

파이차트를 만들기 위해서 각 영역의 비율(ratio)와 이름(label)을 지정하고 `pie()`함수에 인자로 순서대로 전달한다. `autopct`를 사용해서 부채꼴 안에서 표시될 숫자의 형식을 지정할 수 있다.

```py
ratio = [34, 32, 16, 18]
labels = ['Apple', 'Banana', 'Melon', 'Grapes']

plt.pie(ratio, labels=labels, autopct='%.1f%%')
plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152117693-5e1cec23-193c-4679-8cda-fa25042373b4.png){: .align-center}

- `explode`: 속성으로 explode값을 설정하게 되면 부채꼴이 중심에서 벗어나게 만들 수 있다.
- `shadow`: 부채꼴의 그림자를 표시한다.
- `colors`: 각 영역의 색상을 직접 지정할 수 있다.
- `wedgeprops`: 부채꼴 영역의 스타일을 지정할 수 있다.

```py
# 1. explode 설정
ratio = [34, 32, 16, 18]
labels = ['Apple', 'Banana', 'Melon', 'Grapes']
explode = [0, 0, 0, 0.20]

plt.pie(ratio, labels=labels, autopct='%.1f%%', startangle=260, counterclock=False, explode=explode)
plt.show()

# 2. shadow 설정
ratio = [34, 32, 16, 18]
labels = ['Apple', 'Banana', 'Melon', 'Grapes']
explode = [0, 0, 0, 0.20]

plt.pie(ratio, labels=labels, autopct='%.1f%%', startangle=260, counterclock=False, explode=explode, shadow=True)
plt.show()

# 3. colors 색상지정
ratio = [34, 32, 16, 18]
labels = ['Apple', 'Banana', 'Melon', 'Grapes']
explode = [0.05, 0.05, 0.05, 0.05]
colors = ['silver', 'gold', 'whitesmoke', 'lightgray']

plt.pie(ratio, labels=labels, autopct='%.1f%%', startangle=260, counterclock=False, explode=explode, shadow=True, colors=colors)
plt.show()

# 4. wedgeprops: 부채꼴 스타일 지정하기
ratio = [34, 32, 16, 18]
labels = ['Apple', 'Banana', 'Melon', 'Grapes']
colors = ['#ff9999', '#ffc000', '#8fd9b6', '#d395d0']
wedgeprops={'width': 0.7, 'edgecolor': 'w', 'linewidth': 5}

plt.pie(ratio, labels=labels, autopct='%.1f%%', startangle=260, counterclock=False, colors=colors, wedgeprops=wedgeprops)
plt.show()
```
![explode](https://user-images.githubusercontent.com/91870042/152118255-5158c9ba-e875-47e9-98e0-fa4975f1ac5b.png){: width="49%"} ![shadow](https://user-images.githubusercontent.com/91870042/152118380-3098b070-5442-4976-b8b9-2833d1b04a12.png){: width="49%"}

![colors](https://user-images.githubusercontent.com/91870042/152118500-45582dad-ae29-4c7b-bc81-97628a67806b.png){: width="49%"} ![temp](https://user-images.githubusercontent.com/91870042/152118704-28b0e169-147a-4089-9cc3-b4a3049cd271.png){: width="49%"}

<br>

# heatmap: 히트맵
히트맵은 다양한 값을 갖는 숫자 데이터를 색상을 이용해서 시각화한 것이다. 히트맵은 `plt.matshow()`를 이용해서 2차원 배열 형태의 숫자 데이터를 히트맵으로 표현한다.

```py
arr = np.random.standard_normal((30, 40))

plt.matshow(arr)
# 컬러바 표현 
plt.colorbar()
plt.show()
```
![heatmap](https://user-images.githubusercontent.com/91870042/152122632-1dbce6fe-cd0b-4b1b-890f-5cc0385db098.png){: width="49%"} ![heatmap2](https://user-images.githubusercontent.com/91870042/152123042-edadf99d-7218-4626-b838-87fc0a937966.png){: width="49%"}

<br>

# subplot: 여러개의 그래프 표현
`subplot(row, col, index)`을 사용하면 여러개의 그래프를 하나의 그림으로 나타날 수 있게 만들어준다.

```py
# 1. 2 x 1의 그래프 공간 생성
x1 = np.linspace(0.0, 5.0) # start, end사이의 50개의 수 생성 (default) 
x2 = np.linspace(0.0, 2.0)

y1 = np.cos(2 * np.pi * x1) * np.exp(-x1)
y2 = np.cos(2 * np.pi * x2)

plt.subplot(2, 1, 1)                # nrows=2, ncols=1, index=1
plt.plot(x1, y1, 'o-')
plt.title('1st Graph')
plt.ylabel('Damped oscillation')

plt.subplot(2, 1, 2)                # nrows=2, ncols=1, index=2
plt.plot(x2, y2, '.-')
plt.title('2nd Graph')
plt.xlabel('time (s)')
plt.ylabel('Undamped')

plt.tight_layout()
plt.show()

# 2. 1 x 2의 그래프 공간 생성
# something ...
```
![temp](https://user-images.githubusercontent.com/91870042/152124954-cd345e40-49f4-49ed-b099-e850b4e1eaf6.png){: width="49%"} ![temp](https://user-images.githubusercontent.com/91870042/152125267-7425e00e-9437-4dab-9e79-8c276b9d9754.png){: width="49%"}

- `sharex`: 2개의 그래프가 하나의 축을 공유하도록 설정

```py
x1 = np.linspace(0.0, 5.0)
x2 = np.linspace(0.0, 2.0)

y1 = np.cos(2 * np.pi * x1) * np.exp(-x1)
y2 = np.cos(2 * np.pi * x2)

ax1 = plt.subplot(2, 1, 1)                # nrows=2, ncols=1, index=1
plt.plot(x1, y1, 'o-')
plt.ylabel('Damped oscillation')
plt.xticks(visible=False)

ax2 = plt.subplot(2, 1, 2, sharex=ax1)    # nrows=2, ncols=1, index=2
plt.plot(x2, y2, '.-')
plt.xlabel('time (s)')
plt.ylabel('Undamped')

plt.tight_layout()
plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152126129-a86883ab-5c87-421b-a2f8-152144e4536b.png){: .align-center}

<br>

# colormap
maplotlib에서 기본적으로 적용하는 컬러맵의 일부.
![image](https://user-images.githubusercontent.com/91870042/152126820-92a9c29f-1057-47a9-b6f1-0aecd5d2c372.png){: .align-center}

```py
np.random.seed(0)
arr = np.random.standard_normal((8, 100))

plt.subplot(2, 2, 1)
# plt.scatter(arr[0], arr[1], c=arr[1], cmap='spring')
plt.scatter(arr[0], arr[1], c=arr[1])
plt.spring()
plt.title('spring')

plt.subplot(2, 2, 2)
plt.scatter(arr[2], arr[3], c=arr[3])
plt.summer()
plt.title('summer')

plt.subplot(2, 2, 3)
plt.scatter(arr[4], arr[5], c=arr[5])
plt.autumn()
plt.title('autumn')

plt.subplot(2, 2, 4)
plt.scatter(arr[6], arr[7], c=arr[7])
plt.winter()
plt.title('winter')

plt.tight_layout()
plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152126710-1b9b5ace-4c37-4299-99e0-e226ad6e1b67.png){: .align-center}

확인할 수 있는 모든 컬러맵은 다음 코드로 확인할 수 있다.

```py
cmaps = plt.colormaps()
for cm in cmaps:
    print(cm)
```

<br>

# text: 텍스트 삽입
`text(xpos, ypos, 'text')`는 그래프의 적절한 위치에 텍스트를 삽입하도록 만들어준다.

- `fontdict`: 텍스트의 스타일 지정
- `rotation`: 텍스트의 회전
- `bbox`: 텍스트 상자 스타일 지정

```py
a = 2.0 * np.random.randn(10000) + 1.0
b = np.random.standard_normal(10000)
c = 20.0 * np.random.rand(5000) - 10.0

font1 = {'family': 'serif',
        'color':  'darkred',
        'weight': 'normal',
        'size': 16}

font2 = {'family': 'Times New Roman',
        'color':  'blue',
        'weight': 'bold',
        'size': 12,
        'alpha': 0.7}

font3 = {'family': 'Arial',
        'color':  'forestgreen',
        'style': 'italic',
        'size': 14}

box1 = {'boxstyle': 'round',
        'ec': (1.0, 0.5, 0.5),
        'fc': (1.0, 0.8, 0.8)}

box2 = {'boxstyle': 'square',
        'ec': (0.5, 0.5, 1.0),
        'fc': (0.8, 0.8, 1.0),
        'linestyle': '--'}

box3 = {'boxstyle': 'square',
        'ec': (0.3, 1.0, 0.5),
        'fc': (0.8, 1.0, 0.5),
        'linestyle': '-.',
        'linewidth': 2}

plt.hist(a, bins=100, density=True, alpha=0.7, histtype='step')
plt.text(-3.0, 0.15, 'np.random.randn()', fontdict=font1, rotation=85, bbox=box1)
plt.hist(b, bins=50, density=True, alpha=0.5, histtype='stepfilled')
plt.text(2.0, 0.0, 'np.random.standard_normal()', fontdict=font2, rotation=-60, bbox=box2)
plt.hist(c, bins=100, density=True, alpha=0.9, histtype='step')
plt.text(-10.0, 0.08, 'np.random.rand()', fontdict=font3, bbox=box3)
plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152140878-139a5c1a-ec5b-4363-a8c8-041338148711.png){: width="49%"} ![temp](https://user-images.githubusercontent.com/91870042/152141029-05eb70a6-8ff9-492b-ba4f-2c85dad7cec5.png){: width="49%"}

## LATEX
위의 텍스트에서 수식을 표현하기 위해서는 `latex`문법을 사용해서 나타낼 수 있다. 텍스트가 들어갈 자리에 `r'$ 수식 $'`으로 작성하면 latex문법으로 표현된 수식이 보여진다.

```py
plt.plot([1, 2, 3, 4], [1, 4, 9, 16])
plt.xlabel('X-Label')
plt.ylabel('Y-Label')
plt.text(1, 15, r'$\alpha > \beta$', fontdict={'size': 16})

plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152141844-c6f96ece-890c-45c6-abaa-ed75b5320ce0.png){: .align-center}

<br>

# References

[📘 Matplotlib Tutorial - 파이썬으로 데이터 시각화하기 - Wiki docs](https://wikidocs.net/book/5011)