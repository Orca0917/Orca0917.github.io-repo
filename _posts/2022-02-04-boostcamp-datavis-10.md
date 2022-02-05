---
title:  "[Data Visualization] Seaborn 기초"
excerpt: "Seaborn의 기초문법과 기본적인 분류 5가지의 통계 시각화"

categories:
  - datavis
tags:
  - [AI, Naver, BoostCamp, Python, Matplotlib]
toc: true
toc_sticky: true
 
date: 2022-02-04 04:00:00
last_modified_at: 2022-02-04 04:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Seaborn의 구조

## 라이브러리와 데이터셋 호출
- 라이브러리 호출

    ```py
    import seaborn as sns
    print('seaborn version : ', sns.__version__)
    ```

- 데이터셋 호출 (StudentsPerformance.csv)

    ```py
    student = pd.read_csv('./StudentsPerformance.csv')
    student.head()
    ```

    ![image](https://user-images.githubusercontent.com/91870042/152640468-ad8a16ef-6746-4d11-b011-82eedaae6058.png){: .align-center widht="70%"}

## Countplot
`countplot`은 seaborn의 categorical API에서 대표적인 시각화로, 범주를 이산적으로 세서 막대 그래프로 보여주는 함수이다. 예를 들어, 위의 DataFrame에서 각 그룹에 속한 인원이 몇명인지 세서 보여줄 수 있다. 기본적으로 countplot에는 다음과 같은 파라미터가 있다.

- `x` : x축으로 전달될 인자
- `y` : y축으로 전달될 인자
- `data` : DataFrame을 전달
- `hue`, `hue_order` : 색을 의미하며, 데이터의 구분기준을 정해 색상을 통해 내용을 구분한다.
- `palette` : 그래프에서 사용할 색상 파레트를 지정
- `color` : 그래프에서 사용할 gradient한 색상을 지정
- `saturate` : 색상의 채도를 조정
- `ax`

### x, y, data
```py
sns.countplot(x='race/ethnicity', data=student) # x축을 기준으로 countplot생성
sns.countplot(y='race/ethnicity',data=student) # y축을 기준으로 countplot생성
# x축을 기준으로 countplot생성하되, group을 정렬해서 출력
sns.countplot(x='race/ethnicity',data=student,
             order=sorted(student['race/ethnicity'].unique())) 
```
![x축 기준](https://user-images.githubusercontent.com/91870042/152640731-88bb586b-05fa-4647-b30b-b44dbe190963.png){: width="32%"} ![y축 기준](https://user-images.githubusercontent.com/91870042/152640746-93658e0f-e008-4d9f-8be9-5132b3b8cf35.png){: width="32%"} ![정렬된것](https://user-images.githubusercontent.com/91870042/152640754-4191b513-e3cb-4cdf-b08e-7caeca8d37f3.png){: width="32%"}

위의 방법에서 `x`, `y`의 파라미터를 설정하는 것으로 방향이 바뀌지 않는다면, `oriented` 파라미터를 사용해서 세로로 보여주고 싶은 경우 `v`, 가로로 보여주고 싶은경우 `h`를 사용해야 한다.

### hue, palette, color, saturate
`hue`파라미터를 지정하면 Grouped Bar Chart를 시각화해서 보여줄 수 있다.

```py
# palette를 사용해서 사용할 색상을 지정
sns.countplot(x='race/ethnicity',data=student,
              hue='gender', palette='Set2')

# color를 지정할 경우, 하나의 색상에 대해서 gradient로 표시
sns.countplot(x='gender',data=student,
              hue='race/ethnicity', color='red')

# 색상으로 구분하게 되면 순서가 이상해질 수 있는데, 다시 hue_order를 사용해서 정렬한다.
sns.countplot(x='gender',data=student,
              hue='race/ethnicity', 
              hue_order=sorted(student['race/ethnicity'].unique()), color='red'
             )

# saturation을 사용하여 채도를 지정 - 많이 사용하지는 않는다.
sns.countplot(x='gender',data=student,
              hue='race/ethnicity', 
              hue_order=sorted(student['race/ethnicity'].unique()),
              saturation=0.3)
```
![hue 예제](https://user-images.githubusercontent.com/91870042/152640876-28139027-f3fe-4c8a-a0cd-1187ceba42fa.png){: width="49%"} ![hue 예제 gradient](https://user-images.githubusercontent.com/91870042/152640928-cde775ce-7451-41ca-829b-bf1d9ff563db.png){: width="49%"} 
![hue order 예제](https://user-images.githubusercontent.com/91870042/152640988-62d26a63-c875-4da0-85af-8d21c4b1411c.png){: width="49%"} ![채도 예제](https://user-images.githubusercontent.com/91870042/152641095-378e34fe-aa53-4700-bfcd-cc0a7828f508.png){: width="49%"}


### ax
하나의 figure내에 axes를 지정해서 여러개의 그래프를 표현할 수 있다.

```py
fig, axes = plt.subplots(1, 2, figsize=(12, 5))

sns.countplot(x='race/ethnicity',data=student,
              hue='gender', 
              ax=axes[0]
             )

sns.countplot(x='gender',data=student,
              hue='race/ethnicity', 
              hue_order=sorted(student['race/ethnicity'].unique()), 
              ax=axes[1]
             )

plt.show()
```
![ax결과](https://user-images.githubusercontent.com/91870042/152641147-5c2c76b1-5a48-4ded-8abd-c8d38c864737.png){: .align-center width="70%"}

## Categorical API
Categorical API를 사용하기에 앞서서 데이터의 통계량에는 어떤 것들이 있는지 `describe()`함수를 통해서 먼저 알아봐야 한다.

```py
student.describe()
```
![sutudent.describe 결과](https://user-images.githubusercontent.com/91870042/152641197-d6cc98e3-d449-4c1c-a81e-27b07089b626.png){: .align-center width="70%"}

- `count` : null값이 아닌 데이터의 개수
- `mean` : 데이터의 평균
- `std` : 데이터의 표준편차
- `min` : 데이터의 최소값
- `25%` : 데이터를 4등분하여 관측한 값 중 25%의 값 (lower quartile)
- `50%` : 데이터를 4등분하여 관측한 값 중 50%의 값 (median)
- `75%` : 데이터를 4등분하여 관측한 값 중 75%의 값 (upper quartile)
- `max` : 데이터의 최대값

### Box Plot
**Box Plot**, 박스플롯은 데이터의 분포를 살피는 대표적인 시각화 방법이다. 보여지는 사각형에서 박스 내부에 그어진 3개의 선은 **25%, 50%, 75%**를 의미한다.

```py
fig, ax = plt.subplots(1,1, figsize=(12, 5))
sns.boxplot(x='math score', data=student, ax=ax)
plt.show()
```
![Box plot](https://user-images.githubusercontent.com/91870042/152641348-e1d72081-ca63-4265-9373-b2fb02aebada.png){: .align-center width="70%"}

박스 양쪽으로 그어지는 선, 막대는 `whisker`라고 하며 데이터의 분포를 나타낸다. 그 길이는 박스의 길이(IQR)의 **1.5배**만큼의 직선을 긋는다. 그보다 더 벗어나는 데이터들은 점들로 표시하여 outlier 또는 이상치라고 표현한다. 

> IQR(InterQuartile Range) : 25% ~ 75%  
> Outlier: -IQR * 1.5와 IQR * 1.5를 벗어나는 값

하지만, 위의 선에서 왼쪽의 whisker선과 오른쪽의 whisker선의 길이가 다른 것을 볼 수 있다. 그 이유는 최댓값의 범위를 포함하고 있기 때문에, 오른쪽의 길이는 100이상으로 넘어가지 않게 설정이 되어 있다.

```py
# 1. 인종에 따른 수학성적 분포를 Boxplot으로 표현
fig, ax = plt.subplots(1,1, figsize=(10, 5))
sns.boxplot(x='race/ethnicity', y='math score', data=student, 
            order=sorted(student['race/ethnicity'].unique()),
            ax=ax)
plt.show()

# 2. Hue를 사용해서 gender로 나눈 경우
fig, ax = plt.subplots(1,1, figsize=(10, 5))
sns.boxplot(x='race/ethnicity', y='math score', data=student,
            hue='gender',
            order=sorted(student['race/ethnicity'].unique()),
            ax=ax)
plt.show()
```
![BoxPlot 예제1](https://user-images.githubusercontent.com/91870042/152641519-7be893b9-d4c4-4425-a906-0118226d3f71.png){: width="49%"} ![BoxPlot 예제1](https://user-images.githubusercontent.com/91870042/152641520-314f4c5e-7bb4-431b-8d16-0106478279a9.png){: width="49%"}

위의 분석 결과 전체적으로 모든 인종에서 남성이 여성보다 수학성적이 더 높다는 것을 알 수 있다. 그리고 Group E가 다른 Group에 비해서 수학성적이 높은 것도 볼 수 있다.

지금까지 그린 BoxPlot의 시각화를 커스텀하기 위해서 3가지 속성을 사용할 수 있다.
- `width` : 너비를 설정
- `linewidth` : 선의 두께를 설정
- `fliersize` : 이상치를 표시하는 점의 크기 설정

### Violin Plot
위에서 살펴본 Box Plot은 대표값을 보여주기에 적합하지만, 실제 분포를 표현하기에는 부족하다. 좀 더 자세히 말해 평균값이 어느 지점에 있다고 해서 전체 데이터가 고르게 있는지 아니면, 평균지점에 몰려서 존재하는지 알 수는 없었다.

하지만 이번에 알아볼 **ViolinPlot**은 IQR범위가 더불어서 전체 데이터의 분포를 알 수 있는 시각화방법이다. 중앙에 표시되는 흰색 점이 50% 범위를 말하며 두꺼운 검정색 막대가 IQR범위를 말한다.

```py
fig, ax = plt.subplots(1,1, figsize=(12, 5))
sns.violinplot(x='math score', data=student, ax=ax)
plt.show()
```
![바이올린 플롯](https://user-images.githubusercontent.com/91870042/152641709-7c0bc48f-ff42-46e7-bebe-3b2e073a9b10.png){: .align-center width="70%"}

지금까지 설명으로만 봐서는 데이터의 통계적 자료도 알 수 있고 전체 분포도 알 수 있는 정말 좋은 시각화 도구로서 보이지만, <u>바이올린 플롯은 오해가 생기기에 충분한 분포 표현 방식이다.</u>

1. 데이터는 실제로 연속적이지 않다. (점수가 51.12341와 같은 값을 지니는 것은 없다.)
   - 연속적인 데이터를 보여주기 위해서 `Kernel Density Estimate` 방식을 사용한다.
2. 연속적 표현에서 생기는 데이터의 손실과 오차가 존재한다.
3. 데이터의 범위가 없는 데이터까지 표시된다. (0점보다 낮은 점수, 100점보다 높은 점수가 있는 것 처럼 보인다.)

Violin Plot에서는 위와 같은 문제점을 해결하기 위해서 다음 3가지 파라미터를 통해서 오해를 줄여나간다.
- `bw` : 데이터의 분포 표현을 얼마나 자세하게 보여줄 것인지 (작은 숫자를 가질수록 더 자세히 표현)
- `cut` : 끝부분을 얼마나 자를 것인지
- `inner` : 내부를 어떻게 표현할 것인지 (box, quartile, stick, none)

```py
fig, ax = plt.subplots(1,1, figsize=(12, 5))
sns.violinplot(x='math score', data=student, ax=ax,
               bw=0.1,
               cut=0,
               inner='quartile' # box
              )
plt.show()
```
![파라미터를 조정한 바이올린 플롯](https://user-images.githubusercontent.com/91870042/152641916-b889496d-af10-4a93-b16f-807751ea847a.png){: .align-center width="70%"}

이번에는 데이터에서 hue를 사용해서 더 다양한 분포를 살펴보자. hue를 사용하면 각 group별로 바이올린 플롯을 나타내주는데 사실 여기서 바이올린 플롯은 한쪽만 있어도 알아볼 수 있기 때문에, 특정 파라미터를 주어서 효과적으로 표시할 수 있다.

- `split` : 바이올린의 한쪽만 표현
- `scale` : 각 바이올린 플롯을 그릴 때, 실제 데이터를 얼마나 반영할지 선택 (area, count, width)

```py
# hue를 사용한 기본 바이올린 플롯
fig, ax = plt.subplots(1,1, figsize=(12, 7))
sns.violinplot(x='race/ethnicity', y='math score', data=student, ax=ax,
               hue='gender', order=sorted(student['race/ethnicity'].unique())
              )
plt.show()

# split을 사용해서 hue의 각 바이올린 플롯을 합친다.
fig, ax = plt.subplots(1,1, figsize=(12, 7))
sns.violinplot(x='race/ethnicity', y='math score', data=student, ax=ax,
               order=sorted(student['race/ethnicity'].unique()),
               hue='gender',
               split=True,
               bw=0.2, cut=0
              )
plt.show()
```
![violinplot with hue](https://user-images.githubusercontent.com/91870042/152642097-81e75d2e-d994-4c42-a5e7-f96d7cc2ea0a.png){: width="49%"} ![violinplot with split](https://user-images.githubusercontent.com/91870042/152642100-866dd5a2-4e9f-4eaa-869d-6ef5d28c7c2c.png){: width="49%"}

### ETC
- `boxenplot`
- `swarmplot`
- `stripplot`

## Distirbution
### Univariate Distribution
하나의 feature에 대한 분포로 단일확률분포를 말한다. 이를 표현하는 방법으로는 4가지가 존재한다.

- `histplot` : 히스토그램 (막대그래프)
- `kdeplot` : Kernel Density Estimate
- `ecdfplot` : 누적 밀도 함수
- `rugplot` : 선을 사용한 밀도함수

```py
fig, axes = plt.subplots(2,2, figsize=(12, 10))
axes = axes.flatten()

sns.histplot(x='math score', data=student, ax=axes[0])
sns.kdeplot(x='math score', data=student, ax=axes[1])
sns.ecdfplot(x='math score', data=student, ax=axes[2])
sns.rugplot(x='math score', data=student, ax=axes[3])

plt.show()
```
![680ee673-c1a5-4c9b-95f6-dd1d43fcba58](https://user-images.githubusercontent.com/91870042/152642231-09694398-ad4b-48a0-a45a-06859535b9af.png){: .align-center width="70%"}

각각에 대해서 parameter와 함께 더 자세히 살펴보자.

#### histplot
**histplot**, 막대그래프의 막대개수나 간격을 조정하는 파라미터는 다음 2가지가 있다.
- `binwidth` : 막대의 너비를 결정
- `bins` : 막대의 개수를 결정

```py
fig, ax = plt.subplots(figsize=(12, 7))

sns.histplot(x='math score', data=student, ax=ax,
            #  binwidth=50, 
             bins=10,
)

plt.show()
```
`seaborn`에서는 histplot을 또 다른 형태로 표현할 수 있다. 기본적으로 막대그래프이지만 그 내부를 채워서 표현하는 `step`과 전체적인 분포를 곡선으로 나타내는 `poly`가 존재한다. 이 각각의 옵션은
`element` 파라미터로 설정한다.

```py
sns.histplot(x='math score', data=student, ax=ax,
             element='poly' # step, poly
            )
```
![poly](https://user-images.githubusercontent.com/91870042/152642386-6f0b2578-1f4c-45a6-b4ce-c4b13daad3f1.png){: width="49%"} ![step](https://user-images.githubusercontent.com/91870042/152642389-a85942cc-65e5-4cdc-bd83-d93c4c9e52e1.png){: width="49%"}

seaborn의 히스토그램은 여러개의 분포를 동시에 표현할 수 있다. `multiple`파라미터를 사용해서 지정하며 사용할 수 있는 옵션은 다음과 같다.
- `layer` : 투명도를 조절해서 겹치게 보이는 방법
- `dodge` : 이웃되게 표현하는 방법
- `stack` : 쌓아 올려서 표현하는 방법
- `fill` : Percentage Stacked bar chart로 각각의 분포가 얼마나 차지하는지를 알려준다.

```py
sns.histplot(x='math score', data=student, ax=axes[0][0],
             hue='gender', 
             multiple='layer', # layer, dodge, stack, fill
```
![multiple bar chart](https://user-images.githubusercontent.com/91870042/152642800-22423303-606d-4ea7-a457-87706e290598.png){: .align-center width="70%"}

#### kdeplot
**kdeplot**은 연속확률밀도를 보여주는 함수로 seaborn의 다양한 smoothing이나 분포시각화에 보조정보로 많이 사용된다.

```py
sns.kdeplot(x='math score', data=student, ax=ax)
sns.kdeplot(x='math score', data=student, ax=ax,
           fill=True) # 내부를 채워서 표현하는 방법
sns.kdeplot(x='math score', data=student, ax=ax,
           fill=True, bw_method=0.05) # 바이올린플롯에의 bw와 동일
```
![kdeplot](https://user-images.githubusercontent.com/91870042/152642905-db4874a5-f6ff-4c85-879e-b1c344971e29.png){: width="49%"} ![kdeplot filled](https://user-images.githubusercontent.com/91870042/152642907-aff605fb-1801-4e7a-8851-870ad716a04f.png){: width="49%"}

여러개의 연속활률밀도함수 그릴 때는 `hue`를 사용하면 된다. 여기서도 히스토그램과 비슷하게 stack, layer, fill 옵션을 설정할 수 있다. <u>fill옵션은 데이터의 왜곡이 생겨 오해를 불러일으킬 수 있기 때문에 조심히 사용해야 한다.</u>

```py
sns.kdeplot(x='math score', data=student, ax=axes[0][0],
            fill=True, 
            hue='race/ethnicity', 
            hue_order=sorted(student['race/ethnicity'].unique()),
            multiple="stack", # layer, stack, fill
            cumulative=False, # True
            cut=0
           )
```
![multiple kdeplot](https://user-images.githubusercontent.com/91870042/152643054-c55f2884-b0e4-449d-98b4-195281d7afe2.png){: .align-center}

#### ecdfplot
**ecdfplot**은 누적되는 양을 표현하는 방법이다. 위에서 살펴본 cumulative와 유사하다. 실제로 많이 사용되지는 않는 plot이다.

```py
fig, ax = plt.subplots(figsize=(12, 7))
sns.ecdfplot(x='math score', data=student, ax=ax,
             hue='gender',
             stat='count', # proportion
            #  complementary=True # 0에서 시작할지 1에서 시작할지 결정
            )
plt.show()
```
![ecdfplot](https://user-images.githubusercontent.com/91870042/152643193-a483d4d9-d237-47cd-a9ff-90ff6c1f37fc.png){: .align-center width="70%"}

#### rugplot
**rugplot**은 데이터가 얼마나 조밀하게 분포하는지 알아보기 위해 사용하는 방법이다. ecdfplot과 마찬가지로 많이 사용되지는 않는다.

```py
sns.rugplot(x='math score', data=student, ax=ax)
```
![rugplot](https://user-images.githubusercontent.com/91870042/152643243-1c9b1c3d-34af-4f94-a540-aad6b2132e6a.png){: .align-center width="70%"}



### Bivariate(Joint) Distribution
**Bivariate Distribution**은 결합분포를 확인하는 방법으로서 사용하는 함수는 `histplot()`과 `kdeplot()`을 사용한다. 지금까지는 인자에 x또는 y값만 전달을 해서 분포를 확인했는데, 결합분포는 x값과 y값 모두 전달해야 한다.

```py
fig, axes = plt.subplots(1,2, figsize=(12, 7))
ax.set_aspect(1)

# scatter를 표현한 방법
axes[0].scatter(student['math score'], student['reading score'], alpha=0.2)

# seaborn의 histplot을 사용해 결합분포를 확인
# 특정 구간으로 나눠서 어느 구간이 더 밀도가 높은지 확인할 수 있음 (better)
sns.histplot(x='math score', y='reading score', 
             data=student, ax=axes[1],
            #  color='orange',
             cbar=False, # True
             bins=(20, 20), 
            )

plt.show()
```
![결합분포](https://user-images.githubusercontent.com/91870042/152643407-15710d72-b491-4e48-a7c8-65f321cd1b33.png){: .align-center width="70%"}


## Relation & Regression
### scatter plot
이전에 `matplotlib`에서 사용한 scatterplot과 유사하며, 3가지 parameter (style, hue, size)를 지정할 수 있다. 각각에 대해서는 위에서 설명한 것과 동일하다. 모든 파라미터를 한번에 사용하게 되면 한번에 알아보기 힘들정도로 복잡하게 나타나므로, 하나의 파라미터만 사용해서 분포를 보는 것을 추천한다.

```py
fig, ax = plt.subplots(figsize=(7, 7))
sns.scatterplot(x='math score', y='reading score', data=student,
                # style='gender', markers={'male':'s', 'female':'o'},
                hue='race/ethnicity', 
                # size='writing score',
               )
plt.show()
```

### line plot
`seaborn`에서 제공하는 기본 데이터를 사용해서 선그래프를 살펴보자. 먼저, 데이터를 불러오는 과정이다.

```py
flights = sns.load_dataset("flights")
flights.head()
```
![flights](https://user-images.githubusercontent.com/91870042/152643644-d69fe840-3ee9-493d-bbfc-0f68192f8d45.png){: .align-center width="30%"}

위에서 가져온 데이터를 바탕으로 년도별로 각 월에 몇명의 승객이 있었는지 pivot을 통해 나타내보자.

```py
flights_wide = flights.pivot("year", "month", "passengers")
flights_wide.head()
```

![flights_wide](https://user-images.githubusercontent.com/91870042/152643675-9b11ed33-d035-4957-87db-e5588f79cd1b.png){: .align-center width="70%"}

위에서 가져온 데이터로 선그래프를 표현하면 다음과 같다. 하나 참고할 점은 <u>데이터를 pivot을 거치지 않고 넣게 되면 자동으로 평균과 표준편차를 계산해서 그래프에 표현해준다.</u>

```py
# 1월달에 대한 데이터의 분포를 선그래프로 표현하기
sns.lineplot(data=flights_wide, x='year', y='Jan', ax=ax)

# 월별로 지정하지 않고, 전체 데이터를 이용해 선그래프로 표현하기
sns.lineplot(data=flights, x="year", y="passengers", ax=ax)
```
![lineplot](https://user-images.githubusercontent.com/91870042/152643751-dae1992b-eb4a-46d6-b9d6-98f3ef710588.png){: .align-center width="70%"}

### Regplot
`regplot()`은 2차원 데이터에 대해 **선형회귀**를 거친 추세선을 표현해주는 함수이다. 또한 1차원 뿐만 아니라 다차원 회귀선도 표현이 가능하다. 이때는 `order` 파라미터에 그 차원의 수를 입력한다.

- `x_estimator` : 1개의 축에 겹쳐지는 데이터를 범위로 표현
- `x_bins` : 그래프 위에서 보여지는 점의 개수를 설정
- `order`: 다차원 회귀선
- `logx`: log를 사용한 회귀선

```py
fig, ax = plt.subplots(figsize=(7, 7))
sns.regplot(x='math score', y='reading score', data=student)

# 20개의 데이터만 표시하고 x축이 겹치는 부분에 대해서는 평균값 범위로 표현
sns.regplot(x='math score', y='reading score', data=student,
            x_estimator=np.mean, x_bins=20
           )
plt.show()
```
![regplot](https://user-images.githubusercontent.com/91870042/152643979-38c31644-1cbd-4321-b25c-52849d687990.png){: width="49%"} ![regplot](https://user-images.githubusercontent.com/91870042/152644046-3caf9a21-b5e3-4197-a249-9e14f827bb53.png){: width="49%"}


## Matrix Plots
### Heatmap
**히트맵**은 다양한 방식으로 사용될 수 있는데 대표적으로 상관관계를 분석해서 이를 시각화하고자 할때 많이 사용된다. Pandas에서는 `corr()`을 통해서 상관관계를 표현할 수 있다.

![image](https://user-images.githubusercontent.com/91870042/152644113-acc4ea13-226e-4d54-9f01-86491e6f182c.png){: .align-center width="70%"}

위의 데이터셋은 각 성적간에 상관관계가 다 높게 나와서 이번에는 다른 데이터셋인 **심장병 데이터셋**을 사용해서 시각화해보자. 

![심장병 데이터셋](https://user-images.githubusercontent.com/91870042/152644200-3ab15881-902a-4bf5-8629-83b9e0b04206.png){: .align-center width="70%"}

위의 데이터를 pandas에서 `corr()`로도 알아볼 수 있지만 이를 시각화 하기 위해서 히트맵에 표현하는 방법은 다음과 같다.
 
```py
# 1. 상관관계 분석
fig, ax = plt.subplots(1,1 ,figsize=(7, 6))
sns.heatmap(heart.corr(), ax=ax)
plt.show()

# 2. 범위를 설정한 상관관계 분석
fig, ax = plt.subplots(1,1 ,figsize=(7, 6))
sns.heatmap(heart.corr(), ax=ax,
           vmin=-1, vmax=1
           )
plt.show()
```
![bac210dc-442d-48d9-a4b9-2fd091964df1](https://user-images.githubusercontent.com/91870042/152644302-2a96f85a-3c5e-4178-823e-32ca5ff17657.png){: .align-center width="70%"}

위의 시각화 과정에서 1번으로 진행해도 얼핏보기에는 상관관계 분석이 히트맵을 통해서 잘 이루어졌다고 볼 수 있겠지만, 실제로 상관관계는 **-1~+1**의 범위를 갖기 때문에 `vmin`, `vmax`의 지정이 이루어져 더 색의 범위가 맞추어져 효과적으로 알아볼 수 있다. 

하지만 더 원활하게 알아보기 위해서 `colormap`을 알기 쉽게 음이면 파란색을, 양이면 붉은색을 띄게 만들 수 있다. 더 나아가 실제 값을 얼마인지 표현하면 더 정확하게 알아볼 수 있다. 

custom을 더하게 되면, 각각의 칸을 조금 띄우는 `linewidth`를 설정하는 것도 가능하고, 정사각형으로 보이게 하는 `square`파라미터도 설정할 수 있다.

```py
sns.heatmap(heart.corr(), ax=ax,
           vmin=-1, vmax=1, center=0,
            cmap='coolwarm', # colormap의 변경
            annot=True, fmt='.2f',
            linewidth=0.1, square=True
           )

# 사선을 기준으로 대칭이기 때문에 반드시 필요한 부분만 남기고 제외한다.
mask = np.zeros_like(heart.corr())
mask[np.triu_indices_from(mask)] = True

sns.heatmap(heart.corr(), ax=ax,
           vmin=-1, vmax=1, center=0,
            cmap='coolwarm',
            annot=True, fmt='.2f',
            linewidth=0.1, square=True, cbar=False,
            mask=mask
           )
```
![히트맵1](https://user-images.githubusercontent.com/91870042/152644486-d1763c6e-9d0f-44c1-9d82-f1bd6fccf969.png){: width="49%"} ![히트맵2](https://user-images.githubusercontent.com/91870042/152644497-878bbbd9-2e01-4b75-99b4-cafc1b27b60d.png){: width="49%"} 