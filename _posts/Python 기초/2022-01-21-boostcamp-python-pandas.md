---
title:  "[Python Basics for AI] Python Pandas"
excerpt: "Pandas: 파이썬 데이터 처리의 표준 라이브러리"

categories:
  - python
tags:
  - [AI, Naver, BoostCamp, Python, Pandas]
toc: true
toc_sticky: true
 
date: 2022-01-21 01:00:00
last_modified_at: 2022-01-21 01:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

![image](https://user-images.githubusercontent.com/91870042/144968064-c52e30d3-dd43-4fe4-9b23-f393ca5aca1c.png){: .align-center}

# Pandas
구조화된 데이터의 처리를 지원하는 Python라이브러리로서, `Panel data`의 줄임말로 `Pandas`가 되었다. 고성능 array계산 라이브러리인 numpy와 통합하여, 강력한 `스프레드시트`처리 기능을 제공한다. Pandas에서는 인덱싱과 연산용함수, 전처리 함수등을 numpy와 비슷하게 제공하고 있으며 주로, **데이터 처리 및 통계 분석**을 위해서 사용된다.

## Tabular형태의 데이터 명칭
![image](https://user-images.githubusercontent.com/91870042/144960142-1d7b4830-c691-43d4-8213-8b0ce99302ee.png){: .align-center}

- `Data table`: 데이터가 들어있는 테이블. 엑셀의 Sheet와 비슷하다.
- `Attribute`, `column`: 데이터의 속성을 의미한다.
- `instance`, `tuple`, `row`: 한 데이터의 가로 열을 의미한다.
- `data`, `값`, `value`: 행과 열이 만나는 하나의 셀을 의미한다.

## Pandas 설치
```
conda create -n ml python
activate ml
conda install pandas

jupyter notebook # 주피터 실행하기
```

## 데이터 로딩
```py
import pandas as pd

data_url = "https://archive.ics.uci.edu/ml/machine-learning-databases/housing/housing.data"
 # CSV타입의 데이터를 로드하고, separater는 빈공간으로 지정하며 Column은 지정하지 않는다.)
df_data = pd.read_csv(data_url, sep="\s+", header=None) # sep: 띄어쓰기가 하나 이상 나올 수 있다.
# Column 명 직접 지정하기
df_data.columns = ['CRIM', 'ZN', 'INDUS', 'CHAS', 'NOX', 'RM', 'AGE', 'DIS', 'RAD', 'TAX', 'PTRATIO', 'B', 'LSTAT', 'MEDV']
df_data.head() #처음 5줄 출력
```
<br>

# Series
- `Series`: 데이터프레임 중 하나의 column에 해당하는 데이터의 모음 Object
- `DataFrame`: DataTable 전체를 포함하는 Object

![image](https://user-images.githubusercontent.com/91870042/144961072-9678de0f-3bcc-4257-931f-d1bd6256e09b.png){: .align-center}

## 사용법
```py
Series(data=None, index=None, dype=None, name=None, copy=False, fastpath=False)
```
```py
list_data = [1, 2, 3, 4, 5]
example_obj = Series(data = list_data)
print(example_obj)
```
```
0   1
1   2
2   3
3   4
4   5
dtype: int64
```
Series는 `index`와 `values`로 이루어져 있음을 알 수 있다. 또한 index의 이름을 직접 지정하는 것도 가능하다.

```py
list_data = [1, 2, 3, 4, 5]
list_name = ["a", "b", "c", "d", "e"]
example_obj = Series(data = list_data, index=list_name)
print(example_obj)
```

기본적으로 Series와 Dataframe은 **index를 기준으로 데이터가 생성**이 되기 때문에, data의 크기보다 index의 크기가 더 큰 경우에 나머지 값들은 `NaN`값으로 지정이 된다.
```py
list_data = [1, 2, 3, 4, 5]
list_name = ["a", "b", "c", "d", "e", "f", "g"]
example_obj = Series(data = list_data, index=list_name)
print(example_obj)
```
```
a   1.0
b   2.0
c   3.0
d   4.0
e   5.0
f   NaN
g   NaN
dtype: float64
```
<br>

Dict type으로 Series생성
```py
dict_data = {"a":1, "b":2, "c":3, "d":4, "e":5}
example_obj = Series(dict_data, dtype=np.float32, name="example_data")
print(example_obj)
```
```
a   1.0
b   2.0
c   3.0
d   4.0
e   5.0
Name: example_data, dtype: float32
```
<br>

Data index로 값에 접근하는 방법
```py
example_obj["a"]
example_obj["a"] = 3.2
```

## 다양한 함수
- `astype`: 데이터 타입을 변경한다
- `values`: Series의 Value값만 출력해서 보여준다
- `index`: Series의 Index값만 출력해서 보여준다.
- `name`: Data에 대한 이름을 직접 지정할 수 있다.

<br>

# DataFrame
데이터 프레임에서 값을 알기 위해서는 데이터 프레임의 `index`값과 `column`값을 알아야 한다. 또한 데이터 프레임은 각각의 데이터 타입이 다를 수 있다. Series를 모아서 만든 Data Table이기 때문에 기본적으로 2차원으로 구성되어 있다.

![image](https://user-images.githubusercontent.com/91870042/144962636-8e13c1f1-e4fb-4ae0-b4e7-fb98b95f2a7d.png){: .align-center}

## column을 선택하는 방법
- `.`을 사용하는 방법
```py
df.first_name
```

- `index`로 접근하는 방법
```py
df["first_name"]
```

## indexing
- `loc`: index location, 인덱스의 **이름**
- `iloc`: index position, 인덱스의 **번호**

```py
s = pd.Series(np.nan, index=[49, 48, 47, 46, 45, 1, 2, 3, 4, 5])
s.loc[:3] # index값이 3이 나올 때 까지 출력
s.iloc[:3] # 3번째 index까지 출력
```
```
49  NaN
48  NaN
47  NaN
46  NaN
45  NaN
1   NaN
2   NaN
3   NaN
dtype: float64

49  NaN
48  NaN
47  NaN
dtype: float64
```

## handling
새로운 Column에 값을 Boolean 비교로 할당하는 방법
```py
df.debt = df.age > 40
```

Column삭제
```py
del df["debt"]  # 메모리 상에서 삭제 - df에 변화가 생김
df.drop("debt", axis=1) # 메모리 상에서 삭제X - df에 변화가 없음
```

- `T`: Transpose(전치)
- `values`: 값 출력
- `to_csv()`: csv형식으로 데이터프레임을 변환한다.

<br>

# selection & drop

## Selection with column names
- 한개의 column을 선택하는 경우
```py
df["account"].head()
```

- 2개 이상의 column을 선택하는 경우
```py
df[["account", "street", "state"]].head()
```

## Selcetion with index number
```py
df[:3] # Column이름 없이 사용하는 경우, row를 기준으로 표시해준다.
```

||account|name|street|city|state|postal-code|Jan|Feb|Mar|
|---|---|---|---|---|---|---|---|---|---|
|0|211829|Kerluke, Koepp and Kilpert|34456 Sean Highway|New Jaycob|Texas|28752|1000|62000|35000|
|1|320563|Walter-Trantow|1311 Alvis Tunnel|Port Khadijah|Nort Carolina|38365|95000|45000|35000|
|2|648336|Bashirian, Kunde and Price|62184 Schanberger Underpass Apt. 231|New Lilianland|lowa|76517|91000|120000|35000|

```py
account_series = df["account"]
account_series["account"][:3] # 칼럼명과 함께 row index를 사용하면, 해당 칼럼만 보여준다.
account_series[[0, 1, 2]]
account_series[account_series < 5000] # Boolean index
```
```
0   211829
1   320563
2   648336
Name: account, dtype: int64

0   211829
1   320563
2   648336
Name: account, dtype: int64

0   211829
3   109996 
4   121213
5   132971
...
11  231907
12  242368
Name: account, dtype: int64
```

## index 변경
```py
df.index = df["account"]
del df["account"]
df.head()
```

||name|street|city|state|postal-code|Jan|Feb|Mar|
|---|---|---|---|---|---|---|---|---|
|account|||||||||
|211829|Kerluke, Koepp and Kilpert|34456 Sean Highway|New Jaycob|Texas|28752|1000|62000|35000|
|320563|Walter-Trantow|1311 Alvis Tunnel|Port Khadijah|Nort Carolina|38365|95000|45000|35000|
|648336|Bashirian, Kunde and Price|62184 Schanberger Underpass Apt. 231|New Lilianland|lowa|76517|91000|120000|35000|


## basic, loc, iloc selection
![image](https://user-images.githubusercontent.com/91870042/144964814-384eca57-6b67-41a0-9634-850a357c1535.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/144964837-c2a1942a-b10d-4e01-9e3c-7c5fa74f2170.png){: .align-center}

## index 재설정
```py
df.index = list(range(0, 15)) #방법1
df.reset_index(drop=True) #방법2
df.reset_index(inplace=True, drop=True)
```

## data drop
index의 번호로 하나의 row를 지울 수 있다. 실제 데이터 프레임에 적용이 되지는 않고, 데이터프레임에 적용시키기 위해서는 `inplace=True`옵션을 지정해야한다.
```py
df.drop(1)
df.drop(1, inplace=True)
```
축을 기준으로 하여 drop할 수 있다. `axis=1`로 설정하게 되면, 세로줄이 삭제가 된다.
```py
df.drop("city", axis=1, inplace=True)
```

<br>

# dataframe operations

## Series operation

|s1생성|s2생성|
|:---:|:---:|
|![image](https://user-images.githubusercontent.com/91870042/144965484-4f78fd15-ec22-41f9-8331-5028bea5bca8.png){: .align-center}|![image](https://user-images.githubusercontent.com/91870042/144965513-d1f39210-cae0-4aa0-a936-3f893324fbea.png){: .align-center}|

![image](https://user-images.githubusercontent.com/91870042/144965543-8a42eab5-2ff1-4584-8dbd-e872820beddf.png){: .align-center}

## dataframe operation

|df1생성|df2생성|
|:---:|:---:|
|![image](https://user-images.githubusercontent.com/91870042/144965704-b8b4b95b-facc-4807-8c79-86220dd1eb74.png){: .align-center}|![image](https://user-images.githubusercontent.com/91870042/144965714-55258a40-1948-436c-b0e5-1d2ff4417d8d.png){: .align-center}|

![image](https://user-images.githubusercontent.com/91870042/144965740-995e14d6-8f93-4c09-af66-bc3915b9fb48.png){: .align-center}

## series + dataframe
```py
df = DataFrame(np.arange(16).reshape(4, 4), columns=list("abcd"))
s2 = Series(np.arange(10, 14))
print(df + s2)
print(df.add(s2, axis=0))
```

||a|b|c|d|0|1|2|3|
|---|---|---|---|---|---|---|---|---|
|0|NaN|NaN|NaN|NaN|NaN|NaN|NaN|NaN|
|1|NaN|NaN|NaN|NaN|NaN|NaN|NaN|NaN|
|2|NaN|NaN|NaN|NaN|NaN|NaN|NaN|NaN|
|3|NaN|NaN|NaN|NaN|NaN|NaN|NaN|NaN|

||a|b|c|d|
|---|---|---|---|---|
|0|10|11|12|13|
|1|15|16|17|18|
|2|20|21|22|23|
|3|25|26|27|28|

<br>

# lambda, map, apply
## map
pandas의 series타입의 데이터에도 map함수를 사용할 수 있다. map을 활용하여 `dict`, `sequence`형 자료들을 데이터프레임으로 바로 변형할 수 있다.

### Dict를 이용한 mapping
```py
z = {1: 'A', 2: 'B', 3: 'C'}
s1.map(z).head(5)
```
```
0   NaN
1   A
2   B
3   C
4   NaN
dtype: object
```
```py
df["sex_code"] = df.sex.map({"male":0, "female":1})
```

### replace function
```py
df.sex.replace({"male":0, "female":1})
df.sex.replace(["male", "female"], [0, 1], inplace=True)
```

## Lambda
```py
s1 = Series(np.arnage(10))
s1.map(lambda x: x**2).head()
```

## Apply
map과 달리, Series전체에 해당 함수를 적용할 수 있다. 입력값을 Series데이터로 입력받아서 조작할 수 있다.
```py
df_info = df[["earn", "height", "age"]]

f = lambda x : x.max() - x.min()
df_info.apply(f)

df_info.sum()
df_info.apply(sum)
```

||earn|height|age|
|---|---|---|---|
|0|79571.299011|73.89|49|
|1|96396.988643|66.23|62|
|2|48710.666947|63.77|33|
|3|80478.096153|63.22|95|
|4|82089.345498|63.08|43|

```
earn    318047.708444
height  19.870000
age     73.000000
dtype: float64

earn    4.474344e+07
height  9.183125e+04
age     6.250800e+04
dtype: float64
```

### applymap
Series단위가 아니라 element단위로 함수를 적용할 때 `applymap`을 사용한다. 이것은 Series단위에 `Apply`를 적용한것과 동일한 결과를 보인다.
```py
f = lambda x : -x
df_info.applymap(f).head() # 모든 데이터프레임 값에 -가 붙어서 출력
```

<br>

# Pandas built-in Functions
## Describe
`describe`는 numeric type의 데이터 요약 정보를 보여준다. 그 이외의 object데이터 타입은 보여주지 않는다.

## Unique
`unique`는 series 데이터의 유일한 값을 반환한다.

## Sum
`sum`은 기본적인 column 또는 row값의 연산을 지원한다. 또한, 축을 지정해서 합을 계산하는 것도 가능하다.
- `axis = 0` : 열을 기준으로 병함하여 결과적으로 행방향으로 결과가 남는다.
- `axis = 1` : 행을 기준으로 병합하여 결과적으로 열방향으로 결과가 남는다.

## isnull
어떠한 특정한 값이 NaN(null)값의 index를 반환한다. 응용하여, 전체 데이터프레임의 빈 값을 찾고 싶다면 다음과 같이 찾을 수 있다.
```py
df.isnull().sum()
```

## sort_values
데이터 프레임의 값에서 `column`값을 기준으로 정렬해서 보여줄 수 있다. 
```py
df.sort_values(["age", "earn"], ascending = True).head()
```

## value_counts
데이터 프레임에서 해당 값이 몇번 등장하는지 구할 수 있다.
```py
df["sex"].value_counts(sort = True)
df["sex"].value_counts(sort = True) / len(df) # 등장 비율을 구한다.
```

## Correlation & Covariance
상관계수(`corr`)와 공분산(`cov`)의 값을 구할 수 있는 함수이다.
```py
df.corr() # 전체 각 항목간의 상관관계
df["age"].corr(df["earn"]) # 나이와 소득간의 상관관계
df.age.cov(df.earn) # 나이와 소득간의 공분산
df.corrwith(df.earn) # 전체항목과 소득간의 상관관계
```
<br>

# Groupby
SQL에서의 groupby명령어와 동일하다. groupby명령어가 실행되었을 때는 **(split → apply → combine)**의 과정을 거쳐서 연산을 진행한다.

![image](https://user-images.githubusercontent.com/91870042/144971886-6ba26863-e5fa-4c24-8635-7d133d5d5091.png){: .align-center}

```py
df.groupby("묶음의 기준이 되는 칼럼").['적용받는 칼럼'].적용받는 연산
df.groupby("Team").["points"].sum()

# 한 개 이상의 칼럼을 묶어서 사용하는 것도 가능하다
df.groupby(["Team", "Year"]).["points"].sum()
```
```
Team
Devils  1536
Kings   2285
Riders  3049
Royals  1505
kings   812
Name: Points, dtype: int64

Team    Year
Devils  2014    863
        2015    673
Kings   2014    741
        2016    756
        2017    788
Riders  2014    876
        2015    789
        2016    694
```

## Hierarchical Index
`groupby`명령으로 나온 결과물도 데이터 프레임이다. 위에서 본 예시와 같이 2개 이상의 칼럼을 기준으로 groupby를 하게 되면 인덱스가 2개 이상 생성이 된다. 해당 데이터프레임을 `unstack()`, `swaplevel()`의 연산을 통해서 형식을 바꿀 수 있다.

### unstack
`unstack()`은 group으로 묶인 데이터를 matrix형태로 전환해주는 역할을 한다. 위에서 `Team`과 `Year`를 기준으로 groupby를 수행한 결과에서 unstack을 하게 되면, 2개의 index가 행과 열이되어서 값이 보여지는 것을 확인할 수 있다.
> unstack()이 된 상태에서 stack()을 다시 해주면, 기존의 데이터프레임의 상태로 되돌아간다.

```py
h_index = df.groupby(["Team", "Year"]).["points"].sum()
h_index.unstack()
```

![image](https://user-images.githubusercontent.com/91870042/144972890-79d3d010-5176-43b2-bbda-044a2d238c26.png){: .align-center}

### swaplevel
`swaplevel()`은 인덱스의 레벨을 서로 바꾸어 주는 역할을 한다. 위의 예시에서 0번째, 1번째 레벨은 각각, "Team", "Year"이다. 이번에는 반대로, **Year를 기준으로 먼저 group으로 묶고, 그 안에서 다시 Team으로 묶은 결과**를 출력해 주기 위해서는 swaplevel()을 사용하면 된다.
```py
h_index.swaplevel()
h_index.swaplevel().sortlevel(0)
```

![image](https://user-images.githubusercontent.com/91870042/144973115-ff0440b1-159d-476e-9210-9a4d57eca4e6.png){: .align-center}


### operations
위에서 얻은 결과에서 다시, 다양한 연산을 수행할 수 있는데 그중에 합 연산은 다음과 같다.
- `sum`
```py
h_index.sum(level=0) # Team을 기준으로 점수를 합산
h_index.sum(level=1) # 연도를 기준으로 점수를 합산
```

## grouped
데이터프레임에서 그룹을 지정해 **split**된 상태를 추출해서 사용하는 것이다. 그 때의 결과는 `tuple`형태로 지정되어 있어서 그룹의 key값과 value값을 뽑아낼 수 있다.
```py
grouped = df.groupby("Team")
for name, group in grouped:
    print(name)
    print(group)
```
![image](https://user-images.githubusercontent.com/91870042/144973994-9422c5de-cd03-4a1d-9ce8-edde9e0fd70e.png){: .align-center}

위의 결과에서 특정 그룹의 결과만 보고 싶다면, `get_group`을 사용해서 확인할 수 있다.
```py
grouped.get_group("Devils")
```

이렇게 groupby로 추출된 자료를 이용해 3가지 유형의 `apply`를 진행할 수 있다.

### Aggregation
`aggregation`은 요약된 통계정보를 추출해주는 함수이다.
```py
import numpy as np

grouped.agg(sum)
grouped.agg(np.mean)

# 특정 칼럼에 여러개의 함수를 apply할 수 있다.
grouped['points'].agg([np.sum, np.mean, np.std])
```
![image](https://user-images.githubusercontent.com/91870042/144974475-d5ad74cf-6d03-494e-bd90-f4c35e7da617.png){: .align-center}

### Transformation
`transformation`은 추출된 자료의 정보를 lambda와 같은 함수를 통해 변환해주는 역할을 한다. aggregation과 달리 key값 별로 요약된 정보가 아니다. 따라서 특정 그룹을 선택해서 적용시키는 것은 불가능하다. 

```py
score = lambda x: (x)
grouped.transform(score)

score = lambda x: (x.max())
grouped.transform(score)

score = lambda x: (x - x.mean()) / x.std()
grouped.transform(score)
```
![image](https://user-images.githubusercontent.com/91870042/144976064-3c725651-f2fa-4522-b904-d1b0b3e85cc8.png){: .align-center}


### Filtration
`filtration`은 얻어낸 자료에서 특정 정보를 제거하여 보여주는 **필터링**의 역할을 수행한다. `filter`안에는 boolean조건이 존재해야하며, `len(x)`는 그룹으로 묶인 데이터프레임의 개수를 의미한다.

```py
df.groupby('Team').filter(lambda x: len(x) >= 3)
df.groupby('Team').filter(lambda x: x["Rank"].sum() > 2)
df.groupby('Team').filter(lambda x: x["Points"].sum() > 1000)
df.groupby('Team').filter(lambda x: x["Rank"].sum() > 1)
```

<br>

# Case Study
실제 데이터를 이용해서 데이터를 분석
[📄phone_data.csv](https://www.shanelynn.ie/wp-content/uploads/2015/06/phone_data.csv)

```py
import dateutil

df_phone = pd.read_csv("phone_data.csv")
df_phone['date'] = df_phone['date'].apply(dateutil.parser.parse, dayfirst = True)
df_phone.read()
```
![image](https://user-images.githubusercontent.com/91870042/144977727-0425fd03-cb57-47e2-9fdb-d47ef2cf69d1.png){: .align-center}

> 월별로 사용량 분석하기

```py
df_phone.groupby('month')['duration'].sum()
```
```
month
2014-11     26639.441
2014-12     14641.870
2015-01     18233.299
2015-02     15522.299
2015-03     22750.441
Name: duration, dtype: float64
```

> 월별로 통화량 분석하기

```py
df_phone[df_phone['item'] == 'call'].groupby('network')['duration'].sum()
```

> 월별로 통화, 데이터, 문자 사용한 일 수 분석하기

```py
df_phone.groupby(['month', 'item'])['date'].count()
df_phone.groupby(['month', 'item'])['date'].count().unstack()
```

<br>

# Pivot table & Crosstab
## Pivot Table
엑셀에서 많이 사용하던 `피봇`기능과 동일한 기능을 수행한다. Index축은 groupby와 동일하지만, 칼럼값에 추가로 라벨링하여 각 데이터별로 aggfunc의 결과 값을 보여준다.

```py
df_phone.pivot_table(["duration"], 
                index=[df.phone.month, df_phone.item], 
                columns=df.phone.network, 
                aggfunc="sum", 
                fill_value=0)
```

## Corsstab
2개의 칼럼간의 교차 빈도, 비율, 덧셈을 구할때 사용하는 것이 `crosstab`이다. crosstab은 pivot table의 특수한 형태로서, **User-Item Rating Matrix**를 만들 때 사용가능하다.

```py
df_movie = pd.read_csv("./movie_rating.csv")
df_movie.head()
```

||critic|title|rating|
|---|---|---|---|
|0|Jack Matthews|Lady in the Water|3.0|
|1|Jack Matthews|Snakes on a Plane|4.0|
|2|Jack Matthews|You Me and Dupree|3.5|
|3|Jack Matthews|Superman Returns|5.0|
|4|Jack Matthews|The Night Listener|3.0|

```py
import pandas as pd

pd.crosstab(index = df_movie.critic, columns = df_movie.title,
            values = df_movie.rating, aggfunc="first").fillna(0)
```

![image](https://user-images.githubusercontent.com/91870042/144979707-494c8b77-2454-4f06-8883-06b3af9c3e67.png){: .align-center}

<br>

# Merge & Concat
## Merge
`Merge`는 2개의 데이터를 하나로 합칠 때 사용되며, SQL에서의 Merge와 동일한 기능을 수행한다.
```py
import pandas as pd

pd.merge(df_a, df_b, on = 'subject_id')
```
동일한 칼럼이지만, 두 데이터 프레임의 칼럼명이 다를 경우 왼쪽의 칼럼명과 오른쪽 칼럼명을 지정하여 merge를 수행한다.
```py
import pandas as pd

pd.merge(df_a, df_b, left_on='subject_id', rihgt_on='subject_id2')
```

### Join Method
`join`을 하는 방법에는 4가지가 존재한다.
- `inner join`: 두 데이터 프레임의 교집합을 말한다.
- `full join`: 두 데이터 프레임의 합집합을 말한다.
- `left join`: 두 데이터 프레임 중 왼쪽 데이터 프레임을 기준으로 join한다. 그 이외의 값은 NaN값으로 처리한다.
- `right join`: 두 데이터 프레임 중 오른쪽 데이터 프레임을 기준으로 join한다. 그 이외의 값은 NaN값으로 처리한다.

![image](https://user-images.githubusercontent.com/91870042/144987907-207373d1-2b9f-4982-aa88-b62ed6e0fd13.png){: width="50%" height="50%" .align-center}

```py
import pandas as pd

pd.merge(df_a, df_b, on='subject_id', how='left')
```
위에서 입력가능한 how의 값에는 `outer`, `inner`, `left`, `right`가 있으며, `inner`가 기본값으로 설정되어 있다.

### index based join
두 데이터 프레임을 merge하면서 이름이 같은 칼럼이 있을 경우 어느쪽 데이터프레임을 기준으로 잡을 것인지를 작성하는데, 그때 설정하는 옵션이 `right_index`, `left_index`이다.

## Concat
`concat`은 같은 형태를 가진 2개의 데이터프레임을 서로 붙이는 연산작업이다.
```py
df_new = pd.concat([df_a, df_b])
df_new.reset_index()

df_a.append(df_b)

# 축을 기준으로 옆으로 concat도 가능하다.
df_new = pd.concat([df_a, df_b], axis = 1)
```
![image](https://user-images.githubusercontent.com/91870042/144981586-ef9af511-2bd1-4d46-9b77-210fe304cd9d.png){: .align-center}

<br>

# Persistence
## Database Connection
데이터베이스에 연결을 해서 원하는 정보를 추출하여 데이터프레임으로 불러올 수 있다. 데이터베이스에 연결을 할때는 보통 `sqlite3`를 사용을 하며 import는 다음과 같이 한다.
```py
import sqlite3
```

## XLS persistence
데이터프레임의 엑셀 추출코드이다. 일반적으로 `openpyxls`또는 `XlsxWrite`를 사용한다.

### 엑셀로부터 데이터프레임 불러오기
```py
writer = pd.ExcelWriter('./data/df_routes.xlsx', engine='xlsxwriter')
df_routes.to_excel(writer, sheet_name='Sheet1')
```

## Pickle
`pickle`은 가장 일반적인 python파일 persistence이다.
- `to_pickle`: 데이터프레임을 pickle로 저장
- `read_pickle`: pickle파일로 부터, 데이터프레임을 읽어오기

```py
df_routes.to_pickle("./data/df_routes.pickle")

df_routes.read_pickle("./data/df_routes.pickle")
df_routes.describe()
```