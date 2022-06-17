---
title:  "[Data Visualization] Polar Coordinate"
excerpt: "Matplotlib의 Polar Plot, Radar Plot"

categories:
  - datavis
tags:
  - [AI, Naver, BoostCamp, Python, Matplotlib]
toc: true
toc_sticky: true
 
date: 2022-02-11 00:00:00
last_modified_at: 2022-02-11 00:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Polar Plot

![image](https://user-images.githubusercontent.com/91870042/153741886-28a2775c-2b53-4966-bcd0-bce8388849b8.png){: .align-center width="40%"}


- 극좌표계를 사용하는 plot
    - 거리(r), 각(Theta)를 사용하여 시각화
- 회전, 주기성을 표현하기에 적합하다
- `projection=polar` 을 추가해서 사용가능
- Scatter, Line, Bar plot에서 모두 사용 가능

![image](https://user-images.githubusercontent.com/91870042/153741890-0d0b90ac-ffab-468e-b87c-d8653403f800.png){: .align-center width="70%"}

- Matplotlib 로고에서도 사용
    - 공식로고도 matplotlib로 그릴수가 있다.

## DataConverting

- 꼭 극좌표계에서 그릴필요는 없고, 직교좌표계에서도 사용할 수 있다.

![image](https://user-images.githubusercontent.com/91870042/153741893-f6f58b39-e62d-4e1e-b60d-fed00906b829.png){: .align-center width="50%"}

- 삼각함수로 좌표를 변환해서 사용하면 된다.

# Radar Plot

## Radar Plot

![image](https://user-images.githubusercontent.com/91870042/153741895-3d1c69e2-72b0-4a73-a043-3727474649b6.png){: .align-center width="70%"}

- 극좌표계를 사용하는 대표적인 차트
- 별 모양으로 생겨서 Star Plot으로 불리기도 한다.
- 중심점을 기준으로 N개의 변수 값을 이어서 면적으로 표현한다.
- 데이터의 질적인 부분을 표현하기가 좋다.
    - 캐릭터의 강함
    - 운동선수 분석
    - 비교에도 적합

## Radar Plot 주의점

- 각 feature는 독립적이며, 척도가 같아야 한다.
    - 순서형 변수와 수치형 변수가 함께 있다면 고려가 필요하다.
- 예를 들어, 자동차를 보자.
    - 안전성 평가와 소비자 만족도등은 5점 만점으로 표현되고
    - 최대 속도와 연비등은 10km/L 처럼 절대적 수치로 나타낸다.
    - 이런 경우, 하나의 Radar Plot에 나타내는 것은 좋지 않다.

![image](https://user-images.githubusercontent.com/91870042/153741900-e1333288-c722-4326-aae8-90a4113c5b8f.png){: .align-center width="70%"}

- 면적 자체가 가진 의미는 크지가 않다.
    - 다각형의 면적이 중요하지만 feature의 순서에 따라서 많이 달라지게 된다.

![image](https://user-images.githubusercontent.com/91870042/153741907-05ff8a61-a7cf-4edb-a171-e2b6695af098.png){: .align-center width="70%"}

- feature의 수가 많아지면 가독성이 떨어진다.

<br>

# 실습

```python
fig = plt.figure()
ax = fig.add_subplot(111, projection='polar')
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153741910-01ec5142-c30c-42a0-a19a-3e8967063d93.png){: .align-center width="40%"}

- `projection='polar'` 대신에, `polar=True` 를 사용할 수 있다.

```python
fig = plt.figure()
ax = fig.add_subplot(111, polar=True)
plt.show()
```

- `set_rmax` ,`set_rmin` : 반지름 조정
- `set_rticks` : 반지름 표기 grid조정

```python
fig = plt.figure()
ax = fig.add_subplot(111, polar=True)

ax.set_rmax(2)
# ax.set_rmin(1)
ax.set_rticks([1, 1.5, 2])  

plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153741914-de0f6d13-f7ed-44ab-b9d5-20f704ac8a98.png){: width="49%"} ![image](https://user-images.githubusercontent.com/91870042/153741917-8cffab04-2d06-418c-96f9-2e58848dcd6c.png){: width="49%"}

- `set_rmin` 을 한다고 하더라도 도넛과 같은 형태가 되는 것이 아니라, 시작점의 기준이 바뀌는 것이다.
- `set_rlabel_position` : 반지름 label이 적히는 위치의 각도를 조정한다.

```python
fig = plt.figure()
ax = fig.add_subplot(111, polar=True)
ax.set_rlabel_position(-90)  
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153741924-c8d731f3-0106-4c2f-8832-793719f9b867.png){: .align-center width="40%"}

- 각도를 조절하여 부채꼴의 모양만 나타내고 싶은 경우,
    - `set_thetamin` : 각도의 min값
    - `set_thetamax` : 각도의 max값

```python
fig = plt.figure()
ax = fig.add_subplot(111, polar=True)

ax.set_thetamin(45)
ax.set_thetamax(135)
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153741928-4e99907c-4f92-49e3-ba68-ebf881abf574.png){: .align-center width="40%"}

- scatter plot에서 사용하는 polar plot

```python
np.random.seed(19680801)

N = 100
r = 2 * np.random.rand(N)
theta = 2 * np.pi * np.random.rand(N)
area = 200 * r**2
colors = theta

fig = plt.figure()
ax = fig.add_subplot(111, projection='polar')
c = ax.scatter(theta, r, c=colors, s=area, cmap='hsv', alpha=0.75)
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153741932-7e7a968b-7542-41f8-9a86-9726fce0b87d.png){: .align-center width="40%"}

- bar plot에서 사용하는 polar plot
- 2 값의 분포가 얼마나 차이나는지 알아보기는 힘들기 때문에 주의해서 사용하는 것이 좋다.

```python
np.random.seed(19680801)

N = 6
r = np.random.rand(N)
theta = np.linspace(0, 2*np.pi, N, endpoint=False)

fig = plt.figure()
ax = fig.add_subplot(111, projection='polar')
ax.bar(theta, r, width=0.5, alpha=0.5)
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153741945-f4b0fc49-8b81-4945-a902-3a0efeb9b631.png){: .align-center width="40%"}

- plot (선그래프)를 polar plot에 그리기

```python
np.random.seed(19680801)

N = 1000
r = np.linspace(0, 1, N)
theta = np.linspace(0, 2*np.pi, N)

fig = plt.figure()
ax = fig.add_subplot(111, projection='polar')
ax.plot(theta, r)

plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153741946-1ceb49f2-2807-456e-b2a1-defb763b13a4.png){: .align-center width="40%"}

- `fill` 을 사용해서 area chart 표현하기

```python
np.random.seed(19680801)

N = 1000
r = np.linspace(0, 1, N)
theta = np.linspace(0, 2*np.pi, N)

fig = plt.figure()
ax = fig.add_subplot(111, projection='polar')
ax.fill(theta, r) # <-- 이부분 추가
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153741953-2472c819-30cf-4104-8893-ed32c52bc401.png){: .align-center width="40%"}

- 방금 사용한, `fill` 을 적합하게 사용하면 Radar Chart를 사용할 수 있다.
- 실습에서는 Pockemon with stat 데이터셋을 사용한다.

```python
pokemon = pd.read_csv('./pokemon.csv')
pokemon.head()
```

![image](https://user-images.githubusercontent.com/91870042/153741962-dc0373f8-46ed-4cbf-a1c6-916db25bc66e.png){: .align-center width="70%"}

- 포켓몬의 데이터 통계 살펴보기

```python
pokemon.describe()
```

![image](https://user-images.githubusercontent.com/91870042/153741971-26678e3e-dedd-4908-9b4e-860e967fed83.png){: .align-center width="70%"}

- 데이터셋을 자세히 보면, **HP, Attack, Defense, Sp.Atk, Sp.Def, Speed** 이 6가지 feature가 포켓몬의 역량을 나타내는 수치이다.
- 이 6개의 feature에 대한 RadarChar를 그려보자
- 먼저, 해당하는 feature에 대한 값만 추출해 와야한다.

```python
stats = ["HP", "Attack", "Defense", "Sp. Atk", "Sp. Def", "Speed"]
values = pokemon.iloc[0][stats].to_list()
print(values)
-----------------------------------------------------
[45, 49, 49, 65, 65, 45]
```

- 각을 나타내기 위해서 전체 $`2\pi`$ 를 6등분한다.

```python
theta = np.linspace(0, 2*np.pi, 6, endpoint=False)
print(theta)
```

- 이제, `fill` 과 `plot` 을 사용해서 그려볼 수 있다.

```python
fig = plt.figure()
ax = fig.add_subplot(111, projection='polar')

ax.plot(theta, values)
ax.fill(theta, values, alpha=0.5)
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153741976-5a9c3074-7aba-4cda-a2f8-73ab99ca86fd.png){: .align-center width="40%"}

- 위에서 보면 결과물의 테두리가 마지막이 비어있는 것을 알 수 있다.
- 이를 해결하기 위해서 각도 list와 데이터 list마지막에 첫번째 값을 각각 추가해준다.

```python
fig = plt.figure()
ax = fig.add_subplot(111, projection='polar')

values.append(values[0]) # <--- 첫번째 데이터를 추가
theta = theta.tolist() + [theta[0]] # <--- 첫번째 각도를 추가

ax.plot(theta, values)
ax.fill(theta, values, alpha=0.5)

plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153741980-111208a1-25d2-40b0-8ce1-a9d8b7adbae2.png){: .align-center width="40%"}

- 위에서 각 feature가 무엇을 나타내는지 모르고, 각 feature간의 배경선을 맞춰주기 위해서 다음 2가지 메소드를 사용해볼 수 있다.
    - `set_thetagrids` : 각도에 따른 그리드 및 ticklabels 변경
    - `set_theta_offset` : 시작하는 각도를 변경

```python
fig = plt.figure(figsize=(4, 4))
ax = fig.add_subplot(111, projection='polar')

values = pokemon.iloc[0][stats].to_list()
values.append(values[0])

ax.plot(theta, values)
ax.fill(theta, values, alpha=0.5)

ax.set_thetagrids([n*60 for n in range(6)], stats) # <-- 그리드 변경, label설정
ax.set_theta_offset(np.pi/2) # <-- 시작각도 변경

plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153741983-256ddfe1-67cf-4668-88d5-5f6c19ee7f07.png){: .align-center width="40%"}

- 포켓몬의 진화에 따른 능력 변화 살펴보기

```python
fig = plt.figure(figsize=(14, 4))

for idx in range(3):
    ax = fig.add_subplot(1,3,idx+1, projection='polar')

    values = pokemon.iloc[idx][stats].to_list()
    values.append(values[0])

    ax.plot(theta, values, color='forestgreen')
    ax.fill(theta, values, color='forestgreen', alpha=0.3)
    
    ax.set_rmax(100)
    ax.set_thetagrids([n*60 for n in range(6)], stats)
    ax.set_theta_offset(np.pi/2)
    
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153741990-ecef9999-0444-4b87-80e6-6a5d01fea3af.png){: .align-center width="70%"}

```python
fig = plt.figure(figsize=(7, 7))

ax = fig.add_subplot(111, projection='polar')

for idx in range(3):

    values = pokemon.iloc[idx][stats].to_list()
    values.append(values[0])

    ax.plot(theta, values, color='forestgreen')
    ax.fill(theta, values, color='forestgreen', alpha=0.3)
    
ax.set_rmax(110)
ax.set_thetagrids([n*60 for n in range(6)], stats)
ax.set_theta_offset(np.pi/2)
    
plt.show()
```

![image](https://user-images.githubusercontent.com/91870042/153741996-25a0c3a0-de7e-41d1-af17-cbe7bd3c7a45.png){: .align-center width="40%"}