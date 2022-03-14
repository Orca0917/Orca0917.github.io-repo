---
title:  "[Recommender System] TF-IDF 를 사용한 추천시스템"
excerpt: "Term Frequency - Inverse Document Frequency 에 대해서 살펴보고 추천시스템에서 컨텐츠 기반 추천에 대해서 알아보자"

categories:
  - RecSys
tags:
  - [AI, Naver, BoostCamp, Recommender System]
toc: true
toc_sticky: true
use_math: true

date: 2022-03-14 04:00:00
last_modified_at: 2022-03-14 04:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# TF-IDF를 활용한 컨텐츠 기반 추천

텍스트 데이터를 다루는 가장 기본적인 방법인 TF-IDF에 대해서 알아보고 이를 활용한 컨텐츠 기반 추천방법에 대해서 살펴보자.

## 개요

컨텐츠 기반 추천의 아이디어는 굉장히 단순하다. **유저 $u$가 선호한 아이템 $i$과 비슷한 아이템 $i'$을 $u$에게 추천**을 해주는 것이다. 예를 들어, 영화에서는 동일한 감독의 다른 영화를 추천해줄 수도 있고, 비슷한 장르의 영화를 추천해줄 수 있다.

![image](https://user-images.githubusercontent.com/91870042/158010405-f9d53f18-42bd-420b-9cb7-4d70e94f7a1b.png){: .align-center width="70%"}

이러한 컨텐츠 기반 추천의 장점과 단점은 무엇이 있을까.

**장점**

- 유저가 추천을 할 때, 다른 유저의 데이터가 필요하지 않는다. (아이템을 보고 추천을 진행)
- 새로운 아이템이나 인기도가 낮은 아이템을 추천할 수 있다.
- 추천 아이템에 대해 왜 추천이 되었는지 설명할 수 있다.

**단점**

- 아이템의 적합한 특징(feature)를 찾는 것이 어렵다.
- 한 분야나 장르의 추천결과만 계속해서 나올 수 있다. (Overspecialization)
- 다른 유저의 데이터를 활용할 수 없다.

## Item Profile

아이템을 기반으로 추천이 이루어지기 때문에, 각 아이템이 어떤 속성을 가지고 있는지 저장하는 정보가 필요하다. 그 정보를 Item Profile이라고 부른다.

- ex. 영화: 작가, 제목, 배우, 장르, 감독

아이템이 가진 다양한 속성을 어떻게 저장해야 가장 편하게 사용할 수 있을까. 바로 벡터 형태를 사용하는 것이다. 이 벡터값에는 0 또는 1의 값만 갖는 이진값이나 실수값을 사용해볼 수 있다.

### TF-IDF for Text Feature

문서는 텍스트로 이루어져있고, 텍스트는 다시 단어들의 집합으로 구성되어 있다. 이 단어들 중에서 중요한 단어를 선정해서 대표하는 값으로 나타내볼 수 있다. 이 과정에서 중요한 단어를 선정하기 위해서 각 단어의 중요도를 나타내는 Score값이 필요하게 되고 여기에 TF-IDF를 적용시켜 볼 수 있다.

**TF-IDF** (Term Frequency - Inverse Document Frequency)의 말 뜻을 풀어보면서 이해해보자. 어떠한 문서 $d$에서 등장하는 단어 $w$에 대해서, 단어 $w$가 많이 등장(TF)하고, 단어 $w$가 전체 문서 $D$에서는 적게 등장하는 단어라면(IDF) 해당 단어 $w$는 그 문서 $d$를 잘 나타낸다고 할 수 있다.

$$
\begin{aligned}
TF(w,d)&=\text{freq}_{w,d} \\
&\Rightarrow\frac{(\text{freq}_{w,d})}{\underset{k}{\max}(\text{freq}_{k,d})} \quad \text{(normalized)}\\
IDF(w)&=\log\frac{n(D)}{n_w}
\end{aligned}
$$

$$ somtehing $$

- $n_w : w$가 등장하는 문서의 개수
- IDF의 값은 변화가 크기 때문에 $\log$를 사용해서 smoothing을 진행하였다.

## User Profile 기반 추천

TF-IDF를 사용해서 아이템에 대한 Profile을 생성했지만, 우리가 해야하는 것은 이 아이템을 알맞은 유저에게 추천을 하는 것이다. 그렇기 때문에 유저의 취향정보인 User Profile을 생성해야 한다.

### User Profile

유저가 과거에 선호했던 아이템 list가 있고, 각각의 아이템들은 TF-IDF를 통해서 벡터로 표현이 되었다. 각 유저의 아이템 list내에 있는 아이템들의 벡터값들을 통합하면 그것이 User Profile이 된다.

통합하는 방법에는 2가지가 있다.

- **Simple** : 유저가 선호한 아이템 벡터들의 평균값을 사용한다.
- **Variant** : 유저가 아이템에 내린 선호도로 정규화한 평균값을 사용한다.

### Cosine Similarity

이제 Item Profile과 User Profile을 모두 생성했다. 이제 두 Profile이 유사한 아이템과 유저간의 매칭이 이루어져야 한다. 비슷한 정도를 측정하기 위해서 주로 코사인 유사도를 사용한다.

어떤 두 벡터 $X, Y$ 에 대해서 코사인 유사도는 다음과 같이 계산해볼 수 있다.

\begin{aligned}
  \cos(\theta)=\cos(X, Y)=\frac{X\cdot Y}{\vert X\vert\vert Y\vert}=\frac{\sum_{i=1}^{N}X_iY_i}{\sqrt{\sum_{i=1}^{N}X_i^2}\sqrt{\sum_{i=1}^{N}Y_i^2}}
\end{aligned}


두 벡터의 코사인 유사도를 구할 때 유의점은 **두 벡터의 차원이 같아야 한다**는 것이다. 분명 위에서 아이템을 기반으로 User Profile을 생성하였다면 두 차원은 같게 나올 것이다.

코사인 유사도의 결과값으로 1에 가깝게 나왔다면 두 벡터의 방향이 비슷한 것이고, 정 반대는 -1에 가까워진다.

![image](https://user-images.githubusercontent.com/91870042/158012009-9a4dd57b-54cf-4f0d-a977-5043c0f4ce1e.png){: .align-center width="70%"}

### User와 Item사이의 거리

코사인 유사도를 사용해서 User Profile 벡터와 Item Profile 벡터가 얼마나 유사한지를 판단하고 가깝다면 추천을 진행하면 된다. 하지만 유사도의 측정이 아닌 유저 $u$가 아이템 $i$ 에 대해 가질 선호도(평점)을 정확하게 예측하고 싶다면 어떻게 할 수 있을까.

### Item Vector를 사용한 평점예측

유저 $u$ 가 선호한 아이템 리스트 $ I=\{i_1,\dots, i_N\} $ 중에서 특정 Item에 대한 벡터를 $ V=\{v_1,\dots, v_N\} $ 라고 하고, 평점을 $ r_{u,i} $라고 했을 때, 새로운 아이템 $i'$에 대해 평점을 예측해보자.

새로운 아이템과 기존 아이템간의 유사도를 구하는 식

$$ \text{similarity}(i, i') = \cos(v_i, v_{i'}) $$

위에서 구한 유사도를 가중치로 하여 새로운 아이템에 대한 평점을 추론해볼 수 있다.

$$ \text{prediction}(i') = \frac{\sum_{i=1}^{N}\text{similarity}(i, i')\cdot r_{u, i}}{\sum_{i=1}^{N}\text{similarity}(i, i')} $$

---

영화 평점을 예측하는 다음 예제를 살펴보자. 기존에 선호한 영화는 3개가 있고 이번에 새로운 영화에 대한 평점을 예측하면 다음과 같다.

$$
\begin{aligned}
    m_1 : r_{m_1} = 3.0, v_{m_1} = [0.2, 0.4, 1.2, 1.5] \\
    m_2 : r_{m_2} = 2.5, v_{m_2} = [0.4, 0.7, 0.3, 0.5] \\
    m_3 : r_{m_3} = 4.0, v_{m_3} = [0.3, 1.2, 1.0, 1.0]
\end{aligned}
$$

예측하고자 하는 영화($m_4$)의 벡터($v_{m_4}$)

$$
v_{m_4} = [0.4, 1.4, 3.1, 1.0]
$$

$$
\begin{aligned}
    \text{sim}(m_4, m_1) = \cos(v_{m_4}, v_{m_1}) = 0.83 \\
    \text{sim}(m_4, m_2) = \cos(v_{m_4}, v_{m_2}) = 0.72 \\
    \text{sim}(m_4, m_3) = \cos(v_{m_4}, v_{m_3}) = 0.88
\end{aligned}
$$

$$ m_4 = \frac{0.83\times 3.0 + 0.72\times 2.5 + 0.88 \times 4.0}{0.83+0.72+0.88} = 3.2 $$

