---
title:  "[Data Visualization] Bar plot"
excerpt: "matplotlib의 bar plot을 사용한 데이터의 비교, 분석"

categories:
  - datavis
tags:
  - [AI, Naver, BoostCamp, Python, Matplotlib]
toc: true
toc_sticky: true
 
date: 2022-02-03 02:00:00
last_modified_at: 2022-02-03 02:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Bar plot
`Bar plot`은 직사각형 막대를 사용해서 데이터의 값을 표현하는 차트 또는 그래프를 말한다. 다른 말로는 막대그래프, bar chart, bar graph 등의 이름으로 사용된다. 범주에 따른 수치 값을 비교하기에 적합한 시각화 방법이다.

막대그래프는 방향에 따라서 수직, 수평의 모양을 띌 수 있다.

1. 수직 (Vertiacl) : `bar()`
2. 수평 (Horizontal): `barh()` → **범주가 많을 때 사용하는 것이 적합하다.**

```py
fig, axes = plt.subplots(1, 2, figsize=(12, 7))

x = list('ABCDE')
y = np.array([1, 2, 3, 4, 5])

axes[0].bar(x, y)
axes[1].barh(x, y)

plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152333100-df6700e1-fc7f-4a37-a670-9cc12e0b9eef.png){: .align-center}

<br>

# 다양한 bar plot
## multiple bar plot
서로 다른 두 데이터를 비교하기 위해서 plot을 여러개 그려서 각 feature에 대한 수치를 표현하는 방법
```py

