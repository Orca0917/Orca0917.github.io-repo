---
title:  "[Data Visualization] Pie Chart"
excerpt: "Matplotlib의 Pie Chart"

categories:
  - datavis
tags:
  - [AI, Naver, BoostCamp, Python, Matplotlib]
toc: true
toc_sticky: true
 
date: 2022-02-11 01:00:00
last_modified_at: 2022-02-11 01:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Pie Chart

![image](https://user-images.githubusercontent.com/91870042/153742270-34ed332a-534a-40ff-b3a7-b21ea8945c9b.png){: .align-center width="70%"}


- 원을 부채꼴로 분할하여 표현하는 통계차트
    - 전체를 백분위로 나타낼때 사용하기가 좋다.
- 가장 많이 사용하는 차트지만, 지양하는 것이 좋다
    - 각 feature별로 비교하기가 어렵다
    - 유용성이 떨어진다
    - 각도가 길이보다 비교하기가 어렵기 때문에, bar plot이 유용하다.
    - 단독적으로 사용하기 보다는 함께 사용하는 것을 권장한다.
    - `waffle chart` 를 사용하는 것이 비교하는데 있어서 더 유용하다.

## Pie Chart 응용

### Donut Chart

![image](https://user-images.githubusercontent.com/91870042/153742272-3c12f2fe-74a3-4d65-84a3-5131f4e24d17.png){: .align-center width="70%"}

- 중간이 뚫려있는 Pie Chart
- 기존의 Pie Chart의 답답함을 해소시켜주기 때문에 디자인 적으로 선호된다.
- Presentation이나, 인포그래픽에서 종종 사용된다
- `Plotly` 에서 쉽게 사용할 수 있다.

### Sunburst Chart

![image](https://user-images.githubusercontent.com/91870042/153742282-aaded025-943c-4a57-b1db-6f8bdc62457a.png){: .align-center width="70%"}

- 햇살(Sunburst)를 닮은 차트
- 계층적 데이터를 시각화하는데 많이 사용된다.
- 구현 난이도에 비해서 화려하다는 장점이 있지만, 가독성이 좋지는 않다
- 오히려 Sunburst를 사용하기 보다는 Treemap을 사용한다.
- `Plotly` 에서 쉽게 사용할 수 있다.

<br>

# 실습

- `ax.pie()` 를 사용해서 pie plot을 그린다.

```python
labels = ['A', 'B', 'C', 'D']
data = np.array([60, 90, 45, 165]) # total 360

fig, ax = plt.subplots(1, 1, figsize=(7, 7))
ax.pie(data, labels=labels)
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153742286-1c9ac389-97b2-4371-bfb8-5223b7175578.png){: .align-center width="40%"}

- 동일한 데이터에 대해서 Pie chart와 Bar Chart의 장단점은 다음과 같다.
    - 장점: Pie Chart는 비율에 대한 정보를 제공해줄 수 있다.
    - 단점: 각 feature에 대해서 구체적인 양의 비교가 어렵다.

```python
fig, axes = plt.subplots(1, 2, figsize=(12, 7))

axes[0].pie(data, labels=labels)
axes[1].bar(labels, data)

plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153742290-305866c4-b63e-40f8-ada6-58c391a2fd9b.png){: .align-center width="70%"}

- `startangle` : feature의 시작 각도를 지정할 수 있다.

```python
fig, ax = plt.subplots(1, 1, figsize=(7, 7))
ax.pie(data, labels=labels, startangle=90)
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153742295-81fd17f1-1e13-4f35-96be-63c37b432efa.png){: .align-center width="40%"}

- `explode` : pie 한조각을 분리해서 강조할 수 있다.

```python
fig, ax = plt.subplots(1, 1, figsize=(7, 7))
explode = [0, 0, 0.2, 0]

ax.pie(data, labels=labels, explode=explode, startangle=90)
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153742298-24ddea6a-927b-4e3a-a14e-1991a12d7fa7.png){: .align-center width="40%"}

- `shadow` : Pie Chart의 그림자를 조절

```python
fig, ax = plt.subplots(1, 1, figsize=(7, 7))
explode = [0, 0, 0.2, 0]

ax.pie(data, labels=labels, explode=explode, startangle=90, shadow=True)
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153742302-43656d90-dbad-45eb-9c08-d5ff1370b537.png){: .align-center width="40%"}

- `autopct` : 각 데이터가 얼만큼을 차지하고 있는지 나타낼 수 있다.

```python
fig, ax = plt.subplots(1, 1, figsize=(7, 7))
explode = [0, 0, 0.2, 0]

ax.pie(data, labels=labels, explode=explode, startangle=90,
      shadow=True, autopct='%1.1f%%')
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153742305-dc5c9d66-9171-4cc1-bb76-2188f90bacad.png){: .align-center width="40%"}

