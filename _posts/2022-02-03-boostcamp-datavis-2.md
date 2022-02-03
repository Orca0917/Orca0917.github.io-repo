---
title:  "[Data Visualization] Python과 Matplotlib"
excerpt: "Python의 대표적인 시각화 패키지인 Matplotlib를 통한 간단한 시각화 실습"

categories:
  - datavis
tags:
  - [AI, Naver, BoostCamp, Python, Matplotlib]
toc: true
toc_sticky: true
 
date: 2022-02-03 01:00:00
last_modified_at: 2022-02-03 01:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Matplotlib
`Matplotlib`는 파이썬에서 사용할 수 있는 시각화 라이브러리이다. 현재 사용되고 있는 다양항 데이터 분석 및 머신러닝은 Python에서 이루어지고 있기 때문에 사용하기가 좋다. 또한, 다른 패키지인 `numpy`와 `scipy`를 베이스로 하기 때문에 다양한 라이브러리와 호환성이 좋다.

matplotlib이외에도, `seaborn`, `plotly`, `bokeh`, `altair` 등의 시각화 라이브러리가 존재한다. 하지만, 가장 범용성이 높고 근본이 되는 라이브러리는 matplotlib이다.

```py
import numpy as np 
import matplotlib as mpl
import matplotlib.pyplot as plt
```
<br>

# 기본 plot
## figure & axes
matplotlib에서 사용하는 시각화는 `figure`라는 큰 틀에 `ax`라는 서브플롯을 추가해서 만든다. figure는 하나의 큰 틀을 말하는 것이기 때문에 직접 그래프를 그리고 디자인할 공간인 `ax`를 지정해서 만들어 줘야한다.

