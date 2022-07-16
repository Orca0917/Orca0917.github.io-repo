---
title:  "[Data Visualization] Seaborn 심화"
excerpt: "Seaborn의 여러 차트의 정보량을 향상시키는 방법"

categories:
  - datavis
tags:
  - [AI, Naver, BoostCamp, Python, Matplotlib]
toc: true
toc_sticky: true
 
date: 2022-02-04 05:00:00
last_modified_at: 2022-02-04 05:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Joint Plot
바로 이전 게시물에서 *Distribution API* 를 사용하여 결합확률분포를 시각화하는 함수를 보았었다. `histplot`이나 `kdeplot`에 x와 y의 인자를 모두 전달하면 확인할 수 있었다. 이번에는 거기에 각각의 분포까지도 살펴볼 수 있는 시각화인 **Joint Plot**에 대해서 알아보자.

```py
sns.jointplot(x='math score', y='reading score',data=student,
             height=7)
```
![joint plot 예시](https://user-images.githubusercontent.com/91870042/152646138-95467d9d-79a5-4ceb-89c8-d5ac592d513a.png){: .align-center width="70%"}

`hue`와 `kind`를 사용해서 표현한 그래프. 일부 옵션에서는 두 파라미터를 동시에 적용할 경우, 실행이 되지 않는 오류가 있다. 오류가 있는 부분에 대해서는 둘 중 하나만 지정해서 사용해야 한다.

```py
sns.jointplot(x='math score', y='reading score',data=student,
              hue='gender'
             )

sns.jointplot(x='math score', y='reading score',data=student,
            # hue='gender', 
              kind='resid', # { “scatter” | “kde” | “hist” | “hex” | “reg” | “resid” }, 
            # fill=True
             )
```
![hue를 적용한 jointplot](https://user-images.githubusercontent.com/91870042/152646266-7ce0d9ff-d404-4c6d-939e-e8fc0df36ef7.png){: width="49%"} ![hex를 적용한 jointplot](https://user-images.githubusercontent.com/91870042/152646278-ab23323a-077a-4021-a76f-467858166e03.png){: width="49%"}

<br>

# Pair Plot
**Pair Plot**은 데이터셋의 pair-wise관계를 시각화하는 함수이다. 위에서는 원하는 feature2개를 선택해서 알아봤다면, 이번에는 가능한 모든 조합에 대해서 시각화해주는 기능을 한다.

```py
sns.pairplot(data=iris)
```
![pairplot 예시](https://user-images.githubusercontent.com/91870042/152646387-3f454f46-704d-4ba1-89f7-268d1025f7a7.png){: .align-center width="70%"}

이렇게 하게되면 데이터의 분포, 선형성, outlier와 같은 정보들을 한눈에 볼 수가 있다. 하지만 너무 많은 feature가 선택된 경우 생성이 매우 느려지거나 너무 큰 메모리를 사용해서 오류가 발생할 수 있다.

위에서 조금 더 각 종에 대해서 시각화를 하기 위해서 `hue`파라미터를 지정하면 각 종에 대한 분포를 더 보기 쉽게 표현할 수 있다.

```py
sns.pairplot(data=iris, hue='Species')
```

![d17b390c-3366-4a46-97af-32d5f70f3a11](https://user-images.githubusercontent.com/91870042/152647239-45f6128b-5a68-44d0-b4f8-99bbf3cd2caa.png){: .align-center width="70%"}

default로 설정된 scatter기반의 plot말고 다른 plot의 형태를 보고 싶다면, `kind`파라미터를 통해서 직접 지정하는 것이 가능하다.

- `kind`: scatter, kde, hist, reg
- `diag_kind`: auto, hist, kde, none
- `corner` : 대각선을 기준으로 대칭으로 나오기 때문에, 상삼각행렬의 plot을 보지 않게 설정

```py
sns.pairplot(data=iris, hue='Species', kind='hist')

sns.pairplot(data=iris, hue='Species', corner=True)
```
![4b747e60-a4ca-4680-bf99-d34ac6006c4d](https://user-images.githubusercontent.com/91870042/152646579-8f39e49c-6e1c-4f11-b7bd-a5bf263861c8.png){: width="49%"} ![5ef07efc-53cc-4646-b861-bd75a8a2bba1](https://user-images.githubusercontent.com/91870042/152646584-27527b1f-caff-462c-b329-a2d35efed939.png){: width="49%"}

<br>

# FacetGrid
**pairplot**에서 feature와 feature사이의 가능한 모든 분포를 살펴봤다면, **FacetGrid**는 feature간의 분포 뿐만아니라, <u>feature의 카테고리간의 관계</u>도 확인해볼 수 있다. FacetGrid를 기반으로 만들어진 함수는 다음 4가지가 존재한다.

- `catplot` : Categorical
- `displot` : Distribution
- `relplot` : Relational
- `lmplot` : Regression

## catplot
사실 catplot은 지금까지 사용했던 seaborn의 함수중 대다수가 포함되어 있다. `kind`파라미터를 설정하지 않는 경우 기본적으로 `stripplot()`이 생성이 되며 더 자세한 코드는 아래에서 설명한다.

- Categorical scatterplots
    - `stripplot()` : `kind="strip"`
    - `swarmplot()` : `kind="swarm"`

- Categorical distribution plots
    - `boxplot()` : `kind="box"`
    - `violinplot()` : `kind="violin"`
    - `boxenplot()` : `kind="boxen"`

- Categorical estimate plots
    - `pointplot()` : `kind="point"`
    - `barplot()` : `kind="bar"`
    - `countplot()` : `kind="count"`

```py
# catplot-box와 boxplot의 비교
sns.catplot(x="race/ethnicity", y="math score", hue="gender", data=student,
            kind='box')

# sns.boxplot(x="race/ethnicity", y="math score", hue="gender", data=student)
```
![catplot1](https://user-images.githubusercontent.com/91870042/152646917-878753ef-cbf3-448b-bca8-2058cf2244e3.png){: width="49%"} ![catplot2](https://user-images.githubusercontent.com/91870042/152646944-45967eb4-cf5e-4feb-a77f-f700aea9413b.png){: width="49%"}

catplot은 행과 열의 category를 지정해서 그래프를 생성할 수 있다. 행에는 예습의 여부, 열에는 점심식사 여부로 정해서 총 2 * 2 크기의 그래프를 볼 수 있다.

```py
sns.catplot(x="race/ethnicity", y="math score", hue="gender", data=student,
            kind='box', col='lunch', row='test preparation course'
           )
```
![catplot-row, col](https://user-images.githubusercontent.com/91870042/152647057-359c7fa7-3ab8-4a79-8a2e-50806914e6df.png){: .align-center width="70%"}

## displot
displot도 마찬가지로 이전에 배웠던 다양한 함수들을 포함하고 있다. `kind`파라미터를 지정하지 않는 경우 기본적으로 hist를 사용한다.

- `histplot()` : `kind="hist"`
- `kdeplot()` : `kind="kde"`
- `ecdfplot()` : `kind="ecdf"`

```py
sns.displot(x="math score", hue="gender", data=student,
           col='race/ethnicity', # kind='kde', fill=True
            col_order=sorted(student['race/ethnicity'].unique())
           )
```
![displot](https://user-images.githubusercontent.com/91870042/152647123-344667d0-582b-45bb-9aa6-b6984d799941.png){: .align-center}


## relplot
relplot은 종류를 따로 지정하지 않았을 때, 기본적으로 산점도를 그린다. 아래 코드에서는 각 성별별로 점심의 카테고리에 따라서 어떤 분포를 나타내고 있는지 보여준다.

- `scatterplot()` : `kind="scatter"`
- `lineplot()` : `kind="line"`

```py
sns.relplot(x="math score", y='reading score', hue="gender", data=student,
           col='lunch')
```
![relplot](https://user-images.githubusercontent.com/91870042/152647172-a1434eee-c1ad-4843-8892-6fb0df196565.png){: .align-center width="70%"}


## lmplot
lmplot은 x와 y의 선형관계를 시각화로 나타낸다. 아래 그래프에서는 hue값을 성별로 지정해서 각 성별별로 어떻게 선형관계를 이루고 있는지 나타낸다.

```py
sns.lmplot(x="math score", y='reading score', hue="gender", data=student)
```

![lmplot - linearregression](https://user-images.githubusercontent.com/91870042/152647210-c7abc93d-853d-4a41-8409-0fcd2ed7c7d0.png){: .align-center width="70%"}
