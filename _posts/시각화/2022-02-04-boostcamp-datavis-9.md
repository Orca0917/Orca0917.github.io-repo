---
title:  "[Data Visualization] More Tips"
excerpt: "다양한 요소를 목적에 맞고 미적으로 더 나은 시각화를 만드는 방법과 텍스트, 섹, Facet을 다루는 방법"

categories:
  - datavis
tags:
  - [AI, Naver, BoostCamp, Python, Matplotlib]
toc: true
toc_sticky: true
 
date: 2022-02-04 03:00:00
last_modified_at: 2022-02-04 03:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Grid 이해하기
## Default Grid
기본적인 `Grid`는 축과 평행한 선을 사용하여 거리 및 값의 정보를 보조적으로 제공한다. 그리드의 색상은 위에 그려질 그래프의 색상에 방해되면 안되기 때문에 검정색보다는 회색의 **무채색 색상**을 사용한다. 그래프의 위에 보이지 않도로 `zorder`를 맨 밑으로 가도록 조정해야한다.

그리드의 격자판이 어떤 눈금을 기준으로 생성할지 `which`파라미터를 통해서 지정할 수 있다. (major, minor, both) X축, Y축 각각에 대해 그리드를 생성하여 격자판이 만들어지는 것이 아니라 X축 하나 또는 Y축 하나에만 그리드를 `axis`파라미터를 통해서 직접 지정할 수 있다 (x, y, both)

## 다양한 타입의 Grid
위의 X축이나 Y축에 평행만 그리드 뿐만아니라 다양한 형태의 그리드가 존재한다.

- \\(x + y=c\\) : 두 변수의 합이 중요한 경우
- \\(y = cx\\) : 비율이 중요한 경우
- \\(xy = c\\) : 두 변수의 곱이 중요한 경우
- \\((x-\hat{x})^2 + (y-\hat{y})^2\\) : 특정 데이터를 중심으로 보고 싶은 경우 (동심원)

각 grid에 대한 모습은 아래에서 실습부분에서 다시 다룬다.

<br>

# 심플한 처리
## 선 추가하기
`axvlin()`, `axhline()`과 같은 것을 통해서 간단한 직선을 추가할 수 있고, 원하는 정보를 추가하기 위해서 여러가지 스타일을 적용해볼 수 있다. 다음은 시그모이드 함수의 여러가지 정보를 추가한 모습이다.

