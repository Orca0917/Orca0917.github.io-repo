---
title:  "matplotlib: 데이터 시각화 (1)"
excerpt: "matplotlib의 기본사용법, label, legend, axis, linestyle, marker, linecolor, 영역fill, axis scale"

categories:
  - matplotlib
tags:
  - [Python, matplotlib]

toc: true
toc_sticky: true
 
date: 2022-02-01 00:00:00
last_modified_at: 2022-02-01 00:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **Wikidocs의 Matplotlib Tutorial - 파이썬으로 데이터 시각화하기**를 읽고 정리하는 글입니다.<br>
더 자세한 설명은 해당 [문서](https://wikidocs.net/book/5011)에 더 상세하게 설명되어 있습니다.
{: .notice--info}

# Basics
## 기본 그래프 그리기
- `plt.plot()`에 하나의 리스트만 전달되는 경우: 리스트의 값들의 y축의 값들이라고 가정하고, x축은 0, 1, 2, ... 을 크기에 맞춰서 할당한다.
  
  ```py
    import matplotlib.pyplot as plt

    plt.plot([1, 2, 3, 4])
    plt.show()
  ```
  ![temp](https://user-images.githubusercontent.com/91870042/151829247-c7b317c3-da8b-4501-a57d-4dab005a4b9f.png){: .align-center}


- `plt.plot()`에 2개의 리스트가 전달되는 경우: 앞에 전달된 리스트를 x축, 뒤에 전달된 리스트를 y축이라고 하고 그래프를 그린다.

    ```py
    import matplotlib.pyplot as plt

    plt.plot([1, 2, 3, 4], [1, 4, 9, 16])
    plt.show()
    ```
    ![temp](https://user-images.githubusercontent.com/91870042/151829607-11d23e57-d1ad-4e35-970c-ab78a22a4acb.png){: .align-center}

- 레이블이 지정된 데이터 사용하기

    ```py
    import matplotlib.pyplot as plt

    data_dict = {'data_x': [1, 2, 3, 4, 5], 'data_y': [2, 3, 5, 10, 8]}
    plt.plot('data_x', 'data_y', data=data_dict)
    plt.show()
    ```
    ![temp](https://user-images.githubusercontent.com/91870042/151830934-f4689d9e-e6dc-42a0-9810-dfa174effee1.png){: .align-center}


## 스타일 지정
- Format String: 아래에서 사용한 `ro`는 빨간색(r) 원형마커(o)를 말한다. 더 자세한 내용은 이후에 다시 다룬다!
- `axis`: x축의 범위와 y축의 범위를 지정한다. 마찬가지로 더 자세한 설명은 이후에 다룬다!
  
```py
import matplotlib.pyplot as plt

plt.plot([1, 2, 3, 4], [1, 4, 9, 16], 'ro')
plt.axis([0, 6, 0, 20])
plt.show()
```

![temp](https://user-images.githubusercontent.com/91870042/151830071-8865e092-80b4-4bc1-9e68-0c33b5bedf83.png){: .align-center}

## 여러 개의 그래프

```py
import matplotlib.pyplot as plt
import numpy as np

t = np.arange(0., 5., 0.2)

# 빨간 대쉬, 파란 사각형, 녹색 삼각형
# --: 점선, s: 사각형, ^: 삼각형
plt.plot(t, t, 'r--', t, t**2, 'bs', t, t**3, 'g^')
plt.show()
```

![temp](https://user-images.githubusercontent.com/91870042/151830529-9ff49f66-77d1-4803-a28b-2b939a09c04c.png){: .align-center}

<br>

# label
`matplotlib.pyplot`의 `xlabel()`과 `ylabel()`을 사용하여 각 축에 대한 레이블을 지정해서 보여줄 수 있다.

```py
import matplotlib.pyplot as plt

plt.plot([1, 2, 3, 4], [1, 4, 9, 16])
plt.xlabel('X-Label')
plt.ylabel('Y-Label')
plt.show()
```

![temp](https://user-images.githubusercontent.com/91870042/151831182-4807a610-eea5-4fc1-af7e-38b968ec2359.png){: .align-center}

- `labelpad`: 여백 지정하기
  
    ```py
    import matplotlib.pyplot as plt

    plt.plot([1, 2, 3, 4], [2, 3, 5, 10])
    plt.xlabel('X-Axis', labelpad=15)
    plt.ylabel('Y-Axis', labelpad=20)
    plt.show()
    ```
    ![temp](https://user-images.githubusercontent.com/91870042/151831517-a08ae542-6029-42fb-a80f-4a2511b47e3d.png){: .align-center}

- `loc`: 위치 지정하기

    ```py
    import matplotlib.pyplot as plt

    plt.plot([1, 2, 3, 4], [2, 3, 5, 10])
    plt.xlabel('X-Axis', loc='right')
    plt.ylabel('Y-Axis', loc='top')
    plt.show()
    ```

    ![temp](https://user-images.githubusercontent.com/91870042/151831683-eb05e631-a3cc-4357-aa1d-15ff0782ecc7.png){: .align-center}


<br>

# legend
`legend`는 범례를 보여줌으로써 해당 그래프가 어떤 정보를 담고 있는지 알려줄 수 있다. 위치를 직접설정하지 않으면 자동으로 가장 적절한 위치에 배정을 한다.

```py
import matplotlib.pyplot as plt

plt.plot([1, 2, 3, 4], [2, 3, 5, 10], label='Price ($)')
plt.xlabel('X-Axis')
plt.ylabel('Y-Axis')
plt.legend()

plt.show()
```

![temp](https://user-images.githubusercontent.com/91870042/151832087-96040e03-a19a-49a5-97c7-c92f32dbf5ec.png){: .align-center}

- `loc`: 위치 지정하기

    ```py
    import matplotlib.pyplot as plt

    plt.plot([1, 2, 3, 4], [2, 3, 5, 10], label='Price ($)')
    plt.xlabel('X-Axis')
    plt.ylabel('Y-Axis')
    # plt.legend(loc=(0.0, 0.0)) # 1번째 그래프
    # plt.legend(loc=(0.5, 0.5)) # 2번째 그래프
    plt.legend(loc=(1.0, 1.0)) # 3번째 그래프

    plt.show()
    ```

    ![temp](https://user-images.githubusercontent.com/91870042/151832499-7f062333-8e17-446b-a4b4-356fecddebde.png){: width="32%"} ![temp](https://user-images.githubusercontent.com/91870042/151832672-7f74406e-0400-4b4b-991e-b989417bff93.png){:  width="32%"} ![temp](https://user-images.githubusercontent.com/91870042/151833218-e645e296-d462-4af5-915d-2d4fde04bec9.png){: width="32%"}


- `ncol`: 열 개수 지정하기

    ```py
    import matplotlib.pyplot as plt

    plt.plot([1, 2, 3, 4], [2, 3, 5, 10], label='Price ($)')
    plt.plot([1, 2, 3, 4], [3, 5, 9, 7], label='Demand (#)')
    plt.xlabel('X-Axis')
    plt.ylabel('Y-Axis')
    # plt.legend(loc='best')          # ncol = 1
    plt.legend(loc='best', ncol=2)    # ncol = 2

    plt.show()
    ```

    ![temp](https://user-images.githubusercontent.com/91870042/151833845-ed603582-c284-4327-aca5-918e31748861.png){: width="49%"} ![temp](https://user-images.githubusercontent.com/91870042/151833740-f91fc031-d5f2-441d-aff5-0a7f6e5ff7d6.png){: width="49%"}

<br>

# axis
- `xlim()`: X축이 표시되는 범위를 지정 + 반환 
- `ylim()`: Y축이 표시되는 범위를 지정 + 반환 
- `axis()`: X, Y축이 표시되는 범위를 지정 + 반환 

```py
import matplotlib.pyplot as plt

plt.plot([1, 2, 3, 4], [2, 3, 5, 10])
plt.xlabel('X-Axis')
plt.ylabel('Y-Axis')

# plt.xlim([0, 5]) # 기본 이나 리스트, 튜플로 전달 가능
# plt.ylim([0, 15])
plt.axis([0, 5, 0, 15])  # X, Y축의 범위: [xmin, xmax, ymin, ymax]

plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/151834753-1818b14d-67a7-4657-8d89-1a5e716fb9ff.png){: .align-center}

<br>

# linestyle
그래프를 그릴 때 표현할 수 있는 선의 종류를 지정할 수 있다. 선의 종류를 지정하는 방법에는 다시 2가지가 존재한다.

1. 문자열 포맷: 이미 지정된 특정문자열을 사용해서 나타내기
    - `-`: 실선
    - `--`: - 로 이루어진 점선
    - `:`: . 으로 이루어진 점선
    - `-.`: - 과 . 이 번갈아가며 나오는 형태
2. `linestyle`을 이용해서 직접 설정
    - `solid`
    - `dashed`
    - `dotted`
    - `dashdot`

```py
import matplotlib.pyplot as plt

plt.plot([1, 2, 3], [4, 4, 4], '-', color='C0', label='Solid') # linestyle='solid'
plt.plot([1, 2, 3], [3, 3, 3], '--', color='C0', label='Dashed') # linestyle='dashed'
plt.plot([1, 2, 3], [2, 2, 2], ':', color='C0', label='Dotted') # linestyle='dotted'
plt.plot([1, 2, 3], [1, 1, 1], '-.', color='C0', label='Dash-dot') # linestyle='dashdot'
plt.xlabel('X-Axis')
plt.ylabel('Y-Axis')
plt.axis([0.8, 3.2, 0.5, 5.0])
plt.legend(loc='upper right', ncol=4)
plt.show()
```

![temp](https://user-images.githubusercontent.com/91870042/151835570-0036dd0e-deae-4717-b920-82218a9459e1.png){: .align-center}

<br>

# marker
그래프위에서 그려지는 원형점, 삼각형, 사각형등 표시되는 지점들을 도형으로 표현

```py
import matplotlib.pyplot as plt

plt.plot([1, 2, 3, 4], [2, 3, 5, 10], 'bo') # blue, o(원형점)
# plt.plot([1, 2, 3, 4], [2, 3, 5, 10], 'yo--') # marker와 함께 표현
plt.xlabel('X-Axis')
plt.ylabel('Y-Axis')
plt.show()
```

![temp](https://user-images.githubusercontent.com/91870042/151836139-12d6435a-255d-45ef-aba3-ee02facf0a99.png){: width="49%"} ![temp](https://user-images.githubusercontent.com/91870042/151836529-de3b5250-ce95-4b9b-8ca4-e650ed9d4669.png){: width="49%"}

<br>

# color
- `color`: 그래프의 색상을 지정한다. 이미 지정된 포맷문자열(r, g, b, y등)과 같이 나타낼 수 있고 이외에도 color 키워드(limegreen, violet, dodgerblue등) 이나 Hex Code(#e2e2e2)를 사용해서도 나타낼 수 있다.

```py
import matplotlib.pyplot as plt

plt.plot([1, 2, 3, 4], [2.0, 3.0, 5.0, 10.0], 'r')
plt.plot([1, 2, 3, 4], [2.0, 2.8, 4.3, 6.5], color='limegreen')
plt.plot([1, 2, 3, 4], [2.0, 2.5, 3.3, 4.5], color='#7CB7F9')
plt.xlabel('X-Axis')
plt.ylabel('Y-Axis')

plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/151837322-8137bed2-ed42-4fa1-b740-310d4ee2e290.png){: .align-center}

<br>

# fill: 그래프 영역 채우기
그래프 내에서 포함되는 특정 영역을 채워서 강조해준다.
- `fill_between()`: 두 수평방향의 곡선 사이를 채운다.
- `fill_betweenx()`: 두 수직방향의 곡선 사이를 채운다.

    ```py
    import matplotlib.pyplot as plt

    x = [1, 2, 3, 4]
    y = [2, 3, 5, 10]

    plt.plot(x, y)
    plt.xlabel('X-Axis')
    plt.ylabel('Y-Axis')

    plt.fill_between(x[1:3], y[1:3], alpha=0.5)
    plt.fill_betweenx(y[2:4], x[2:4], alpha=0.5)

    plt.show()
    ```

    ![temp](https://user-images.githubusercontent.com/91870042/151837679-9571f96e-4278-49bb-8a5c-008c00fd7379.png){: width="49%"} ![temp](https://user-images.githubusercontent.com/91870042/151837989-dec5c313-d183-49a0-acc0-40446deba6a2.png){: width="49%"}


- `fill()`: 직접 지정한 다각형의 영역을 채운다.

    ```py
    import matplotlib.pyplot as plt

    x = [1, 2, 3, 4]
    y1 = [2, 3, 5, 10]
    y2 = [1, 2, 4, 8]

    plt.plot(x, y1)
    plt.plot(x, y2)
    plt.xlabel('X-Axis')
    plt.ylabel('Y-Axis')

    plt.fill_between(x[1:3], y1[1:3], y2[1:3], color='lightgray', alpha=0.5)
    plt.fill([1.9, 1.9, 3.1, 3.1], [1.0, 4.0, 6.0, 3.0], color='lightgray', alpha=0.5)

    plt.show()
    ```
    ![temp](https://user-images.githubusercontent.com/91870042/151838210-6b82882f-415f-406b-8c98-c70fdd03a370.png){: width="49%"} ![temp](https://user-images.githubusercontent.com/91870042/151838418-38960dec-041c-44d0-8ffe-2365b10840c4.png){: width="49%"} 

<br>

# scale
`scale`을 지정하면 x축 또는 y축의 간격을 지정할 수 있다. 기본적으로 일정간격 떨어져 있는 `linscale`을 사용하지만 `log`, `symlog`, `logit`으로 변경할 수 있다.

```py
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(-10, 10, 100)
y = x ** 3

plt.plot(x, y)
# plt.xscale('linear')
plt.xscale('symlog')

# 좌: scale적용 이전, 우: scale 적용 이후
plt.show()
```

![temp](https://user-images.githubusercontent.com/91870042/151838877-8ab2f068-e48c-4d05-b2df-b5ddc63a42e8.png){: width="49%"} ![temp](https://user-images.githubusercontent.com/91870042/151838929-f57d2148-bfb2-4c33-8655-845e05dad42b.png){: width="49%"}


```py
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(0, 5, 100)
y = np.exp(x)

plt.plot(x, y)
# plt.yscale('linear')
plt.yscale('log')

plt.show()
```
![temp](https://user-images.githubusercontent.com/91870042/151839204-b19670c1-dba3-44b5-b729-792f8fadca5c.png){: width="49%"} ![temp](https://user-images.githubusercontent.com/91870042/151839258-8c178a6d-d1d3-47b3-99ba-4ffbb86319b5.png){: width="49%"}

<br>

# References

[📘 Matplotlib Tutorial - 파이썬으로 데이터 시각화하기 - Wiki docs](https://wikidocs.net/book/5011)