- `labeldistance` : Pie Chart의 label이 너무 길경우, 글자가 Pie Chart와 겹칠 수 있다. 그것을 방지하기 위해서 Pie Chart와의 거리를 더 멀게 할 수 있다.
- `rotatelabels` : label을 회전

```python
fig, ax = plt.subplots(1, 1, figsize=(7, 7))
explode = [0, 0, 0.2, 0]

ax.pie(data, labels=labels, explode=explode, startangle=90,
      shadow=True, autopct='%1.1f%%', labeldistance=1.15, rotatelabels=90)
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153742309-ea7f9e27-d95f-4505-b495-d166d5963d0c.png){: .align-center width="40%"}

- `counterclock` : feature들의 배열을 반시계방향이 아닌, 시계방향으로 나열해볼 수 있다.

```python
fig, ax = plt.subplots(1, 1, figsize=(7, 7))
explode = [0, 0, 0.2, 0]

ax.pie(data, labels=labels, explode=explode, startangle=90, counterclock=False)
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153742312-2f41f1c8-6615-4932-95fc-019f7d1d00bd.png){: .align-center width="40%"}

- `radius` : 원 자체의 크기를 결정할 수 있다.

```python
fig, axes = plt.subplots(1, 3, figsize=(12, 7))
explode = [0, 0, 0.2, 0]

for size, ax in zip([1, 0.8, 0.5], axes):
    ax.pie(data, labels=labels, explode=explode, startangle=90, counterclock=False,
           radius=size
          )
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153742319-71c8ce38-0f94-4229-ac12-ae81eea84705.png){: .align-center width="70%"}

- Donut Chart는 기존의 Pie plot에 또 하나의 원을 그리는 방법으로 구현한다.

```python
fig, ax = plt.subplots(1, 1, figsize=(7, 7))

ax.pie(data, labels=labels, startangle=90,
      shadow=True, autopct='%1.1f%%')

# 좌표 0, 0, r=0.7, facecolor='white'
centre_circle = plt.Circle((0,0),0.70,fc='white')
ax.add_artist(centre_circle)

plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153742327-f300a06a-ffde-490c-a56c-3e41e2a3cb8d.png){: .align-center width="40%"}

- `pctdistance` : Percentage가 원의 중심에서 얼마나 떨어트릴지 결정할 수 있다.
- `textprops` : 텍스트의 색상과 같은 정보를 설정할 수 있다.

```python
fig, ax = plt.subplots(1, 1, figsize=(7, 7))

ax.pie(data, labels=labels, startangle=90,
      shadow=True, autopct='%1.1f%%', pctdistance=0.85, textprops={'color':"w"})

# 좌표 0, 0, r=0.7, facecolor='white'
centre_circle = plt.Circle((0,0),0.70,fc='white')
ax.add_artist(centre_circle)

plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153742331-90a57a59-8a09-45d9-a0b7-cb88651e53c2.png){: .align-center width="40%"}