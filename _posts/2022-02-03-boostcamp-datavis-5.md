---
title:  "[Data Visualization] Scatter plot"
excerpt: "matplotlib의 Scatter plot을 사용해 데이터의 상관관계 분석을 하고 표현/분석하기"

categories:
  - datavis
tags:
  - [AI, Naver, BoostCamp, Python, Matplotlib]
toc: true
toc_sticky: true
 
date: 2022-02-03 04:00:00
last_modified_at: 2022-02-03 04:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# 기본 Scatter plot
## Scatter plot
**Scatter plot**(산점도)는 점을 사용해서 두 feature간의 관계를 알기 위해 사용하는 그래프이다. matplotlib에서 그래프를 표현하기 위해서는 `.scatter()`를 사용한다.

```py
fig = plt.figure(figsize=(7, 7))

# 관계를 보기 때문에, aspect=1 이라고 설정
# 가로축과 세로축의 scale을 맞춰주는 역할을 해준다.
ax = fig.add_subplot(111, aspect=1) 

np.random.seed(970725)

x = np.random.rand(20)
y = np.random.rand(20)

ax.scatter(x, y)
ax.set_xlim(0, 1.05)
ax.set_ylim(0, 1.05)

plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152357506-6dd8e3c5-264d-4e38-92d3-8c9fd0bbd458.png){: .align-center}


## Scatter plot의 요소
- 색 : color, 색으로 표현하는 것이 가장 효과적이다.
- 모양: marker, 모양은 크게 잘 구분이 되지 않는다.
- 크기: size

```py
fig = plt.figure(figsize=(7, 7))
ax = fig.add_subplot(111, aspect=1)

np.random.seed(970725)

x = np.random.rand(20)
y = np.random.rand(20)
s = np.arange(20) * 20

ax.scatter(x, y, 
           s= s,
           c='white',
           marker='o',
           linewidth=1,
           edgecolor='gray')

plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152357734-48e8b283-47d6-4fd6-b882-86759590a529.png){: .align-center}


