---
title:  "경사하강법 (Gradient Descent)의 개념과 문제점"
excerpt: "손실함수의 결과값을 최소화시키는 값을 찾기 위한 여정, 경사하강법에 대해서 다룹니다."

categories:
  - DLBasic
tags:
  - [AI, Naver, BoostCamp, Math]
toc: true
toc_sticky: true
 
date: 2022-02-07 04:00:00
last_modified_at: 2022-06-18 00:00:00
---

![image](https://user-images.githubusercontent.com/91870042/174464474-4651054e-9153-46cf-91ae-e77a1e32fc5d.png){: .align-center width="100%"}

# 경사하강법

바로 이전 포스팅에서 직접 만든 신경망 구조의 나쁨 정도를 평가할 수 있는 손실함수를 살펴보았습니다. 우리의 신경망이 좋아지기 위해서는 손실함수의 결과값인 오차가 최소로 줄어들게 만들어야 합니다. 고등학교에서는 이 값을 찾기위해서 미분하여 그 값이 0이 되는 극솟값을 찾아 문제를 해결하였습니다.

마찬가지로 딥러닝에서도 미분을 하여 0이 되는 값을 찾으면 됩니다! 하지만 딥러닝에 미분을 그대로 적용하기에는 한계점이 있습니다.

- 고등학교 때 미분했던 함수와는 다르게 매우 복잡한 형태의 함수를 미분해야 합니다. 이 때문에 미분계수와 그 값을 구하기가 복잡하고 어렵습니다😥
- 딥러닝은 데이터의 양이 매우 크기 때문에 반복적으로 국소적 미분을 통한 연산을 진행하면 계산량 측면에서 매우 효율적으로 최적의 해를 근사하는 것이 가능합니다.

> 그래서, 경사하강법은 어떻게 연산량을 최적화 할 수 있었을까?

## 🧐 아이디어

![image](https://user-images.githubusercontent.com/91870042/174464942-39f8c7e7-2218-4ad0-8eb3-edd859a29cf1.png){: .align-center width="50%"}

경사하강법에 대해 설명하기 위해 하나의 상황을 가정해봅시다. 현재 아주 어두운 산속에 놓인 나머지 앞을 전혀 볼 수 없는 상황입니다. 이때 산에서 내려오기 위한 방법을 강구해야합니다. 간단하게, 발의 감각을 사용하여 현재 지점이 오르막길인지 내리막길인지 판단하고 계속 내리막을 향해서 내려가다 보면 산 밑으로 내려올 수 있습니다. 바로 이 원리를 그대로 이용한 것이 경사하강법입니다.

좀 더 수학적으로 접근한다면, 바로 현재 지점의 기울기 정보만을 사용하여 최적의 해를 찾아나가는 방법입니다. 모든 지점의 미분값을 알 필요가 없고 현재의 미분값만 연산하면 되므로 연산량이 크게 감소할 수 있습니다. 이를 그림으로 나타내면 아래와 같이 그려지게 됩니다.

![image](https://user-images.githubusercontent.com/91870042/174465046-267aa6cf-d804-437f-bf51-70086616ad29.png){: .align-center width="50%"}

계산과정에서는 현재 지점의 기울기를 구하기 위해서 `미분의 극한` 개념을 사용하며, 이 미분값을 통해 어디로 가야 최저점으로 갈 수 있는지 알 수 있습니다.

정확히 말하자면 경사하강법은 $x^2$을 미분하였을 때 나오는 $2x$를 가지고 결과를 얻어내는 것은 아닙니다. 예로, 현재 지점 $\alpha$와 바로 옆 $\alpha + 0.001$의 함수 결과값을 비교하면 현재 지점의 기울기가 음수인지 양수인지 알 수 있습니다. 이런 단순한 연산과정 덕분에 빠른 처리가 가능한 것입니다. 더 상세한 내용은 아래의 경사하강법(1) 게시글을 참고해주세요🙏
{: .notice--success}

![image](https://user-images.githubusercontent.com/91870042/149925166-a7254354-8897-465f-a9b2-f97fc4b5b27a.png){: .align-center}

- 미분 값이 음수인 경우  
$x+f'(x) < x$: 왼쪽으로 이동하여 함수값이 증가한다.  
$x-f'(x) > x$: 오른쪽으로 이동하여 함수값이 감소한다.

- 미분 값이 양수인 경우  
$x+f'(x) < x$: 오른쪽으로 이동하여 함수값이 증가한다.  
$x-f'(x) > x$: 왼쪽으로 이동하여 함수값이 감소한다.

원래의 값에서 미분한 값을 빼주게 되면, 함수값이 감소하는 성질을 이용하여 경사하강법을 수식으로 나타내봅시다. 실제 손실함수는 다차원 함수이므로 다차원의 미분 표기법인 $\nabla$ 를 사용하여 나타냈으며 2차원 평면에서 $f'$ 연산과 비슷한 개념입니다.

$$
\mathbf{x}_{t+1} \leftarrow \mathbf{x}_t - \gamma_t \nabla f(\mathbf{x}_t)
$$

- $\mathbf{x}_t$ : $t$ 시점의 위치
- $\gamma_t$ : 이동할 때, 얼마나 이동할지 조절 (학습률)
- $f$ : 손실 함수(loss function)

학습률로 나타낸 $\gamma$ 는 **현재 방향으로 얼마나 이동할 것인가!** 를 나타내는 값입니다. 상수값으로 표현되며 값이 크다면 미분계수의 방향으로 더 크게 이동하게 됩니다. 미분과 편미분을 사용한 더 자세한 수학적인 내용은 AI Math 카테고리의 다른 게시물을 참고해주세요!

- [🌏 [AI Math] 경사하강법 (1)](https://killerwhale0917.github.io/aimath/boostcamp-math-gradient-descent/)
- [🌏 [AI Math] 경사하강법 (2)](https://killerwhale0917.github.io/aimath/boostcamp-math-gradient-descent2/)

<br>

## 😨 문제점

논리적인 근거를 갖는 경사하강법에도 문제는 있습니다. 실제 산 아래 마을은 더 밑인데 골짜기 같은 곳에 머무르는 경우입니다. 어두운 곳에서 발의 감각만을 사용하여 다 내려왔다고 생각했는데 알고보니 산의 가장 아래는 아니었습니다. 바로 이 지점을 `local minimum` 이라고 하며 이 지점을 탈출하기 위해 경사하강법에 여러 기법들을 도입하기 시작했습니다.

![image](https://user-images.githubusercontent.com/91870042/174465195-8439ee5f-7fa6-4d7a-a562-65fb19dab4d2.png){: .align-center width="50%"}

다른 문제점으로 `안장점 (Saddle Point)`이라는 상황이 존재합니다. 안장점은 마치 말의 안장과 같이 생겨서 불리는 이름으로 보는 관점에 따라 극소값이 될수도, 극대값이 될 수도 있습니다. 최적화된 값이 안장점에 도달한다면, 실제로는 더 내려갈 수 있는데 내려가지 못하는 문제가 발생합니다.

![image](https://user-images.githubusercontent.com/91870042/174466950-64c3cb3e-76de-4cae-8f7f-200e348a460a.png){: .align-center width="50%"}

<br>

이렇게 간단하게만 경사하강법이 무엇인지, 어떤 원리를 가지고 있는지 알아보았습니다. 아직 경사하강법의 문제를 해결한 방법에 대해서 소개해드리지는 않았지만, 기회가 된다면 딥러닝의 Optimizer 파트에서 한 번 다루도록 하겠습니다.

<br>

# References

[🎨 이미지 소스 : Gradient Descent](https://ml-cheatsheet.readthedocs.io/en/latest/gradient_descent.html)

[🌏 위키백과 : Saddle Point](https://en.wikipedia.org/wiki/Saddle_point)

[🌏 위키백과 : 극값](https://ko.wikipedia.org/wiki/%EA%B7%B9%EA%B0%92)

[🌏 IBM : How does gradient descent work?](https://www.ibm.com/cloud/learn/gradient-descent)

[🎬 YouTube : 경사 하강, 신경 네트워크가 학습하는 방법 / 심층 학습, 2장](https://www.youtube.com/watch?v=IHZwWFHWa-w&t=1s)