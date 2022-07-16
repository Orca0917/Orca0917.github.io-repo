---
title:  "[Recommender System] 인기도 기반 추천"
excerpt: "상품의 인기도를 기반으로 유저에게 추천을 진행하는 방법"

categories:
  - RecSys
tags:
  - [AI, Naver, BoostCamp, Recommender System]
toc: true
toc_sticky: true
use_math: true

date: 2022-03-14 02:00:00
last_modified_at: 2022-03-14 02:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# 인기도 기반 추천

## 개요

인기도 기반 추천은 말 그대로 상품들 중에서 가장 인기있는 상품을 추천하는 것이다. 그렇다면 '인기있다는 것'은 어떻게 기준을 삼아서 분류할 수 있을지 생각해보아야 한다. 인기도의 척도에는 조회수, 평균평점, 리뷰의 개수, 좋아요와 싫어요의 개수 등 다양한 속성을 사용해볼 수 있다.

- **Most Popular** : 조회수가 가장 많은 아이템을 추천하는 것으로, 다른 유저들도 관심을 갖는 아이템을 추천하는 것이다. 보통 뉴스기사 추천을 예로 생각해볼 수 있다. 사람들은 다른 사람들도 많이 찾아본 이슈를 찾아보길 원한다.
- **Highly Rated** : 평균 평점이 가장 높은 아이템을 추천하는 것으로, 맛집 추천이 이에 해당한다. 평점이 높은 맛집일수록 맛있는 음식을 제공하는 음식점으로 생각해볼 수 있다.

## Most Popular

![image](https://user-images.githubusercontent.com/91870042/158136117-4584cee2-40c1-4e12-a95b-d34fb2511603.png){: .align-center width="70%"}

조회수가 많은 아이템을 추천하거나 좋아요가 가장 많은 아이템을 추천하는 것이 여기에 해당된다. 하지만 단순히 조회수가 많은 아이템만을 기준으로 삼아서 추천을 진행하면, 너무 오래되었지만 조회수가 높은 것들이 계속 추천이 될 것이다. 그렇기 때문에 얼마나 오래된 것인지 판단하는 age 속성과 함께 계산해야 한다.

$$
\begin{aligned}
\text{score} &= f(\text{popularity}, \text{age})\\
&= (\text{upvote} - \text{downvote}) - \text{time elapsed}\\
&= \text{page view} - \text{time elapsed}
\end{aligned}
$$

하지만, 위의 식에서 **pageview가 너무 빠르게 늘어나서 아주 큰 값을 가지게 된다면 시간이 아무리 지나도 상위권에 해당 아이템이 뜨는 문제가 발생**한다.

### Hacker News Formula

![image](https://user-images.githubusercontent.com/91870042/158137920-cb1ba358-5e9e-4f6f-bde5-14a0ca02e9e6.png){: .align-center width="40%"}

Hacker News에서는 많은 사람들이 본 뉴스를 추천하되, 최신의 정보를 포함하도록 설정하였다. 시간이 지날 수록 age값을 증가시켜서 전체적인 score 값을 감소시키는 방법을 사용하였다. 또한 시간에 따라 줄어드는 score를 조정하기 위해서 gravity라는 상수를 사용하였다.

$$ \text{score}=\frac{\text{page views}-1}{(\text{age}+2)^{\text{gravity}=1.8}} $$

### Reddit Formuula

![image](https://user-images.githubusercontent.com/91870042/158138058-5be23748-3fdd-40a5-9297-037d15918fee.png){: .align-center width="40%"}

Reddit에서는 위의 Hacket News처럼 score값을 분모로 나누어서 **감점시키는 방법을 사용하지는 않았다**. 반대로 **최근에 포스팅된 글에 더 높은 점수를 부여**해서 상위에 추천될수 있도록 만들었따.

$$ \text{score}=\log_{10}(\text{ups} - \text{downs})+\frac{\text{sign(ups-downs)}\cdot \text{seconds}}{45000} $$ 

위에서 첫 번째 term은 popularity, 두 번째 term은 글이 포스팅된 절대시간을 의미한다. 나중에 게시된 포스팅일수록 절대시간이 크기 때문에 더 높은 score를 갖도록 하였다.

하나 더 눈여겨 볼 점은 좋아요와 싫어요 수에 log를 씌운 것이다. 그 이유는 한 번 아이템이 상단에 올라가게 되면 추천수가 급증하기 때문에 그런 문제점을 해결하기 위해 점점 증가폭이 적어지는 log를 사용했다.

## Highly Rated

![image](https://user-images.githubusercontent.com/91870042/158138913-6d31c1df-d5a7-42a6-be4a-751a50bda89b.png){: .align-center width="70%"}

높은 평점을 받은 아이템을 추천하는 방법이지만, 생각해 보아야 할 것은 높은 평점이라고 반드시 그 점수를 신뢰할 수 있다는 보장은 없다. 예를 들어서 평가의 개수가 1000개인데 4.7점인 것과 평가의 개수가 1개인데 4.8점인 것을 비교하는 문제와 동일하다. 그렇기 때문에 평가의 개수도 같이 고려를 진행해야 한다.

$$ \text{score} = f(\text{rating}, \text{number of ratings}) $$

### Stream Rating Formula

전체 평점은 모든 평점에 대한 평균값을 사용하되, 그 개수에 따라서 보정을 하는 방법이다. 수식은 다음과 같다.

$$ \text{avg rating}=\frac{\text{number of positive reviews}}{\text{number of reviews}} $$

$$ \text{score} = \text{avg rating} - (\text{avg rating}-0.5)\cdot 2^{-log(\text{number of reviews})} $$ 

위의 수식은 review개수가 너무 적을 때 score값이 0.5보다 작다면 더 높게 보정을 하고, score의 값이 너무 크다면 0.5보다 낮게 보정을 진행해주는 수식이다. review의 개수가 정말 많다면 score값은 전체 에 대한 평균값과 거의 유사해진다. 가까워질 수 있는 것은 $2^{-\log}$ 식 덕분인데 매우 커지면 0에 가까워지기 때문이다.

예로, 영화평점이 1점~5점으로 부여될 수 있다면 그 평균값인 3점으로 균형을 맞추어줄 수 있다.

$$ \text{score} = \text{avg rating} - (\text{avg rating}-3.0)\cdot 2^{-log(\text{number of reviews})} $$ 