## Scatter plot의 목적
- `상관관계 분석`: 양의 상관관계, 음의 상관관계, 없음

    ![image](https://user-images.githubusercontent.com/91870042/152356920-ad49f6f4-811b-46cc-a18f-91e9c355cce2.png){: .align-center}

- `군집`, `값 사이의 차이`, `이상치`

    ![image](https://user-images.githubusercontent.com/91870042/152356974-b9e11a20-55e1-40cb-b6ea-3015429edbc1.png){: .align-center}


<br>

# 정확한 Scatter plot
## Overplotting
점이 많아질 수록 점의 분포를 파악하기 힘들다. 점이 겹치고 점간의 테두리가 오해를 일으킬 수 있음. → 4가지 방법으로 해결

`투명도 조정` , `지터링` , `2차원 히스토그램`, `컨투어` 

- 투명도 처리를 해서 밀도가 높으면 더 진해진다는 것을 알 수 있다.
- 지터링 → 점을 약간 이동해서 겹치지 않게 한다. 다른 방법이 더 효율적임..
- 2차원 히스토그램 → 1차원 히스토그램과 다르게 x축에 대해서도 구간을 나눠서 그림 (heatamp)
- 등고선 그래프 → 등고선이 좁을수록 데이터가 조밀하고 넓을 수록 밀도가 낮음.

![image](https://user-images.githubusercontent.com/91870042/152360736-79e9d9f7-635e-45ac-8ddb-a8d87aba5ddd.png){: .align-center}

## 점의 요소와 인지
- 색: 연속적인 값은 gradient하게, 이산적인 값은 개별 색상으로 설정

- 마커: 거의 구별하게 힘들고 크기가 고르지 않다. → 마커만으로 구분하는건 비추천한다. 또한 잉크 비례의 법칙에도 맞지가 않는다.

- 크기: 같은 점이라도 크기 차이를 둬서 차별을 둔다. 거품같기 때문에 버블차트라고 부르기도 하지만 오용하기 쉽다. 100이라는 점과 300이라는 점이 3배차이가 나야하지만 그렇지 않다. 관계보다는 점간의 비율에 초점을 두었을 때 크기고 구분하는 것이 좋다.

## 상관관계와 인과관계
**상관관계와 인과관계는 서로 다른 개념이다.**

- 인과관계: 사전정보와 함게 가정으로 제시한다. 
- 상관관계: 어떤 두 데이터 간에 관계가 존재하는지 본다. heatmap에서 다시 다룬다.

## 추세선
상관관계가 있을 때, 추세선을 이용해서 같이 표현해주는 것이 좋다. → scatter의 패턴을 유추할 수 있다. 추세선은 1개만 사용해서 전체적인 분포를 확인한다.

![image](https://user-images.githubusercontent.com/91870042/152360851-66f24ad5-5371-4637-be88-2e9f567e1d14.png){: .align-center}

## ETC
scatter와 grid는 서로 상성이 좋지 않다. grid를 사용한다면 최소한으로 사용하고 색은 무채색을 사용한다. 범주형이 포함된 관계에서는 히트맵이나 bubble chart를 추천한다.

<br>

# 실습: iris 데이터셋
지금부터 사용하는 실습 데이터는 유명한 데이터셋 중 하나인 iris(붓꽃 데이터셋)을 이용한다. 이 데이터셋을 이용해서 상관관계를 확인하는데 이와 함께 클러스터, 값의 차이, 이상치에 대해서 확인한다.

```py
fig = plt.figure(figsize=(7, 7))
ax = fig.add_subplot(111)

# 꽃밭침의 길이와 너비와의 관계를 산점도로 표시
ax.scatter(x=iris['SepalLengthCm'], y=iris['SepalWidthCm'])
plt.show()
```

![temp](https://user-images.githubusercontent.com/91870042/152358041-dfe4eda8-39fd-4aeb-b3a4-7648142aeb47.png){: .align-center}

꽃밭임의 너비의 평균보다 작은것은 파란색, 더 큰것은 회색으로 표현

```py
fig = plt.figure(figsize=(7, 7))
ax = fig.add_subplot(111)

slc_mean = iris['SepalLengthCm'].mean()
swc_mean = iris['SepalWidthCm'].mean()

ax.scatter(x=iris['SepalLengthCm'], 
           y=iris['SepalWidthCm'],
           c=['royalblue' if yy <= swc_mean else 'gray' for yy in iris['SepalWidthCm']]
          )

plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152358656-430c9397-50a7-496b-8b0f-c95e8dbced8d.png){: .align-center}

지금까지 보았을 때, 꽃밭침의 길이와 너비는 큰 상관관계가 있다고 판단하기 어렵다. 이번에는 각 종마다 동일하게 꽃밭침의 길이와 너비에 대해서 확인을 해서 여전히 상관관계가 없는지 확인해보자.

```py
fig = plt.figure(figsize=(7, 7))
ax = fig.add_subplot(111)

for species in iris['Species'].unique():
    iris_sub = iris[iris['Species']==species]
    ax.scatter(x=iris_sub['SepalLengthCm'], 
               y=iris_sub['SepalWidthCm'], 
               label=species)

ax.legend()    
plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152359282-26211d06-a612-4c3e-8ac8-da96a9e71619.png){: .align-center}
대체로 양의 상관관계를 보이고 있음을 알 수 있다. 종을 구분하지 않고 보았을 때 보이지 않던 정보들이 보이게 되었다.

이번에는 꽃받침의 길이, 너비의 분석이 아닌 꽃잎의 길이와 너비의 상관관계 분석을 진행해보자.
```py
fig = plt.figure(figsize=(7, 7))
ax = fig.add_subplot(111)

for species in iris['Species'].unique():
    iris_sub = iris[iris['Species']==species]
    ax.scatter(x=iris_sub['PetalLengthCm'], 
               y=iris_sub['PetalWidthCm'], 
               label=species)

ax.legend()    
plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152359511-63632d93-ef18-484a-bf4a-51093f0d7f53.png){: .align-center}
이번에는 확실히 군집화 (클러스터링)가 이루어진 것을 확인할 수 있다. 하지만 조금 떨어져 있는 setosa집단에 대해서 구분선을 두어 더 시각적인 주의를 줄 수 있다.

```py
ax.axvline(2.5, color='gray', linestyle=':')    
ax.axhline(0.8, color='gray', linestyle=':')  
```
![temp](https://user-images.githubusercontent.com/91870042/152359730-c9e89ece-4e1b-42b2-b3bf-01130311dc15.png){: .align-center}

특정한 feature에 대해서 상관관계 분석이 아닌 모든 feature에 대한 상관관계를 분석하기 위해서 중첩 반복문을 활용해서 확인할 수 있다. 모든 feature의 개수를 4개라고 했을 때, 2개씩 뽑아서 분석하는 경우의 수는 \\(_4C_2\\)로 6가지가 존재한다.

```py
fig, axes = plt.subplots(4, 4, figsize=(14, 14))

feat = ['SepalLengthCm', 'SepalWidthCm', 'PetalLengthCm', 'PetalWidthCm']

for i, f1 in enumerate(feat):
    for j, f2 in enumerate(feat):
        if i <= j : 
            axes[i][j].set_visible(False)
            continue
        for species in iris['Species'].unique():
            iris_sub = iris[iris['Species']==species]
            axes[i][j].scatter(x=iris_sub[f2], 
                               y=iris_sub[f1], 
                               label=species, 
                               alpha=0.7)
        if i == 3: axes[i][j].set_xlabel(f2)
        if j == 0: axes[i][j].set_ylabel(f1)

plt.tight_layout()        
plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152361518-b532cf75-1c7f-4df1-a1cc-f370ca761b3b.png){: .align-center}