![image](https://user-images.githubusercontent.com/91870042/152637977-b2f8ec01-ad36-49df-9092-9d4e66a1c173.png){: .align-center width="70%"}

## 면 추가하기
면을 추가하기 위해서는 `axvspan()`, `axhspan()` 함수를 사용한다. 이 면을 추가하게 되면 보조적인 정보를 제공하여 원하는 부분만 한눈에 들어오게 할 수 있다. 다음은 각 영화에 대해서 상영 등급을 시각적으로 보여주는 자료이다.

![image](https://user-images.githubusercontent.com/91870042/152638041-b879d94e-83b1-4cc0-9cb4-9caeabf1f38f.png){: .align-center}

<br>

# Setting: Theme
테마를 설정하면, 일일이 스타일 설정을 바꾸지 않고 테마를 바꾸어서 전체적인 색상이나 스타일을 변경할 수 있다. 자주 사용하는 테마는 다음 3가지가 있다.

- `default`
- `fivethirtyeight`
- `ggplot` : R에서 사용하는 디자인
  
![image](https://user-images.githubusercontent.com/91870042/152638070-e198383d-43ed-41dc-b171-b5bc1197233d.png){: .align-center width="70%"}

<br>

# 실습

## Grid
Grid에서 많이 사용하는 parameters.

- `which` : 어떤 눈금을 기준으로 grid를 생성할 것인지
- `axis` : 어떤 축에 대해서 grid를 생성할 것인지
- `linestyle` : grid로 생성되는 선의 종류를 결정
- `linewidth` : grid로 생성되는 선의 두께를 결정
- `zorder` : grid가 표시될 z-order를 결정 일반적으로 맨뒤로 설정한다.

```py
fig, ax = plt.subplots()
ax.grid()
plt.show()
```
![image](https://user-images.githubusercontent.com/91870042/152638135-aebff94d-65ee-41bb-9674-5c8bc57eb07d.png){: .align-center width="70%"}

```py
np.random.seed(970725)

x = np.random.rand(20)
y = np.random.rand(20)

fig = plt.figure(figsize=(16, 7))
ax = fig.add_subplot(1, 1, 1, aspect=1)

ax.scatter(x, y, s=150, 
           c='#1ABDE9',
           linewidth=1.5,
           edgecolor='black', zorder=10)

# x축의 눈금에 minor한 눈금을 추가한다.
ax.set_xticks(np.linspace(0, 1.1, 12, endpoint=True), minor=True)

ax.set_xlim(0, 1.1)
ax.set_ylim(0, 1.1)
    
ax.grid(zorder=0, linestyle='--') # zorder를 설정해서 scatter와 겹치지 않게 한다.
ax.set_title(f"Default Grid", fontsize=15,va= 'center', fontweight='semibold')

plt.tight_layout()
plt.show()
```
![image](https://user-images.githubusercontent.com/91870042/152638232-d6fe85ba-9418-4570-a1f9-a55205faae35.png){: .align-center width="70%"}

scatter와 grid의 상성이 안좋은데, 그 이유는 scatter로 표현된 점이 커서 grid위에서 정확히 어디에 분포한지 알아보기 힘들기 때문이다. 위의 plot을 자세히 보면 x축에 `minor tick`을 사용해서 작은 눈금이 생긴것을 확인할 수 있다.

- \\(x + y=c\\) : 두 변수의 합이 중요한 경우

  ```py
  fig = plt.figure(figsize=(16, 7))
  ax = fig.add_subplot(1, 1, 1, aspect=1)

  # 지정한 축을 기준으로 내부에 있는것은 파란색, 외부에 있는 것은 회색으로 표현
  # x + y < 1.0 인것들만 파란색으로 표현하기
  ax.scatter(x, y, s=150, 
            c=['#1ABDE9' if xx+yy < 1.0 else 'darkgray' for xx, yy in zip(x, y)],
            linewidth=1.5,
            edgecolor='black', zorder=10)

  ## Grid Part
  x_start = np.linspace(0, 2.2, 12, endpoint=True)

  # 대각 Grid생성. 
  for xs in x_start:
      ax.plot([xs, 0], [0, xs], linestyle='--', color='gray', alpha=0.5, linewidth=1)


  ax.set_xlim(0, 1.1)
  ax.set_ylim(0, 1.1)

  ax.set_title(r"Grid ($x+y=c$)", fontsize=15,va= 'center', fontweight='semibold')

  plt.tight_layout()
  plt.show()
  ```
  ![image](https://user-images.githubusercontent.com/91870042/152638315-968bcd05-7f62-444f-aad0-e2160904de06.png){: .align-center width="70%"}

- \\(y = cx\\) : 비율이 중요한 경우

  ```py
  fig = plt.figure(figsize=(16, 7))
  ax = fig.add_subplot(1, 1, 1, aspect=1)

  ax.scatter(x, y, s=150, 
            c=['#1ABDE9' if yy/xx >= 1.0 else 'darkgray' for xx, yy in zip(x, y)],
            linewidth=1.5,
            edgecolor='black', zorder=10)

  ## Grid Part : 10등분해서 radian구간 생성하기
  radian = np.linspace(0, np.pi/2, 11, endpoint=True)

  for rad in radian:
      ax.plot([0,2], [0, 2*np.tan(rad)], linestyle='--', color='gray', alpha=0.5, linewidth=1)


  ax.set_xlim(0, 1.1)
  ax.set_ylim(0, 1.1)

  ax.set_title(r"Grid ($y=cx$)", fontsize=15,va= 'center', fontweight='semibold')

  plt.tight_layout()
  plt.show()
  ```

  ![image](https://user-images.githubusercontent.com/91870042/152638445-baf0fcf5-e2ed-47af-821d-8f94bf25cfe8.png){: .align-center width="70%"}


  각 지점을 계산하는 방법: grid간의 각도를 \\(\theta\\)라고 했을 때, `tan`를 이용해서 구할 수 있다.

- \\((x-\hat{x})^2 + (y-\hat{y})^2\\) : 특정 데이터를 중심으로 보고 싶은 경우 (동심원)

  ```py
  fig = plt.figure(figsize=(16, 7))
  ax = fig.add_subplot(1, 1, 1, aspect=1)

  ax.scatter(x, y, s=150, 
            c=['darkgray' if i!=2 else '#1ABDE9'  for i in range(20)] ,
            linewidth=1.5,
            edgecolor='black', zorder=10)

  ## Grid Part
  rs = np.linspace(0.1, 0.8, 8, endpoint=True)

  for r in rs:
      # 과거 lineplot 포스팅에서 원을 그리는 방법을 그대로 가져와서 사용
      xx = r*np.cos(np.linspace(0, 2*np.pi, 100))
      yy = r*np.sin(np.linspace(0, 2*np.pi, 100))
      ax.plot(xx+x[2], yy+y[2], linestyle='--', color='gray', alpha=0.5, linewidth=1)

      # 오른쪽 45도 아래에 텍스트로 거리감을 표시한다.
      ax.text(x[2]+r*np.cos(np.pi/4), y[2]-r*np.sin(np.pi/4), f'{r:.1}', color='gray')

  ax.set_xlim(0, 1.1)
  ax.set_ylim(0, 1.1)

  ax.set_title(r"Grid ($(x-x')^2+(y-y')^2=c$)", fontsize=15,va= 'center', fontweight='semibold')

  plt.tight_layout()
  plt.show()
  ```
  ![image](https://user-images.githubusercontent.com/91870042/152638492-31be51f5-0670-461b-9b54-dd7ec006e949.png){: .align-center width="70%"}

## Line & Span
단일 직선을 그리기 위해서 다음 2가지 함수를 사용할 수 있다.
- `axvline()` : 수직선을 그릴 때 사용
- `axhline()` : 수평선을 그릴 때 사용

```py
fig, ax = plt.subplots()

ax.set_aspect(1)
ax.axvline(0, color='red')
ax.axhline(0, color='green')

# 0을 기준으로 수직선을 생성하되, y축 구간을 지정하는 경우
# ax.axvline(0, ymin=0.3, ymax=0.7, color='red')

ax.set_xlim(-1, 1)
ax.set_ylim(-1, 1)

plt.show()
```
![img1](https://user-images.githubusercontent.com/91870042/152638595-00969733-bc40-489c-87a1-3130cdd82c9f.png){: width="49%"} ![img2](https://user-images.githubusercontent.com/91870042/152638625-99838fcb-891d-4f13-92ee-41dc823c1050.png){: width="49%"}

오른쪽 그래프에서 axvline으로 y축의 구간을 지정해서 선분을 생성하기 보다 `plot()`을 사용해서 생성하는 편이 더 간단하다.

```py
# 두 과목의 평균이상을 받은 학생만 표시
fig, ax = plt.subplots(figsize=(10, 10))
ax.set_aspect(1)

math_mean = student['math score'].mean()
reading_mean = student['reading score'].mean()

ax.axvline(math_mean, color='gray', linestyle='--')
ax.axhline(reading_mean, color='gray', linestyle='--')

ax.scatter(x=student['math score'], y=student['reading score'],
           alpha=0.5,
           color=['royalblue' if m>math_mean and r>reading_mean else 'gray'  for m, r in zip(student['math score'], student['reading score'])],
           zorder=10,
          )

ax.set_xlabel('Math')
ax.set_ylabel('Reading')

ax.set_xlim(-3, 103)
ax.set_ylim(-3, 103)
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/152638715-439b3892-e5a3-4bb8-bf97-1e558eb6925a.png){: .align-center width="70%"}

그래프에서 면적을 생성하기 위해서는 다음 2가지 합수를 사용할 수 있다.
- `axvspan()` : Y축에 평행한 면적 생성
- `axhspan()` : X축에 평행한 면적 생성
위에서 했던 것과 비슷하게 x축이나 y축의 구간을 설정하게 되면 원하는 구간만큼의 면적을 생성할 수 있다.

```py
fig, ax = plt.subplots()

ax.set_aspect(1)
ax.axvspan(0,0.5, color='red')
ax.axhspan(0,0.5, color='green')

# 원하는 구간의 면적만 생성하기
# ax.axvspan(0,0.5, ymin=0.3, ymax=0.7, color='red')

ax.set_xlim(-1, 1)
ax.set_ylim(-1, 1)

plt.show()
```
![img1](https://user-images.githubusercontent.com/91870042/152638821-b880ab7d-11e8-4300-adba-8e14465a9864.png){: width="49%"} ![img2](https://user-images.githubusercontent.com/91870042/152638832-700aa444-5e94-445e-9ed0-59a80f436b4e.png){: width="49%"}

이 `span`은 일반적으로 특정 부분을 강조하는 방법으로 사용하지만, 오히려 특정 부분의 주의를 없앨 수 도 있다. 다음 코드는 두 과목의 점수를 모두 평균 이상으로 받은 학생만 강조하게 보이도록 하는 코드이다.

```py
fig, ax = plt.subplots(figsize=(8, 8))
ax.set_aspect(1)

math_mean = student['math score'].mean()
reading_mean = student['reading score'].mean()

# span을 생성하고 경계선을 점선으로 표시한다.
ax.axvspan(-3, math_mean, color='gray', linestyle='--', zorder=0, alpha=0.3)
ax.axhspan(-3, reading_mean, color='gray', linestyle='--', zorder=0, alpha=0.3)

ax.scatter(x=student['math score'], y=student['reading score'],
           alpha=0.4, s=20,
           color=['royalblue' if m>math_mean and r>reading_mean else 'gray'  for m, r in zip(student['math score'], student['reading score'])],
           zorder=10,
          )

ax.set_xlabel('Math')
ax.set_ylabel('Reading')

ax.set_xlim(-3, 103)
ax.set_ylim(-3, 103)
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/152638920-bfbb86fe-8aaa-49a6-9458-d7ba3ac5b21e.png){: .align-center width="70%"}

## Spines
- `set_visible` : 테두리를 지정해서 보이거나 보이지 않게 만들 수 있다.
- `set_linewidth` : 축의 테두리를 두껍게 만들 수 있다.
- `set_position` : 축의 위치를 바꾸서어 수직선을 생성할 수 있다.

```py
fig = plt.figure(figsize=(12, 6))

_ = fig.add_subplot(1,2,1)
ax = fig.add_subplot(1,2,2)
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)
ax.spines['left'].set_linewidth(1.5)
ax.spines['bottom'].set_linewidth(1.5)
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/152639052-a01713e8-a13d-47d7-9241-08d22a18f92b.png){: .align-center width="70%"}

축을 이동하는 경우에 기준점으로 삼는 방법에는 2가지 방법이 있다.

- 데이터를 기준으로 축 옮기기
- 축을 기준으로 축 옮기기

```py
ax2.spines['left'].set_position(('data', 0.3)) # 0.3구간에 축을 생성 (Y축)
ax2.spines['bottom'].set_position(('axes', 0.2)) # 축의 비율을 기준 20%, 80% (X축)
```

![935c5d07-4de4-4351-a2ad-c26bd50db639](https://user-images.githubusercontent.com/91870042/152639122-e165ad6b-d760-4779-b42e-f6a607e4986e.png){: .align-center width="70%"}

## Settings

참고: [Customizing Matplotlib with style sheets and rcParams](https://matplotlib.org/stable/tutorials/introductory/customizing.html)

- 기본 스타일 변경
  
  ```py
  # 기본 선의 스타일을 변경한다.
  # 따로 스타일을 지정하지 않고, plot을해도 지정한 스타일이 적용된다.
  plt.rcParams['lines.linewidth'] = 2
  plt.rcParams['lines.linestyle'] = ':'

  # 위의 문법과 동일한 문법 (plt, mpl둘다 사용해도 좋다)
  plt.rc('lines', linewidth=2, linestyle=':')
  mpl.rc('lines', linewidth=2, linestyle=':')
  ```

- 테마 변경 : 보편적으로 테마는 `ggplot`, `seaborn`, `fivethirtyeight`를 많이 사용한다.

  ```py
  # 사용가능한 테마 보기
  print(mpl.style.available)

  # seaborn스타일의 테마 설정하기
  mpl.style.use('seaborn')

  # 하나의 그래프에 대해서만 스타일을 변경하는 방법
  with plt.style.context('fivethirtyeight'):
    plt.plot(np.sin(np.linspace(0, 2 * np.pi)))
  plt.show()
  ```

