---
title:  "[Data Visualization] Facet"
excerpt: "다양한 요소를 사용해 목적에 맞고 미적으로 더 나은 시각화를 만드는 방법. 여러 각도에서 데이터를 바라볼 수 있는 시각화의 생성 방법"

categories:
  - datavis
tags:
  - [AI, Naver, BoostCamp, Python, Matplotlib]
toc: true
toc_sticky: true
 
date: 2022-02-04 02:00:00
last_modified_at: 2022-02-04 02:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Facet
## Multiple Veiw의 필요성
`Facet`은 화면에 대한 분할을 의미하며 화면 상에 View를 분할 및 추가하여 다양한 관점을 전달하는 방법이다.
- 같은 데이터셋에 **서로 다른 인코딩**을 통해 다른 인사이트를 제공
- **같은 방법으로 동시에** 여러 feature을 확인
- 큰 틀에서 볼 수 없는 **부분 집합을 세세**하게 보여줄 수 있다.

<br>

# Matplotlib에서 구현
## figure & axes
`Figure`는 큰 틀이며, `ax`는 각 플롯이 들어가는 공간을 말한다.

![image](https://user-images.githubusercontent.com/91870042/152551025-825a6138-a200-419d-807c-2aa4f3289a16.png){: .align-center width="70%"}

## N x M subplots
가장 쉽게 구현할 수 있는 3가지 방법
- `plt.subplot()`
- `plt.figure() + fig.add_subplot()`
- `plt.subplots()`

쉽게 조정할 수 있는 요소: `figuresize`, `dpi`, `sharex, sharey`, `squeeze`, `aspect`

## Gird Spec의 활용

![image](https://user-images.githubusercontent.com/91870042/152552006-94d0595c-ff2a-4afc-a387-b699b468ca68.png){: width="49%"} ![image](https://user-images.githubusercontent.com/91870042/152552205-760468a3-5de1-4fa0-8944-4e61bddd4fc4.png){: width="49%"}

좌측의 4x4크기의 Grid에서 다른 사이즈로 직접 지정해서 오른쪽 처럼 만들 수 있는 방법에는 2가지가 있다.

1. `fig.add_grid_spec()`  
   
   이 함수는 numpy의 slicing을 생각하면 쉽게 구현할 수 있다. 내가 지정하고 싶은 구간을 선택을 하면 그 구간이 하나의 subplot으로 생성이된다. ex. `axes[1:, :3]`
2. `fig.subplot2grid()`  

   이 함수는 시작위치 x, y와 각각의 범위 dx, dy를 지정하여 subplot의 크기를 지정한다. 예를 들어, (0, 0), 1, 3 의 값이 전달되었다면 0,0 에서 시작되어 x축으로 1만큼, y축으로 3만큼의 범위 (가로로 3칸)를 하나의 subplot으로 지정한다.

## 내부에 그리기
ax내부에 subplot을 추가하는 방법 중 하나로 미니맵과 같은 형태로 추가하거나 외부 정보를 적은 비중으로 추가할 때 사용된다. `ax.inset_axes()`를 사용해서 구현한다.

![image](https://user-images.githubusercontent.com/91870042/152553468-abd43b2c-3220-40eb-be22-de96a55b69f8.png){: .align-center width="35%"}


## 외부에 그리기
그리드를 사용하지 않고, 사이드에 subplot을 추가하는 것으로서 `make_axes_locatable(ax)`를 사용한다. 방향의 통계정보를 제공할수 있으며 이 subplot에 제목과 눈금라벨등 텍스트 추가도 가능하다.

![image](https://user-images.githubusercontent.com/91870042/152553604-afd600a4-a1e9-447c-b7a1-2a4f95f964a9.png){: .align-center width="40%"}

<br>

# 실습

## subplot의 생성
```py
# 방법1
fig = plt.figure()
ax = fig.add_subplot(121)
ax = fig.add_subplot(122)

# 방법2
fig, axes = plt.subplots(1, 2)
```

## Figures & Ax props
- `DPI`: DPI는 Dots Per Inch로 한글로 **해상도**를 의미한다. 기본값으로 100으로 지정되어 있으며 더 늘릴수록 고해상도의 plot을 얻을 수 있다.

    ```py
    fig = plt.figure(dpi=200)
    fig.savefig('saved.png', dpi=200) # 이미지의 저장
    ```


- `sharex`, `sharey`: 하나의 fig내에 존재하는 여러개의 `ax`에 대해서 축을 공유할 수 있게 하는 속성이다.

    ```py
    fig, axes = plt.subplots(1, 2, sharey=True) # y축의 눈금까지 모두 공유
    ax[1] = fig.add_subplot(122, sharey=ax[0]) # y축의 눈금을 표시하되 범위를 공유
    ```


- `squeeze`, `flatten`: squeeze는 차원을 늘리는 것을 말한다. squeeze옵션을 주게 되면 항상 `axes`를 2차원의 형태로 받기 때문에 반목문으로 ax에 접근할 때 유리하다. 반면에 `flatten`은 2차원으로 받은 `axes`에 대해서 1차원으로 펴주는 역할을 한다. 이에 하나의 반목문 만으로 모든 ax에 대해 접근할 수 있다.

    ```py
    # squeeze의 사용
    fig, axes = plt.subplots(n, m, squeeze=False, figsize=(m*2, n*2))
    idx = 0
    for i in range(n):
        for j in range(m):
            axes[i][j].set_title(idx)
            axes[i][j].set_xticks([])
            axes[i][j].set_yticks([])
            idx+=1

    # flatten의 사용
    fig, axes = plt.subplots(n, m, figsize=(m*2, n*2))
    for i, ax in enumerate(axes.flatten()):
        ax.set_title(i)
        ax.set_xticks([])
        ax.set_yticks([])
    ```

- `aspect`: 그래프를 그려졌을 때 보여지는 비율을 의미한다. 비율을 지정할 때 입력되는 값은 `x축의 비율` / `y축의 비율` 로 계산되어서 들어간다.

    ```py
    fig = plt.figure(figsize=(12, 5))
    ax1 = fig.add_subplot(121, aspect=1)
    ax2 = fig.add_subplot(122, aspect=0.5)
    plt.show()
    ```
    ![image](https://user-images.githubusercontent.com/91870042/152555690-2ba1a46e-0d92-4155-bc9c-0e0d16e1996e.png){: .align-center width="70%"}

## gridspec
- `add_gridspec`을 사용한 gridspec의 생성 **[추천]**

    ```py
    fig = plt.figure(figsize=(8, 5))
    gs = fig.add_gridspec(3, 3) # make 3 by 3 grid (row, col)

    ax = [None for _ in range(5)]

    ax[0] = fig.add_subplot(gs[0, :]) 
    ax[0].set_title('gs[0, :]')

    ax[1] = fig.add_subplot(gs[1, :-1])
    ax[1].set_title('gs[1, :-1]')

    ax[2] = fig.add_subplot(gs[1:, -1])
    ax[2].set_title('gs[1:, -1]')

    ax[3] = fig.add_subplot(gs[-1, 0])
    ax[3].set_title('gs[-1, 0]')

    ax[4] = fig.add_subplot(gs[-1, -2])
    ax[4].set_title('gs[-1, -2]')

    for ix in range(5):
        ax[ix].set_xticks([])
        ax[ix].set_yticks([])

    plt.tight_layout()
    plt.show()
    ```
    ![image](https://user-images.githubusercontent.com/91870042/152556068-dbc6e6b3-1ccb-4f93-b737-cb371c0cce0c.png){: .align-center width="70%"}

- `subplot2grid`를 사용한 gridspec의 생성

    ```py

    fig = plt.figure(figsize=(8, 5)) # initialize figure

    ax = [None for _ in range(6)] # list to save many ax for setting parameter in each

    ax[0] = plt.subplot2grid((3,4), (0,0), colspan=4)
    ax[1] = plt.subplot2grid((3,4), (1,0), colspan=1)
    ax[2] = plt.subplot2grid((3,4), (1,1), colspan=1)
    ax[3] = plt.subplot2grid((3,4), (1,2), colspan=1)
    ax[4] = plt.subplot2grid((3,4), (1,3), colspan=1,rowspan=2)
    ax[5] = plt.subplot2grid((3,4), (2,0), colspan=3)


    for ix in range(6): 
        ax[ix].set_title('ax[{}]'.format(ix)) # make ax title for distinguish:)
        ax[ix].set_xticks([]) # to remove x ticks
        ax[ix].set_yticks([]) # to remove y ticks
        
    fig.tight_layout()
    plt.show()
    ```
    ![image](https://user-images.githubusercontent.com/91870042/152556239-623d38b3-03a6-4de1-873a-4b279f4ba0f9.png){: .align-center width="70%"}

- `add_axes`: 특정 subplot을 임의의 위치에 생성하는 방법. 위치를 조정하여 그래프를 그리는 것이 쉽지 않기 때문에 많이 사용되지는 않는다.

    ```py
    ax[0] = fig.add_axes([0.1,0.1,0.8,0.4]) # x, y, dx, dy
    ax[1] = fig.add_axes([0.15,0.6,0.25,0.6])
    ax[2] = fig.add_axes([0.5,0.6,0.4,0.3])
    ```

- `inset_axes`: 미니맵처럼 하나의 그래프내에 서브플롯을 그릴 때 사용한다.

    ```py
    fig, ax = plt.subplots()
    axin = ax.inset_axes([0.8, 0.8, 0.2, 0.2]) # 각 비율의 값으로 설정된다. (x, y, dx, dy)
    plt.show()
    ```
    ![image](https://user-images.githubusercontent.com/91870042/152556768-4008bdc4-cdd0-4dd7-bc14-4b6c8d9a7135.png){: width="49%"} ![image](https://user-images.githubusercontent.com/91870042/152556925-a45e510b-e0ea-4c29-ab1b-a6e4ae8f0b25.png){: width="49%"}

- `make_axes_locatable`: subplot을 colorbar의 형태처럼 만드는 형태. 원래 있던 plot에 대해서 비율을 나누어서 구성하기 때문에 추가하고자 하는 방향의 **비율이 줄어드는 경향**이 있다.

    ```py
    from mpl_toolkits.axes_grid1.axes_divider import make_axes_locatable

    fig, ax = plt.subplots(1, 1)
    ax_divider = make_axes_locatable(ax)

    ax = ax_divider.append_axes("right", size="7%", pad="2%")

    # im = ax.imshow(np.arange(100).reshape((10, 10)))
    # fig.colorbar(im, cax=ax)
    plt.show()
    ```
    ![image](https://user-images.githubusercontent.com/91870042/152557308-07641699-af80-460f-b633-f5f185fa00f9.png){: width="49%"} ![image](https://user-images.githubusercontent.com/91870042/152557389-2f4a85ac-f6a0-489f-a381-070bb7d72b34.png){: width="49%"}