![image](https://user-images.githubusercontent.com/91870042/152316157-ae8c6ca2-5459-4e64-b64c-f553371ce678.png){: .align-center width="70%"}

```py
fig = plt.figure()
plt.show() # figure에 나타는 그래프를 화면에 표시
```
![temp](https://user-images.githubusercontent.com/91870042/152317089-d6e1873e-bc70-4e16-849f-03aa5e0eb9a8.png){: .align-center width="40%"}

`figure`의 크기를 조절하고 싶다면, 가로와 세로의 길이를 **inch단위**로 조절할 수 있다.
```py
fig = plt.figure(figsize=(12, 7))
ax = fig.add_subplot()
plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152317683-07c878cb-71f5-4ce1-aa97-85a5e22d3770.png){: .align-center width="70%"}

하나의 `figure`에 2개 이상의 그래프를 그리고 싶다면, `subplot`을 하나더 추가하면 된다. 추가할 때, 몇개의 칸을 나누고 어디에 그릴지 까지 지정해야 한다.

```py
fig = plt.figure()
ax = fig.add_subplot(121)  # ax = fig.add_subplot(1, 2, 1)
ax = fig.add_subplot(122)  # ax = fig.add_subplot(1, 2, 2)
plt.show()
-----------------------------------------------------------
fig = plt.figure()
ax = fig.add_subplot(211)  # ax = fig.add_subplot(2, 1, 1)
ax = fig.add_subplot(212)  # ax = fig.add_subplot(2, 1, 2)
plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152318052-cc023657-b0ef-4837-af8d-61fcd22f1612.png){: width="49%"} ![temp](https://user-images.githubusercontent.com/91870042/152318350-d473a8de-36f8-40bf-adc8-9ceb9fe4518f.png){: width="49%"}

## plt로 그래프그리기
plt를 사용해서 그래프를 그리는 방법에는 2가지가 있다. 먼저 그래프별로 객체를 생성하는 것이 아닌 하나의 객체에 대해서 먼저 다 설정을 완료하면서 진행하는 순차적방법이 있다. 하지만 이는 Python의 장점을 살린 **Pythonic Code**에 맞지 않아서 객체지향 API로 만드는 것이 일반적이다.

- `pyplot API` : 순차적 방법
- `객체지향(Object Oriendted, OO) API`: 그래프에서 각 객체에 대해 직접적으로 수정'

```py
# pyplot API : 순차적 방법 을 사용하는 경우
x1 = [1, 2, 3]
x2 = [3, 2, 1]

fig = plt.figure()
ax1 = fig.add_subplot(211) 
plt.plot(x1) # ax1에 그리기
ax2 = fig.add_subplot(212) 
plt.plot(x2) # ax2에 그리기

plt.show()
```
```py
# 객체지향 API 를 사용하는 경우
x1 = [1, 2, 3]
x2 = [3, 2, 1]

fig = plt.figure()
ax1 = fig.add_subplot(211) 
ax2 = fig.add_subplot(212) 
ax1.plot(x1) 
ax2.plot(x2)

plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152319343-801a02bd-f3bb-49b2-8fe6-6fb7196cd0f1.png){: .align-center}

pyplot의 `plt`와 `ax`가 가진 멤버함수는 거의 유사하지만 동일하지는 않다. 그렇기 때문에 `plt` 에서 된다고 해서 `ax` 에서도 적용이 반드시 가능한 것은 아니다. 

위에서 plt를 사용해서 그래프를 그리다가 객체로 전환하기 위해서 `plt.gcf().get_axes()`로 서브플롯의 객체를 받아올 수 있다.

<br>

# Plot의 요소
## 하나의 subplot에서 여러개 그리기
`ax`를 사용해서 하나의 subplot내에서 여러개의 그래프를 그리는 것이 가능하다. 하나의 subplot에 동일한 종류의 그래프가 여러개 그려질 경우, 아래와 같이 자동으로 색상이 구분되어서 나타난다.
```py
x = np.linspace(0, 2 * np.pi, 100)

fig = plt.figure()
ax = fig.add_subplot(111)
ax.plot(x, np.sin(x))
ax.plot(x, np.cos(x))

plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152320681-eebdb3b4-e6e5-4a40-b024-33c233735cec.png){: width="49%"} ![temp](https://user-images.githubusercontent.com/91870042/152321182-b415123c-6ece-4f69-9086-7c6368fb8967.png){: width="49%"}

하지만, 하나의 subplot내에서 서로 종류가 다른 그래프가 그려진다면 색상구분이 이루어지지 않는다. 그렇기 때문에 직접 색상을 명시해줄 필요가 있다.

## 색상 지정
그래프의 색상을 지정하기 위해서는 `color` 파라미터를 통해서 전달한다.
```py
fig = plt.figure()
ax = fig.add_subplot(111) 
# 3개의 그래프 동시에 그리기
ax.plot([1, 1, 1], color='r') # 한 글자로 정하는 색상
ax.plot([2, 2, 2], color='forestgreen') # color name
ax.plot([3, 3, 3], color='#000000') # hex code (BLACK)
plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152325459-139807c5-028d-45c4-8d25-bfae2041296b.png){: .align-center}

## 텍스트 표시
### legend: 범례
하나의 subplot에 여러개의 그래프를 넣으면 각각의 그래프가 정확히 어떤것을 말하고 있는지 모르는 경우가 생긴다. 그렇기 때문에 각각이 무엇을 나타내는지 표현하는 `범례`를 표시해주는 것이 좋다.

범례를 표시하기 위해서는 `ax.legend()` 부분만 작성하면 보여준다. 범례가 나타나는 위치는 자동으로 가장 적절한 위치에 배치된다.

```py
fig = plt.figure()
ax = fig.add_subplot(111) 
ax.plot([1, 1, 1], label='1') 
ax.plot([2, 2, 2], label='2') 
ax.plot([3, 3, 3], label='3')
ax.set_title('Basic Plot')
ax.legend()
plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152325536-81459337-59b3-4007-8e6e-1bd9d3449f62.png){: .align-center}

### title: 제목
각 그래프의 제목을 설정할 때는 `ax.set_title('')`을 사용했었다. 하지만, 여러개의 subplot을 담고 있는 figure의 제목을 설정할 때는 `fig.suptitle('')`을 사용하면 된다.
```py
fig = plt.figure()
ax1 = fig.add_subplot(121)
ax2 = fig.add_subplot(122)

ax1.set_title('ax1')
ax2.set_title('ax2')

fig.suptitle('fig')
```
![temp](https://user-images.githubusercontent.com/91870042/152325777-19b483b0-16a3-481f-bcc8-06f712ebf4f1.png){: .align-center}

### ticks: 축 정보
축 정보를 수치정보가 아닌 원하는 값으로 설정하고 싶다면, `set_xticks` `set_xticklabels`를 이용하면 된다.

- `set_xticks()` : x축의 좌표값을 특정 숫자값으로 변경하고 싶은 경우
- `set_xticklabels()`: 위에서 설정한 값에 맞게 축에 적히는 텍스트를 수정한다.

```py
fig = plt.figure()
ax = fig.add_subplot(111) 
ax.plot([1, 1, 1], label='1') 
ax.plot([2, 2, 2], label='2') 
ax.plot([3, 3, 3], label='3')

ax.set_title('Basic Plot')
ax.set_xticks([0, 1, 2])
ax.set_xticklabels(['zero', 'one', 'two'])
ax.legend()

plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152325908-fecadfd1-09c5-4cf1-ba3e-c7908db4424d.png){: .align-center}


### text, annotate
그래프 내부에 원하는 글자를 넣고 싶거나 주석을 달고 싶을 때 사용한다.

- `text`
- `annotate`

```py
fig = plt.figure()
ax = fig.add_subplot(111) 
ax.plot([1, 1, 1], label='1') 
ax.plot([2, 2, 2], label='2') 
ax.plot([3, 3, 3], label='3')

ax.set_title('Basic Plot')
ax.set_xticks([0, 1, 2])
ax.set_xticklabels(['zero', 'one', 'two'])

ax.text(x=1, y=2, s='This is Text') # 텍스트 지정
# ax.annotate(text='This is Annotate', xy=(1, 2)) :: annotate를 사용

ax.legend()

plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/152326024-4eec1340-ad51-4151-a7c6-8c41e6975cea.png){: .align-center}

annotate를 사용하면 텍스트 뿐만아니라 **화살표**도 사용할 수 있다. 화살표는 사용하기가 어려워서 사용하지 않고, 주로 텍스트만 삽입하는 경우가 대부분이다.

```py
fig = plt.figure()
ax = fig.add_subplot(111) 
ax.plot([1, 1, 1], label='1') 
ax.plot([2, 2, 2], label='2') 
ax.plot([3, 3, 3], label='3')

ax.set_title('Basic Plot')
ax.set_xticks([0, 1, 2])
ax.set_xticklabels(['zero', 'one', 'two'])

ax.annotate(text='This is Annotate', xy=(1, 2),
           xytext=(1.2, 2.2), 
            arrowprops=dict(facecolor='black'),
           )
ax.legend()

plt.show()
```

![temp](https://user-images.githubusercontent.com/91870042/152326152-34b14c88-0101-4ccb-95ef-8f7e6b01378c.png){: .align-center}