fig, axes = plt.subplots(1, 2, figsize=(15, 7)) # sharey=True로 설정 가능
axes[0].bar(group['male'].index, group['male'], color='royalblue')
axes[1].bar(group['female'].index, group['female'], color='tomato')
plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152333460-1c20549d-1c6d-4f28-8531-6db223209357.png){: width="49%"} ![temp](https://user-images.githubusercontent.com/91870042/152334082-f1a23f27-08d8-4bc1-b536-8ce09a88e7ef.png){: width="49%"}

y축을 따로 지정하거나 공유하지 않았을 때, 남자와 여자사이의 차이가 거의 없어보이는 문제가 생긴다. 이를 해결하기 위한 방법이 2가지 존재한다.

- y축을 공유하는 방법 `sharey = True`
- 각각의 subplot에 대해서 y축을 고정하는 방법 `ax.set_ylim(0, 200)`

## stacked bar plot
2개 이상의 그룹을 쌓아서 표현하는 막대 그래프. 맨 밑의 bar의 분포는 파악하기 쉽지만, 그 외의 분포들은 파악하기 어렵다. 그렇기 때문에 stacked를 사용하는 경우 각각의 분포를 annotation으로 표현해주는 것이 좋다.

`bottom` 파라미터를 사용해서 밑에 남성 데이터를 둔 다는 것을 표시한다.
```py
fig, axes = plt.subplots(1, 2, figsize=(15, 7))

group_cnt = student['race/ethnicity'].value_counts().sort_index()
axes[0].bar(group_cnt.index, group_cnt, color='darkgray')
axes[1].bar(group['male'].index, group['male'], color='royalblue')
axes[1].bar(group['female'].index, group['female'], bottom=group['male'], color='tomato')

for ax in axes:
    ax.set_ylim(0, 350)
    
plt.show()
fig.savefig('temp.png', transparent=True)
```
![temp](https://user-images.githubusercontent.com/91870042/152334391-8bbf3484-f1d0-431c-87bd-b7707614850d.png){: .align-center}

`Stacked Bar Plot`의 단점으로는 여성 데이터의 분포를 알기 힘들다는 것이 있다. 예를 들어 groupB와 groupD의 여성 데이터의 차이가 얼마나 나는지 알기가 힘들다. 그렇기 때문에 각 차이를 사용자가 직접 확인할 수 있게 **interactive** 한 시각화가 필요하다.

## Percentage Stacked Bar Plot
두 데이터를 쌓아서 표시하되, 그 비율이 각 데이터가 차지하는 만큼을 표현하는 방법이다.

```py
fig, ax = plt.subplots(1, 1, figsize=(12, 7))

group = group.sort_index(ascending=False) # 역순 정렬
total=group['male']+group['female'] # 각 그룹별 합


ax.barh(group['male'].index, group['male']/total, 
        color='royalblue')

ax.barh(group['female'].index, group['female']/total, 
        left=group['male']/total, 
        color='tomato')

ax.set_xlim(0, 1)
for s in ['top', 'bottom', 'left', 'right']:
    ax.spines[s].set_visible(False)

plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152334805-e878d5d1-65a0-45c1-96e9-c71f9e6d2be7.png){: .align-center}


위의 코드에서 group의 순서를 역순으로 정렬해 준 이유는 기본적으로 `barh` 플롯의 경우, 밑에서 부터 위로 라벨링을 진행하기 때문에, 위에서 아래로 읽기 편하게 하기 위해 역정렬을 해주는 것이다. 추가로, 그래프 생성시 기본적으로 생기는 사각형 테두리를 없애기 위해서 각변 (spines)에 대해서 숨김 처리를 진행하였다.

## overlapped bar plot
2개의 그룹만 비교를 진행한다면 겹쳐서 비교하는 것도 하나의 선택지가 될 수 있다. 3개 이상이 겹쳐지게 되면 확인하기가 힘들어진다. 같은 축을 사용하기 때문에 비교하기 쉽지만, 투명도를 적당히 조절해서 확인할 수 있게끔 만들어줘야한다.

투명도를 지정하기 위해서는 `alpha` 파라미터를 사용한다. 일반적으로 Bar Plot보다는 Area Plot에서 더 효과적이다. 자세한 내용은 seaborn에서 다룬다.

```py
# 투명도의 설정에 따른 overlapped bar plot의 차이
group = group.sort_index() # 다시 정렬

fig, axes = plt.subplots(2, 2, figsize=(12, 12))
axes = axes.flatten()

for idx, alpha in enumerate([1, 0.7, 0.5, 0.3]):
    axes[idx].bar(group['male'].index, group['male'], 
                  color='royalblue', 
                  alpha=alpha)
    axes[idx].bar(group['female'].index, group['female'],
                  color='tomato',
                  alpha=alpha)
    axes[idx].set_title(f'Alpha = {alpha}')
    
for ax in axes:
    ax.set_ylim(0, 200)
        
plt.show()
```

![temp](https://user-images.githubusercontent.com/91870042/152335073-e73a2c65-9a6f-4f89-8a72-83be91fc1079.png){: .align-center}

## grouped bar plot
가장 추천하는 방법이며 그룹별 범주에 따른 bar를 이웃되게 배치하는 방법이다. `matplotlib` 에서는 구현이 까다로운 편이다. 하지만 `seaborn`을 사용하면 훨씬 간편하게 구현할 수 있다.

그룹이 너무 많을 때는 grouped bar plot을 사용하는 것은 좋지가 않다. 너무 많은 경우에는 TOP5, TOP7정도만 표현하고 나머지는 ETC로 명시한다.

matplotlib에서 grouped bar plot을 구현하기 위해서 다음 3가지 테크닉을 사용해야한다.

- x축 조정
- width조정
- xticks, xticklabels 설정

2개의 group에 대해서 구별 하는 경우 [(0 - width/2, 0 + width/2), (1- width/2, 1 + width/2)] 처럼 표현한다.

matplotlib에서 그룹이 2개인 경우 말고, 그룹이 3개 이상인 경우 모두에게 적용할 수 있는 x축 계산 공식

$$
x + \frac{-N+1+2 i}{2}\times width
$$

```py
group = student.groupby('parental level of education')['race/ethnicity'].value_counts().sort_index()
group_list = sorted(student['race/ethnicity'].unique())
edu_lv = student['parental level of education'].unique()

fig, ax = plt.subplots(1, 1, figsize=(13, 7))

x = np.arange(len(group_list))
width=0.12

for idx, g in enumerate(edu_lv):
    ax.bar(x+(-len(edu_lv)+1+2*idx)*width/2, group[g], 
       width=width, label=g)

ax.set_xticks(x)
ax.set_xticklabels(group_list)
ax.legend()    
    
plt.show()
```

![temp](https://user-images.githubusercontent.com/91870042/152335407-1ee3034e-bffd-44fe-b11e-148ce6b338c4.png){: .align-center}

<br>

# 더 정확한 bar plot
## Principle of Proportion Ink
> The representation of numbers, as phbysically measured on the surface of the graphic itself, should be directly proportional to the numerical quantities represented.
> 

실제 값과 그에 표현되는 그래픽으로 표현되는 잉크의 양은 비례해야한다.

즉, 어떠한 값이 100을 가지고 있다면 해당 값을 표현하기 위해서는 잉크의 양이 100만큼 사용되어야 하고, 다른 값이 60을 가지고 있다면 그 값을 표현하려면 60만큼의 잉크의 양을 사용해야 한다.

추가로, 그래프간의 비교를 용이하게 하기 위해서는 x축의 시작은 0으로 지정되어야 한다.

위의 원칙은 막대 그래프에서만 한정되는 원칙은 아니면 `area plot`, `donut chart` 등 다수의 시각화에서 적용된다

```py
score = student.groupby('gender').mean().T

ig, axes = plt.subplots(1, 2, figsize=(15, 7))
idx = np.arange(len(score.index))
width=0.3

for ax in axes:
    ax.bar(idx-width/2, score['male'], 
           color='royalblue',
           width=width)
    ax.bar(idx+width/2, score['female'], 
           color='tomato',
           width=width)
    ax.set_xticks(idx)
    ax.set_xticklabels(score.index)

axes[0].set_ylim(60, 75)
    
plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152337871-e49cf894-7698-4c37-8221-a7d0166a759c.png){: .align-center}


이 그래프를 보았을 때, 확연한 차이를 보이는 것은 왼쪽의 그래프이다. 하지만 실제로 여자가 저만큼 수학을 못하는 것도 아니고, 남자가 실제로 저렇게 큰 차이를 보이며 못하는 것이 아니다. 이는 데이터의 값이 0부터 시작하지 않아서 발생되는 모습이다.

이런 차이를 줄이기 위해서 오른쪽과 같은 시각 자료를 생성하는 것이 바람직하다. 오른쪽 그래프의 차이를 늘리기 위해서 plot의 크기 (inch)를 조절하는 것도 하나의 방법이다.

## 데이터 정렬

더 정확한 정보의 전달을 위해서는 정렬이 필수적이다. Pandas에서 제공하는 `sort_values()` 또는 `sort_index()` 를 사용해서 정렬을 진행한다.

데이터의 종류에 따라서 정렬하는 기준을 달리하는게 좋다. 앞서서 살펴본 데이터의 종류에 따라서 정렬 기준을 다음과 같이 설정한다.

1. 시계열: `시간순`
2. 수치형: `크기순`
3. 순서형: `범주의 순서대로`
4. 명목형: `범주의 값 따라 정렬`

## 적절한 공간 활용
`matplotlib` 에서 제공하는 `subplot` 을 사용해서 그래프를 그리게 되면 그래프가 많이 붙어 있는 느낌을 받을 수 있다. 이에 따라 적절한 공간을 활용해서 눈에 더 잘 들어오는 시각화를 진행할 수 있다.

- x축과 y축의 제한을 두기 위해서 `.set_xlim()`, `.set_ylim()` 을 사용
- 축의 눈금을 가리기 위해서 `.spines[spine].set_visible()` 을 사용
- 막대의 두께를 설정 `width` (기본은 0.8을 사용) → 0.6~0.7 사용이 적절
- 범례 설정 `.legend()` → 위치
- 양 옆의 테두리 설정 `.margin()`

![temp](https://user-images.githubusercontent.com/91870042/152336537-51e04c6e-5f63-4ad7-a106-f77a9601357d.png){: .align-center}


## 복잡함과 단순함
필요없는 복잡함으로 화려하게 시각화할 필요가 없음. 무의미하게 3차원 이상으로 시각화를 하는 것은 지양해야한다. 

1. 큰 틀에서 비교를 할 때는 `grid()` 를 제거하는 것이 좋음
2. `.set_ticklabels()` 에서 불필요한 눈금의 라벨을 제거하는 것이 좋음
3. Text나 annontation을 적절한 위치에 배치하는 것도 중요

```py
group_cnt = student['race/ethnicity'].value_counts().sort_index()

fig, axes = plt.subplots(1, 2, figsize=(15, 7))

for ax in axes:
    ax.bar(group_cnt.index, group_cnt,
           width=0.7,
           edgecolor='black',
           linewidth=2,
           color='royalblue',
           zorder=10
          )

    ax.margins(0.1, 0.1)

    for s in ['top', 'right']:
        ax.spines[s].set_visible(False)

axes[1].grid(zorder=0)

for idx, value in zip(group_cnt.index, group_cnt):
    axes[1].text(idx, value+5, s=value,
                 ha='center', 
                 fontweight='bold'
                )
        
plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152337250-bba08bdb-1a90-461d-a603-dde4004ca95f.png){: .align-center}

## ETC
- 오차막대 (errorbar)을 사용하여 불확실성 정보를 추가할 수 있다.
- Bar사이의 gap을 0으로 설정하면 히스토그램 처럼 표현할 수 있다. → histogram을 부록처럼..
- 다양한 Text 정보의 활용 → `.set_title()`, `.set_xlabel()`, `.set_ylabel()`

```py
# 막대 그래프의 에러에 대한 표시 (표준편차)
fig, ax = plt.subplots(1, 1, figsize=(10, 10))

idx = np.arange(len(score.index))
width=0.3


ax.bar(idx-width/2, score['male'], 
       color='royalblue',
       width=width,
       label='Male',
       yerr=score_var['male'],
       capsize=10
      )

ax.bar(idx+width/2, score['female'], 
       color='tomato',
       width=width,
       label='Female',
       yerr=score_var['female'],
       capsize=10
      )

ax.set_xticks(idx)
ax.set_xticklabels(score.index)
ax.set_ylim(0, 100)
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)

ax.legend()
ax.set_title('Gender / Score', fontsize=20)
ax.set_xlabel('Subject', fontweight='bold')
ax.set_ylabel('Score', fontweight='bold')

plt.show()
```

![temp](https://user-images.githubusercontent.com/91870042/152337089-ca547d8a-72e5-41de-b0fb-7734b75603ff.png){: .align-center}

