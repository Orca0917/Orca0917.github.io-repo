---
title:  "[Data Visualization] Color"
excerpt: "미적으로 더 나은 시각화를 만드는 방법. 차트의 요소들 중 색에 대한 설명"

categories:
  - datavis
tags:
  - [AI, Naver, BoostCamp, Python, Matplotlib]
toc: true
toc_sticky: true
 
date: 2022-02-04 01:00:00
last_modified_at: 2022-02-04 01:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Color에 대한 이해
## 색이 중요한 이유
시각화에 있어서 어떤게 가장 표현이 잘되는지 표현하는 그림은 다음과 같다.

identity를 구분하는 가장 중요한 요소는 <u>위치적인 정보</u>이고 그 다음으로 중요한 것은 <u>색상</u>이다. 위치는 시각화에 방법에 따라서 결정되고 색은 우리가 직접 결정할 수 있다. 어떤 식으로 색을 구성하느냐에 따라서 우리가 전달하고 싶은 것이 잘 전달될 수 있다.

보는 사람으로서 더 매력적으로 느껴지기 위해서는 더 **좋은 색**과 색과 색의 매칭, **조화**가 중요한 요소로 포함된다.

## 화려함이 시각화의 전부는 아니다.
시각적으로 화려하게 와닿는 것은 분명히 매력적이지만 화려함은 시각화의 일부 요소이다. 무엇보다 중요한 것은 보는 사람으로 하여금 내가 전달하고 싶은 바를 효과적으로 전달하는 것이다. 또한 그것을 받아들이는 독자도 오해없이 잘 받아들이는 것이 중요하다.

## 색이 가지는 의미
지금까지 살아오면서 이미 다양한 색을 바라보면서 살아왔다.

- 높은 온도와 낮은 온도: 빨강, 파랑
- Kakao, Naver: 노랑, 초록
- 각 정당의 색상

이렇게 이미 잘 알려진 색상의 느낌을 잘 살려서 활용하는 것도 중요하다. 색상에 대한 감이 없다면, 이렇게 통용되는 색상을 사용하거나 다른 사례 스터디를 통해서 색상을 선택하는 것이 좋다.

## 색상 더 이해하기
색을 이해하기 위해서는 `RGB`값보다 `HSL`값을 이해하는 것이 중요하다. HSL이 의미하는 바는 다음과 같다.

