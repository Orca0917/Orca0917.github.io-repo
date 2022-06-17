---
title:  "[Data Visualization] 다양한 시각화 라이브러리"
excerpt: "matplotlib, seaborn이외의 missingno, treemap, wafflechart, venn"

categories:
  - datavis
tags:
  - [AI, Naver, BoostCamp, Python, Matplotlib]
toc: true
toc_sticky: true
 
date: 2022-02-11 02:00:00
last_modified_at: 2022-02-11 02:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Missingno

![image](https://user-images.githubusercontent.com/91870042/153742364-684603e3-1d34-4473-aef2-ea1624d162f3.png){: .align-center width="70%"}

- 결측치를 체크하는 시각화 라이브러리
- 빠르게 결측치의 분포를 확인하고 싶을 때 사용할 수 있다.
- 정렬을 사용해서 Null값의 분포를 더 자세히 알아볼 수 있다.

<br>

# Treemap

![image](https://user-images.githubusercontent.com/91870042/153742366-a57ef14d-4657-44ba-b934-97f0c3e57884.png){: .align-center width="70%"}

- 계층적 데이터의 대표적인 시각화
- 계층적 데이터를 직사각형을 사용해서 포함관계를 표현한 시각화 방법이다.
- 사각형을 분할하는 타일링 알고리즘에 따라서 형태가 다양해질 수 있다.
- 큰 사각형을 분할하여 전체를 나타내는 **모자이크 플롯** 과 유사하다.

![image](https://user-images.githubusercontent.com/91870042/153742368-c2f27dba-c7db-4614-a6d2-a63f3eefa6ab.png){: .align-center width="70%"}

- 단점으로, 계층이 깊어질수록 사각형이 너무 작아져서 텍스트를 넣기가 어려워진다.
- 최근에는 plotly의 interactive한 장점이 있어서 작은 텍스트여도 줌인, 줌아웃이 가능해졌다.

<br>

# Waffle Chart

- 와플의 형태로 데이터를 시각화 하는 방법

![image](https://user-images.githubusercontent.com/91870042/153742370-a520e4e5-70c2-4990-8958-7cc9f457d4a9.png){: .align-center width="70%"}

- 기본적인 형태는 정사각형이지만, 원하는 벡터 이미지로도 사용할 수 있다.
- Pie plot과 유사하게 전체적인 비율을 살펴볼때도 유용하다.
- 위의 사각형을 아이콘으로 대체하여 Pictogram Chart로도 그려볼 수 잇다.

![image](https://user-images.githubusercontent.com/91870042/153742374-351625d9-0335-4691-aa56-fa67aed00e30.png){: .align-center width="70%"}

<br>

# Venn

- 벤 다이어그램
- 집합에서 사용하는 익숙한 벤다이어그램
    - EDA 보다는 **출판, 및 프레젠테이션**에서 많이 사용한다.
    - 디테한 한숑이 draw.io나 ppt에 비해서 어려운 단점이 있다.
- 원이 많아 질수록 가독성이 크게 떨어지는 단점이 있어서 사용을 자제하는 것이 좋다.

<br>

# 실습

- 실습에서 사용할 타이타닉 데이터셋 가져오기

```python
titanic = sns.load_dataset('titanic')
titanic.head()
```

![image](https://user-images.githubusercontent.com/91870042/153742382-5727f06b-66a5-4caa-99bc-525c5b1f6385.png){: .align-center width="70%"}

```python
titanic.info()
```

![image](https://user-images.githubusercontent.com/91870042/153742384-21a987c3-7d50-4666-95e5-18c2c04cd69b.png){: .align-center width="55%"}

- missingno를 사용한 결측치의 확인

```python
msno.matrix(titanic)
```

![image](https://user-images.githubusercontent.com/91870042/153742388-906b8e88-6a37-4580-9052-d634e3d7bccb.png){: .align-center width="70%"}

- row당 결측치의 개수가 다르기 때문에 정렬을 통해서 자세히 살펴볼 수 있다.

```python
msno.matrix(titanic, 
            sort='descending', # ascending
           )
```

![image](https://user-images.githubusercontent.com/91870042/153742391-edd504ce-4a3b-4d88-bb6f-e726e406582a.png){: .align-center width="70%"}

- bar chart로 null값의 확인해보기

```python
msno.bar(titanic)
```

![image](https://user-images.githubusercontent.com/91870042/153742397-6609dc7a-1abd-47ab-a279-d8f2f29d3375.png){: .align-center width="70%"}

- `squarify` 를 사용한 트리맵의 사용. 더 자세한 사용은 `pyplot` 에서 할 수 있다.

```python
values = [100, 200, 300, 400]
squarify.plot(values)
```

![image](https://user-images.githubusercontent.com/91870042/153742400-45e42677-8549-4ab1-8d9c-3d3b5637a038.png){: .align-center width="40%"}

- `label` : Treemap에 label을 설정
- `color` : Treemap에 보여지는 색상을 직접 지정
- `pad` : 직사각형간의 간격을 설정
- `text_kwargs` : 텍스트의 요소를 Dict타입으로 전달한다

```python
values = [100, 200, 300, 400]
label = list('ABCD')
color = ['#4285F4', '#DB4437', '#F4B400', '#0F9D58']

squarify.plot(values, label=label, color=color, pad=0.2,
             text_kwargs={'color':'white', 'weight':'bold'}
             )
```

![image](https://user-images.githubusercontent.com/91870042/153742408-29a6052c-0e9a-450d-9f50-e372497bf803.png){: .align-center width="40%"}

- 축들의 정보를 matplotlib 문법으로 깔끔하게 제거

```python
fig, ax = plt.subplots()
values = [100, 200, 300, 400]
label = list('ABCD')
color = ['#4285F4', '#DB4437', '#F4B400', '#0F9D58']

squarify.plot(values, label=label, color=color, pad=0.2, 
               text_kwargs={'color':'white', 'weight':'bold'}, ax=ax)

ax.axis('off')
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153742412-1ae4121c-70a9-456d-b8d1-1057103c5c09.png){: .align-center width="40%"}

### Waffle Chart

- `rows`, `columns` 로 사각형의 전체 형태를 지정하며, `values` 로 값을 전달한다.
- 현재는 48, 46, 6을 50칸으로 표현하기 때문에 줄어들거나 오차가 생킬 수 있다.

```python
fig = plt.figure(
    FigureClass=Waffle, 
    rows=5, 
    columns=10, 
    values=[48, 46, 6],
    figsize=(5, 3)
)
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153742415-ef730382-e7f4-4142-ab33-de9b8be131f3.png){: .align-center width="40%"}

- `legend` : Dict타입으로 전달하며 우측상단이나 중앙하단에 표현하는 것이 좋다.
- 데이터를 전달할때, 각각의 label도 함께 전달한다. (Dict)

```python
data = {'A': 50, 'B': 45, 'C': 15}

fig = plt.figure(
    FigureClass=Waffle, 
    rows=5, 
    values=data, 
    legend={'loc': 'upper left', 'bbox_to_anchor': (1.1, 1)}
)
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153742420-9c849bdb-88c8-46bd-9284-5b31a48815d4.png){: .align-center width="40%"}

- `cmap_name` : 컬러맵을 통해서 와플차트의 색상을 변경할 수 잇따.

```python
data = {'A': 50, 'B': 45, 'C': 15}

fig = plt.figure(
    FigureClass=Waffle, 
    rows=5, 
    values=data, 
    cmap_name='tab10',
    legend={'loc': 'lower left', 'bbox_to_anchor': (0, -0.4), 'ncol': len(data), 'framealpha': 0},
)

plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153742425-8b24f600-d8f6-43bd-a5f8-ddc54039efb4.png){: .align-center width="40%"}

- `colors` : 직접 색상을 지정

```python
data = {'A': 50, 'B': 45, 'C': 15}

fig = plt.figure(
    FigureClass=Waffle, 
    rows=5, 
    values=data, 
    colors=["#232066", "#983D3D", "#DCB732"],
    legend={'loc': 'lower left', 'bbox_to_anchor': (0, -0.4), 'ncol': len(data), 'framealpha': 0},
)

plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153742433-374d485f-8fd3-4308-9931-49ca6678e3f1.png){: .align-center width="40%"}

- `starting_location` : 4개의 꼭지점을 방위를 기준으로 시작점을 잡을 수 있다.

```python
data = {'A': 50, 'B': 45, 'C': 15}

fig = plt.figure(
    FigureClass=Waffle, 
    rows=5, 
    values=data, 
    legend={'loc': 'lower left', 'bbox_to_anchor': (0, -0.4), 'ncol': len(data), 'framealpha': 0},
    starting_location='SE' # NW, SW, NE and SE
)

plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153742440-c97a8fc8-0356-4f67-b2e4-f259e40f44bd.png){: .align-center width="40%"}

- `vertical` : 기본적으로 가로로 진행되며, 세로로 진행하고 싶다면 True를 전달한다.

```python
data = {'A': 50, 'B': 45, 'C': 15}

fig = plt.figure(
    FigureClass=Waffle, 
    rows=5, 
    values=data, 
    legend={'loc': 'lower left', 'bbox_to_anchor': (0, -0.4), 'ncol': len(data), 'framealpha': 0},
    vertical=True
)

plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153742441-74ec1137-fa98-46f6-817f-7b926c13ab49.png){: .align-center width="40%"}

- `block_arranging_style` : 어떤식으로 블록을 채워나갈지 결정한다. 기본은 snake방식을 사용

```python
fig = plt.figure(
    FigureClass=Waffle,
    rows=7,
    values=data, 
    legend={'loc': 'lower left', 'bbox_to_anchor': (0, -0.4), 'ncol': len(data), 'framealpha': 0},
    block_arranging_style= 'new-line',
)
```

![image](https://user-images.githubusercontent.com/91870042/153742444-79ea804f-03de-48fd-ac36-962e716696f6.png){: .align-center width="40%"}

- `icon` : 아이콘의 이름
- `icon_legend` : 아이콘을 범례로 사용할 것인지. 사용하지 않으면 색상으로만 표현된다.
- `font_size` : 아이콘의 크기

```python
fig = plt.figure(
    FigureClass=Waffle, 
    rows=10,     
    values=data, 
    legend={'loc': 'lower left', 'bbox_to_anchor': (0, -0.4), 'ncol': len(data), 'framealpha': 0},
    icons='child',
    icon_legend=True,
    font_size=15,
)
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153742448-1414efb2-02a9-45a9-9115-2556020cae66.png){: .align-center width="40%"}

- 2개의 집합을 사용해서 벤다이어 그램을 그리는 경우

```python
from matplotlib_venn import venn2
venn2(subsets = (3, 2, 1))
```

![image](https://user-images.githubusercontent.com/91870042/153742453-6fc757f2-cc03-4318-b3d8-6145a26cf38f.png){: .align-center width="40%"}

- 1개만 포함되는 인덱스
    - 1 : 001
    - 2 : 010
    - 4 : 100
- 2개가 포함되는 인덱스
    - 3 : 011
    - 5 : 101
    - 6 : 110
- 3개가 포함되는 인덱스
    - 7 : 111

```python
venn3(subsets = (1, 2, 3, 4, 5, 6, 7), set_labels = ('Set1', 'Set2', 'Set3'))
```

![image](https://user-images.githubusercontent.com/91870042/153742463-f993f9d2-e8ad-4f18-b47f-22b47bbedc3c.png){: .align-center width="40%"}

- `set` 으로 전달하면, 자동으로 그 교집합을 계산해서 보여준다.

```python
set1 = set(['A', 'B', 'C', 'D'])
set2 = set(['B', 'C', 'D', 'E'])
set3 = set(['C', 'D',' E', 'F', 'G'])

venn3([set1, set2, set3], ('Set1', 'Set2', 'Set3'))
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153742469-8a2eadd1-0f86-4e11-97ac-61aea8a957d8.png){: .align-center width="40%"}