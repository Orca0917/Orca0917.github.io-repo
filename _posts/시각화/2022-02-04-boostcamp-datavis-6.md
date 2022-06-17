---
title:  "[Data Visualization] Text"
excerpt: "시각화 자료에 대해 추가적인 설명정보를 추가하는 Text"

categories:
  - datavis
tags:
  - [AI, Naver, BoostCamp, Python, Matplotlib]
toc: true
toc_sticky: true
 
date: 2022-02-04 00:00:00
last_modified_at: 2022-02-04 00:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Matplotlib에서 Text
## Text in Viz
시각화에서 굳이 텍스트가 필요한 이유는 다음 2가지가 있다.
- Visual representation들이 줄 수 없는 많은 설명을 추가한다.
- 잘못된 전달에서 생기는 오해를 방지한다.

하지만, 그렇다고 Text를 너무 과하게 사용한다면 시각화가 가지고 있는 간결함을 해쳐서 독자가 시각화 자료를 이해하는 과정을 방해할 수도 있다.

## Anatomy of a Figure
![image](https://user-images.githubusercontent.com/91870042/152456223-e41f534c-bac8-4388-9800-dded9ba8261b.png){: .align-center}

- `Title`: 가장 큰 주제를 설명
- `Label`: 축에 해당하는 데이터 정보를 제공
- `Tick Label`: 축의 눈금에 적힌 라벨. 축에 눈금을 사용하여 스케일 정보를 추가
  - Major, Minor Tick Label이 있어서 축의 눈금도 구분되게 만들 수 있다.
- `Legend`: 범례. 한 그래프에서 2개 이상의 서로 다른 데이터를 분류하기 위해서 사용하는 보조정보
- `Annotation(Text)`: 그 외의 시각화에 대한 설명을 추가 (Annotation은 화살표 사용 가능)

## Text API

|pyplot API|Object-Oriented API|설명|
|:-----:|:-----:|:-----:|
|`suptitle`|`suptitle`|figure의 제목을 설정|
|`title`|`set_title`|`ax`의 제목을 설정|
|`xlabel`|`set_xlabel`|x축의 라벨을 설정|
|`ylabel`|`set_ylabel`|y축의 라벨을 설정|
|`figtext`|`text`|fig의 텍스트 삽입|
|`text`|`text`|Axes의 텍스트 삽입|
|`annotate`|`annotate`|화살표를 포함한 `ax`의 주석 삽입|

- `ax.text()`: 좌표를 기반으로 텍스트를 작성
- `fig.text()`: 하나의 fig내의 비율을 기반으로 텍스트 작성

```py
fig, ax = plt.subplots()
fig.suptitle('Figure Title')
ax.plot([1, 3, 2], label = 'legend')
ax.legend()
ax.set_title('Ax Title')
ax.set_xlabel('X Label')
ax.set_ylabel('Y Label')

ax.text(x=1, y=2, s='Text')
fig.text(0.5, 0.6, s='Figure Text')

plt.show()
```
![image](https://user-images.githubusercontent.com/91870042/152532548-1d6751b7-b839-4785-9cad-c811ac4b3e97.png){: .align-center}

<br>

# Text Properties
## Font Components
- `family` / `fontfamily` : 폰트 글꼴 설정
- `size` / `fontsize` : 폰트의 크기 설정
- `style` /`fontstyle` : 폰트의 기울임 설정
- `weight` / `fontweight` : 폰트의 강조 설정. 글씨체에 따라서 일부가 같게 나오기도 한다.

![image](https://user-images.githubusercontent.com/91870042/152532941-ff631a37-abd9-4123-ab14-69a93a6bdacd.png){: .align-center width="70%"}

```py
fig, ax = plt.subplots()
ax.set_xlim(0, 1)
ax.set_ylim(0, 1)

ax.text(x=0.5, y=0.5, s='Text\nis Important',
        fontsize=20,
        fontstyle='italic',
        fontweight='bold',
        fontfamily='serif',
       )
plt.show()
```
![image](https://user-images.githubusercontent.com/91870042/152533563-ba68d1f3-0eb1-4695-8830-7adaee747144.png){: .align-center width="70%"}

## Easy to use Props
- `color` : 글씨의 색상
- `linespacing` : 줄과 줄 사이의 간격
- `backgroundcolor` : 배경색
- `alpha` : 투명도
- `zorder` : Z축의 순서
- `visible` : 텍스트를 보이게 할 것인지 설정

```py
fig, ax = plt.subplots()
ax.set_xlim(0, 1)
ax.set_ylim(0, 1)

ax.text(x=0.5, y=0.5, s='Text\nis Important',
        fontsize=20,
        fontweight='bold',
        fontfamily='serif',
        color='royalblue',
        linespacing=2,
        backgroundcolor='lightgray',
        alpha=0.5
       )
plt.show()
```
![image](https://user-images.githubusercontent.com/91870042/152533821-ccbdc9e7-7f66-4188-99ef-7153cd1c312e.png){: .align-center width="70%"}

## Alignment
텍스트를 (x, y)에 작성을 하면 텍스트 박스의 좌측하단부를 좌표에 맞춰서 작성이된다. 그런 부분을 중앙에 오도록 하거나 아니면 마음대로 기준이 되는 위치를 설정할 수 있다.

- `ha` : horizontal alignment
- `va` : vertical alignment
- `rotation`
- `multialignment`

```py
fig, ax = plt.subplots()
ax.set_xlim(0, 1)
ax.set_ylim(0, 1)

ax.text(x=0.5, y=0.5, s='Text\nis Important',
        fontsize=20,
        fontweight='bold',
        fontfamily='serif',
        color='royalblue',
        linespacing=2,
        va='center', # top, bottom, center
        ha='center', # left, right, center
        rotation='horizontal' # 세로:vertical, 각도:45, ...
       )
plt.show()
```
![image](https://user-images.githubusercontent.com/91870042/152534140-9f0b018c-a353-4f50-a0fc-bb5d66514c8b.png){: .align-center width="70%"}

## Bbox
`bbox` : 텍스트에 대해서 테두리를 감싸주는 것을 말한다. 단순히 네모난 사각형 뿐만 아니라 화살표, 원, 둥근모서리 사각형 등 다양한 모양을 지원한다.

![image](https://user-images.githubusercontent.com/91870042/152534386-5533d432-5a0b-41d3-9480-3099ee1aa932.png){: .align-center width="70%"}

```py
fig, ax = plt.subplots()
ax.set_xlim(0, 1)
ax.set_ylim(0, 1)

ax.text(x=0.5, y=0.5, s='Text\nis Important',
        fontsize=20,
        fontweight='bold',
        fontfamily='serif',
        color='black',
        linespacing=2,
        va='center', # top, bottom, center
        ha='center', # left, right, center
        rotation='horizontal', # vertical?
        bbox=dict(boxstyle='round', facecolor='wheat', alpha=0.4)
        # ec='blue' : 테두리 색상 설정 (edge color)
       )
plt.show()
```
![image](https://user-images.githubusercontent.com/91870042/152534477-df63a6b5-506f-44ce-bc23-271541484d30.png){: .align-center width="70%"}

<br>

# 실습

데이터셋 : `StudentsPerformance.csv`

```py
student = pd.read_csv('./StudentsPerformance.csv')
student.head()
```
![image](https://user-images.githubusercontent.com/91870042/152534866-bdda5d8c-8155-4bbb-ae8e-8005c7cab42f.png){: .align-center}

## 성별과 성적
위의 데이터에서 성별에 따라서 수학성적과 읽기성적이 어떻게 분포되는지 시각적으로 확인해보자.

```py
# 수학성적과 읽기성적에 대한 관계 / 성별에 따른 색 지정

fig = plt.figure(figsize=(9, 9))
ax = fig.add_subplot(111, aspect=1)

for g, c in zip(['male', 'female'], ['royalblue', 'tomato']):
    student_sub = student[student['gender']==g]
    ax.scatter(x=student_sub ['math score'], y=student_sub ['reading score'],
               c=c,
               alpha=0.5, 
               label=g)
    
ax.set_xlim(-3, 102)
ax.set_ylim(-3, 102)

ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)

ax.set_xlabel('Math Score', 
              fontweight='semibold')
ax.set_ylabel('Reading Score', 
              fontweight='semibold')

ax.set_title('Score Relation', 
             loc='left', va='bottom', # 제목의 위치를 왼쪽으로 변경
             fontweight='bold', fontsize=15
            )

ax.legend(
    title='Gender',
    shadow=True, # 범례의 그림자를 설정
    labelspacing=1.2, # 범례내부의 텍스트 간의 간격
#     loc='lower right' # 범례의 위치를 지정
#     bbox_to_anchor=[1.2, 0.5] # subplot 외부에 범례를 지정 (비율로 지정)
#     ncol=2 # 범례를 한줄에 2개씩 보여주도록 설정
)

plt.show()
```
![image](https://user-images.githubusercontent.com/91870042/152535236-aeb3a471-ebd3-4b9b-8570-8ada039d85bb.png){: .align-center}

## 수학성적의 분포
10점씩 잘라서 구분한 수학성적에서 학생들의 점수가 어떻게 분포되어 있는지 효과적인 시각자료로 표현해보자.

```py
# DataFrame에서 사용할 데이터 가공하기
def score_band(x):
    tmp = (x+9)//10
    if tmp <= 1: 
        return '0 - 10'
    return f'{tmp*10-9} - {tmp*10}'

student['math-range'] = student['math score'].apply(score_band)
student['math-range'].value_counts().sort_index()
```
```py
# 학생들의 성적을 가져오되, 점수순서로 정렬하고 
# 각 점수대 별로 몇명의 학생이 있는지 확인한다.
math_grade = student['math-range'].value_counts().sort_index()

fig, ax = plt.subplots(1, 1, figsize=(11, 7))
ax.bar(math_grade.index, math_grade,
       width=0.65, 
       color='royalblue',
       linewidth=1,
       edgecolor='black'
      )

ax.margins(0.01, 0.1)
ax.set(frame_on=False) # 사각형의 각 변을 모두 제거
ax.set_yticks([]) # y축의 눈금을 제거
ax.set_xticks(np.arange(len(math_grade))) # math_grade의 개수만큼 xtick 지정
ax.set_xticklabels(math_grade.index) # 위에서 지정한 xtick을 레이블로 교체

ax.set_title('Math Score Distribution', fontsize=14, fontweight='semibold')

for idx, val in math_grade.iteritems():
    # 각 데이터 분포의 최상단 + 3 정도에 표시
    # 패딩을 통해서 표현하는 방법도 있음
    ax.text(x=idx, y=val+3, s=val, 
            va='bottom', ha='center', # textbox의 중앙하단이 기준이 되도록 설정
            fontsize=11, fontweight='semibold'
           )

plt.show()
```
![image](https://user-images.githubusercontent.com/91870042/152536071-948d8f53-9b4f-42c4-b6e7-b39e2fc3d30f.png){: .align-center width="70%"}

## 특정 학생 표시
위에서 배웠던 `annotate`를 사용해 화살표를 포함한 텍스트 정보를 생성해보자

```py
fig = plt.figure(figsize=(9, 9))
ax = fig.add_subplot(111, aspect=1)
i = 13

# 모든 학생의 성적을 scatter로 표시 (회색)
ax.scatter(x=student['math score'], y=student['reading score'],
           c='lightgray',
           alpha=0.9, zorder=5)
    
# 원하는 학생의 성적을 scatter로 표시 (주황색)
ax.scatter(x=student['math score'][i], y=student['reading score'][i],
           c='tomato',
           alpha=1, zorder=10)    
    
ax.set_xlim(-3, 102)
ax.set_ylim(-3, 102)

# 상단과 우측의 축제거
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)

ax.set_xlabel('Math Score')
ax.set_ylabel('Reading Score')

ax.set_title('Score Relation') 

# x축과 평행한 선
ax.plot([-3, student['math score'][i]], [student['reading score'][i]]*2,
        color='gray', linestyle='--',
        zorder=8)

# y축과 평행한 선
ax.plot([student['math score'][i]]*2, [-3, student['reading score'][i]],
       color='gray', linestyle='--',
       zorder=8)

bbox = dict(boxstyle="round", fc='wheat', pad=0.2)
arrowprops = dict( # 화살표 스타일 지정
    arrowstyle="->")

# 화살표를 포함한 텍스트 추가
ax.annotate(text=f'This is #{i} Studnet',
            xy=(student['math score'][i], student['reading score'][i]),
            xytext=[80, 40],
            bbox=bbox,
            arrowprops=arrowprops,
            zorder=9
           )

plt.show()
```
![image](https://user-images.githubusercontent.com/91870042/152536708-766793ef-d377-4ce8-b679-f0dcf07f2a1f.png){: .align-center width="70%"}