![image](https://upload.wikimedia.org/wikipedia/commons/8/8a/HSL_color_solid_sphere_spherical.png){: .align-center width="50%"}

- `Hue`(색조): 빨강, 파랑, 초록등 색상으로 생각하는 부분이며 빨간색에서 보라색까지 있는 스펙트럼에서 0~360의 값으로 표현한다.
- `Saturate`(채도): 무채색과의 차이를 나타내며 선명도라고 볼 수 있다.
- `Lightness`(광도): 색상의 밝기

<br>

# Color Palette의 종류

## 범주형
**범주형**(Categorical) 파레트는 **Discrete, Qualitative**등 다양한 이름으로 불리고 있다. 범주형 컬러파레트는 각각 독립된 색상으로 구성되어 있어 범주형 변수에 사용하기 적합하다. (Bar plot)

![image](https://user-images.githubusercontent.com/91870042/152539326-67bb950b-72af-4901-91fc-ee9cdc5bb262.png){: .align-center}

일반적으로 최대 10개의 색상을 사용하는 것이 좋다. 색상간의 채도, 명도가 비슷한것은 구분하기 쉽지 않기 때문에 사용하지 않는 것이 좋다.

![image](https://user-images.githubusercontent.com/91870042/152539428-9648aca7-8660-4302-a8bb-530dfc4bdb71.png){: .align-center}

- One color: 전체적인 분포를 보기에 유용하다
- categorical: 이산적인 개별 값에 적합하다
- Diverge, Sequential: 같은 값에 대해서도 다른 가중치가 적용되는 것 처럼 보인다.

범주형 색상은 채도와 광도는 거의 일정하고 색상의 변화만으로 차이를 주는 것이 특징이다.

## 연속형
Gradient를 사용한 **연속형 색상**으로서 정렬된 값을 가지는 순서형, 연속형 변수에 사용하기 적합하다. 연속적인 색상을 사용하여 값을 표현하며 어두운 배경에서는 밝은색이, 밝은 배경에서는 어두운색이 더 큰 값을 표현한다.

![image](https://user-images.githubusercontent.com/91870042/152539565-3bcf070a-221c-408b-a253-8e2e91d811b1.png){: .align-center}

색상은 단일 색조로 표현하는 것이 좋고, **균일한 색상변화가 중요**한 color palette이다. 단일색조로 표현하기 위해 시작 색상을 흰색 또는 검정색으로 두는 것이 좋다. 이의 대표적인 예시로 github의 commit log가 있다.

![image](https://user-images.githubusercontent.com/91870042/152472537-4fea5852-514e-4e8e-b936-6d772279d3c2.png){: .align-center}


## 발산형
연속형과 유사한 형태를 가지지만 차이점은 중앙을 기준으로 양끝으로 각각 발산한다는 점이 다르다. 상반된 값이나, 서로 다른 2개의 값을 표현하는데 적합하다. <u>양끝으로 갈수록 색이 진해지며, 중앙의 색은 양쪽의 점에서 편향되지 않아야 한다.</u> 하지만 그렇다고 중앙의 색이 무채색일 필요는 없다.

![image](https://user-images.githubusercontent.com/91870042/152539823-74376625-482f-4a00-8df8-79d2269a55e2.png){: .align-center}

<br>

# 그 외 색 tips
## 강조, 대비
색을 사용하는 것은 강조를 위해서 사용된다. 이 강조를 더 효과적으로 표현하기 위해서 `Highlighting`을 사용할 수 있다.

강조를 위한 방법 중 하나로 **색상 대비**를 사용할 수 있으며 그 종류는 다음 4가지가 있다.
- `명도 대비` : 밝은 색과 어두운 색을 배치하면 밝은 색은 더 밝게, 어두운 색은 더 어둡게 보인다. (회색-검정)
- `색상 대비` : 가까운 색은 차이가 더 크게 보인다. (파랑-보라, 보라-빨강)
- `채도 대비` : 채도의 차이. 채도가 더 높아보이는 것이 선명해 보인다. (회색-주황)
- `보색 대비` : 정반대 색상을 사용하면 더 선명해 보인다. (빨강-초록)

## 색각 이상
색인지가 중요한 분야에 있어서는 `색각, 색맹`에 대한 고려가 필수적이다. 대표적으로 이에 사용되는 colormap은 **viridis, plasma, inferno, magma**가 있다.

![image](https://user-images.githubusercontent.com/91870042/152540344-0882980f-0098-4fec-a68e-64e39489b59c.png){: .align-center}

<br>

# 실습

여기서 사용하게 될 데이터셋은 **StudentsPerformance.csv** 파일이며 데이터셋은 [여기](https://www.kaggle.com/spscientist/students-performance-in-exams)에서 다운받을 수 있다.
{: .notice--warning}

```py
student = pd.read_csv('./StudentsPerformance.csv')
student.head()
```
![image](https://user-images.githubusercontent.com/91870042/152541591-44431136-f650-412b-9d68-edf6051c6411.png){: .align-center}

위의 DataFrame에서 **범주형**파레트는 `race/ethnicity`에 대해 적용해서 각 그룹별로 색상을 지정할 것이고, **연속형** 파레트를 사용해 각 학생별로 수학, 읽기, 쓰기 점수분포를 표현할 것이다.

```py
# Group to Number
groups = sorted(student['race/ethnicity'].unique())
gton = dict(zip(groups , range(5)))

# Group에 따라 색 1, 2, 3, 4, 5
student['color'] = student['race/ethnicity'].map(gton)
```

## 색 살펴보기
```py
from matplotlib.colors import ListedColormap

qualitative_cm_list = ['Pastel1', 'Pastel2', 'Accent', 'Dark2', 'Set1', 'Set2', 'Set3', 'tab10']

fig, axes = plt.subplots(2, 4, figsize=(20, 8))
axes = axes.flatten() # 2차원 numpy array로 생성된 것을 1차원으로 늘린다.

student_sub = student.sample(100)

for idx, cm in enumerate(qualitative_cm_list):    
    pcm = axes[idx].scatter(student_sub['math score'], student_sub['reading score'],
                     c=student_sub['color'], cmap=ListedColormap(plt.cm.get_cmap(cm).colors[:5]) 
                     # ListedColormap = RGB값으로 이루어진 튜플을 cmap으로 변환
                     )

    # 색상구분을 위한 colorbar 추가 : 일종의 subplot이기 때문에 ticks지정 가능
    cbar = fig.colorbar(pcm, ax=axes[idx], ticks=range(5)) 
    cbar.ax.set_yticklabels(groups)
    axes[idx].set_title(cm)
    
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/152542906-0ba540ec-3d65-4494-8dc0-5720ce288d5c.png){: .align-center}

예쁜 시각화를 위해서 Pastel톤 계열을 사용하는 것이 좋지만, `Pastel1`, `Pastel2`와 같이 너무 연한것은 사용하지 않는 것이 좋다. 위에서 보았을 때, 색의 구분이 5개만 되더라도 알아보기 힘들다는 것을 알 수 있디. 그렇기 때문에 아무리 많아도 7~8개 정도만 사용하는 것이 바람직히다.

일반적으로 많이 사용되는 것은 `tab10`과 `Set2`이다.

## 연속형 색상
연속형 색상은 heatmap, contour plot(등고선)과 같은 2차원 시각화에서 많이 사용한다. 연속형 색상은 색조는 유지하되 색의 밝기를 조정해서 연속적인 표현을 나타낸다.

```py
sequential_cm_list = ['Greys', 'Purples', 'Blues', 'Greens', 'Oranges', 'Reds',
            'YlOrBr', 'YlOrRd', 'OrRd', 'PuRd', 'RdPu', 'BuPu',
            'GnBu', 'PuBu', 'YlGnBu', 'PuBuGn', 'BuGn', 'YlGn']

fig, axes = plt.subplots(3, 6, figsize=(25, 10))
axes = axes.flatten()

student_sub = student.sample(100)

for idx, cm in enumerate(sequential_cm_list):    
    pcm = axes[idx].scatter(student['math score'], student['reading score'],
                            c=student['reading score'], 
                            cmap=cm,
                            vmin=0, vmax=100
                            # 색상에서 최소값과 최대값을 지정한다.
                     )
    fig.colorbar(pcm, ax=axes[idx])
    axes[idx].set_title(cm)
    
plt.show()
```
![image](https://user-images.githubusercontent.com/91870042/152543488-5322f0b5-747e-4e2d-9552-c6bdffdab9ad.png){: .align-center}

### imshow
`seaborn`에서 사용하는 heatmap표현을 matplotlib에서 사용해보기

```py
im = np.arange(100).reshape(10, 10)
fig, ax = plt.subplots(figsize=(10, 10))
ax.imshow(im)
plt.show()
```
![image](https://user-images.githubusercontent.com/91870042/152543755-3f861ceb-af7d-4ad9-8e27-4fae45a49c6e.png){: .align-center width="70%"}

```py
# 깃허브 잔디밭 구현하기
im =  np.random.randint(10, size=(7, 52))
fig, ax = plt.subplots(figsize=(20, 5))
ax.imshow(im, cmap='Greens') # 녹색계열의 색상을 사용
ax.set_yticks(np.arange(7)+0.5, minor=True) # 테두리 지정
ax.set_xticks(np.arange(52)+0.5, minor=True)
ax.grid(which='minor', color="w", linestyle='-', linewidth=3)
plt.show()
```

## 발산형 색상
연속형과 유사하지만 어디를 중심으로 삼을 것인지가 중요하다. 상관관계나 기준값이 있는 데이터에 적합하고 지리데이터에서도 많이 사용된다.

`TwoSlopeNorm`: 어떤 기준점을 바탕으로 각각 정규화를 진행해준다.

```py
print(student['reading score'].mean()) # 학생 읽기 성적의 평균
print(offset([0, 10, 20, 30, 70, 85, 100]))
>>> 69.169
    [0.  0.07228672 0.14457344 0.21686015 0.5134767  0.75673835  1. ]
```
위의 결과를 보면, 0점 ~ 평균값을 0~50%로 정규화하고, 나머지 평균값~100점을 50~100%로 정규화를 진행했다. 따라서 평균에 가까운 70점에 대해서는 51%의 결과가 나오게 되었다.

```py
# 읽기데이터의 평균을 기준으로 수학점수와 쓰기점수의 발산형 파레트 생성

from matplotlib.colors import TwoSlopeNorm

diverging_cm_list = ['PiYG', 'PRGn', 'BrBG', 'PuOr', 'RdGy', 'RdBu',
            'RdYlBu', 'RdYlGn', 'Spectral', 'coolwarm', 'bwr', 'seismic']

fig, axes = plt.subplots(3, 4, figsize=(20, 15))
axes = axes.flatten()

offset = TwoSlopeNorm(vmin=0, vcenter=student['reading score'].mean(), vmax=100)

student_sub = student.sample(100)

for idx, cm in enumerate(diverging_cm_list):    
    pcm = axes[idx].scatter(student['math score'], student['reading score'],
                            c=offset(student['math score']), 
                            cmap=cm,
                     )
    cbar = fig.colorbar(pcm, ax=axes[idx], 
                        ticks=[0, 0.5, 1], 
                        orientation='horizontal'
                       )
    cbar.ax.set_xticklabels([0, student['math score'].mean(), 100])
    axes[idx].set_title(cm)
    
plt.show()
```
![image](https://user-images.githubusercontent.com/91870042/152544911-347180dd-6e14-4d35-ae4d-832b49a233d9.png){: .align-center width="70%"}

## 색상 대비
위에서 다룬 데이터를 계속 사용해서 그룹별 인원수 그래프를 그리고, `수학`, `읽기`, `쓰기` 성적간의 상관관계 분석을 진행해보자.

```py
fig = plt.figure(figsize=(18, 15))
groups = student['race/ethnicity'].value_counts().sort_index()

ax_bar = fig.add_subplot(2, 1, 1)
ax_bar.bar(groups.index, groups, width=0.5)

ax_s1 = fig.add_subplot(2, 3, 4)
ax_s2 = fig.add_subplot(2, 3, 5)
ax_s3 = fig.add_subplot(2, 3, 6)

ax_s1.scatter(student['math score'], student['reading score'])
ax_s2.scatter(student['math score'], student['writing score'])
ax_s3.scatter(student['writing score'], student['reading score'])

for ax in [ax_s1, ax_s2, ax_s3]:
    ax.set_xlim(-2, 105)
    ax.set_ylim(-2, 105)

plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/152545543-47e1656e-ed41-472f-80b7-e0ac9db20783.png){: .align-center width="70%"}

위와 같이 그리게 되면, scatter plot에서 각 그룹별로 어떤 분포를 가지고 잇는지 알아보기가 힘들다. 이런 문제를 해결하기 위해서 앞서서 다루었던 **명도, 채도, 보색대비**를 사용해볼 수 있다.

```py
# 명도 대비
a_color, nota_color = 'black', 'lightgray' # 명도 대비
a_color, nota_color = 'orange', 'lightgray' # 채도 대비
a_color, nota_color = 'tomato', 'lightgreen' # 보색 대비

colors = student['race/ethnicity'].apply(lambda x : a_color if x =='group A' else nota_color)
color_bars = [a_color] + [nota_color]*4
```
![image](https://user-images.githubusercontent.com/91870042/152545990-da301dce-f4ca-43f1-80db-96e60294a47d.png){: width="32%"}![image](https://user-images.githubusercontent.com/91870042/152546073-a510317a-bdea-424c-be50-b59fff0a7321.png){: width="32%"}![image](https://user-images.githubusercontent.com/91870042/152546137-67355b96-f2db-457b-b857-846d4ccbd929.png){: width="32%"}