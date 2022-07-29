---
title:  "Codeforces Round #810 (Div.2)"
excerpt: "ABC 문제풀이"

categories:
  - codeforces-contest
tags:
  - [C++, 알고리즘, Div2]

toc: true
toc_sticky: true
 
date: 2022-07-29
last_modified_at: 2022-07-29
---

## A. Perfect Permutation

문제 링크 : [https://codeforces.com/contest/1711/problem/A](https://codeforces.com/contest/1711/problem/A)

풀이 링크 : [https://github.com/killerWhale0917/Algorithm/blob/main/Codeforces/1711A.cpp](https://github.com/killerWhale0917/Algorithm/blob/main/Codeforces/1711A.cpp)

- $1 \sim N$ 까지의 수가 한 번씩 등장하는 수열이 있을 때 이를 적절히 배치해서 $\text{arr}[i]\mod i$ 의 값이 0이 되지 않도록 수열을 재배치 해야하는 문제입니다.

- $i=1$ 인 경우를 제외하고는 숫자 하나씩 왼쪽으로 shift 하여 모두 나누어 떨어지지 않게 만드는 것이 가능합니다. 

- ex. [1, 2, 3, 4, 5] $\rightarrow$ [2, 3, 4, 5, 1]

```cpp
for (int i = 1; i < N; ++i) answer[i] = i;
answer[0] = N;
```

<br>

## B. Party

문제 링크 : [https://codeforces.com/contest/1711/problem/B](https://codeforces.com/contest/1711/problem/B)

풀이 링크 : [https://github.com/killerWhale0917/Algorithm/blob/main/Codeforces/1711B.cpp](https://github.com/killerWhale0917/Algorithm/blob/main/Codeforces/1711B.cpp)

- $n$ 명의 사람과 $m$ 개의 친구쌍 정보가 주어졌을 때, 이들을 파티에 초대하는 문제입니다.

- 파티에 오지 못하는 인원이 있을 때는 해당 인원 $i$ 의 $\text{unhappy}[i]$ 만큼의 감점을 당합니다.

- 먼저 케이크를 짝수개 준비해야 하기 때문에, 연결된 간선을 짝수개만큼 이용할 수 있습니다. 친구쌍 1개는 케이크를 1개 소비함과 같습니다.

- 그렇기 때문에, 전체의 친구쌍 정보 $m$이 짝수라면 모든 인원이 참가할 수 있기 때문에, 0 을 출력합니다.

- 이제 남은 상황은 $m$ 이 홀수인 상황이며, $m$ 이 짝수가 되도록 한 친구쌍을 제거하거나, 친구 1명을 초대하지 않게 하는 방법이 있습니다. 이 2가지 방법에 대해 모두 시도를 해서 둘 중 작은 값을 최종 결과로 출력합니다.

- 하나 주의해야할 것은, 어떤 친구를 초대하지 않거나, 친구쌍을 초대하지 않을 때는 그 친구와 연결된 친구의 수를 파악해줘야 합니다. 친구 1명을 초대하지 않을 경우에는 그 친구와 연결된 친구의 수가 홀수이어야 하며, 반대로 친구쌍 (친구 2명)을 초대하지 않을 때는, 연결된 친구의 수가 둘다 짝수이어야 합니다.

```cpp
for (int i = 1; i <= n; ++i) 
  if (degree[i] % 2 == 1) 
    answer = min(answer, unhappy[i]);
        
for (int i = 0; i < m; ++i) 
  if (degree[friends[i].first] % 2 == 0 && degree[friends[i].second] % 2 == 0) 
    answer = min(answer, unhappy[friends[i].first] + unhappy[friends[i].second]);
```

<br>

## C. Color the Picture

문제 링크: [https://codeforces.com/contest/1711/problem/C](https://codeforces.com/contest/1711/problem/C)

풀이 링크: [https://github.com/killerWhale0917/Algorithm/blob/main/Codeforces/1711C.cpp](https://github.com/killerWhale0917/Algorithm/blob/main/Codeforces/1711C.cpp)

- $n \times m$ 크기의 격자판이 주어졌을 때, 각 칸에 적절한 색상을 칠해서 `toroidal` 상태를 만족하도록 해야하는 문제입니다.

- 여기서 말하는 `toroidal` 상태는 다음 2 식을 만족하는 2 칸을 의미합니다. 문제는 모든 칸이 toroidal 상태를 만족하게 해야합니다.

$$
x_1 - x_2 \equiv \pm 1 \text{ mod } n \quad \text{and} \quad y_1 = y_2 \\
y_1 - y_2 \equiv \pm 1 \text{ mod } m \quad \text{and} \quad x_1 = x_2
$$

- 위의 식을 해석하면, 만약 어떤 칸 $A$가 $n \times m$ 의 테두리가 아닌 내부에 존재한다면, 그 칸의 상하좌우가 위의 식을 만족하는 칸이 되며, 만약 테두리에 존재한다면, 상하 또는 좌우를 선택하고 막힌쪽의 반대쪽으로 칸이 선택됩니다. 그림으로 표현하면 다음과 같은 상태입니다.

![image](https://user-images.githubusercontent.com/91870042/181748076-804fe27a-e3ff-4c4d-96bb-73c75104b59d.png){: .align-center}

- 그렇다면, 모든 칸이 위와 같은 상태를 만족하려면 다음과 같이 최소 2줄 이상이 같은 색상으로 칠해진 상황이어야만 가능합니다.

![image](https://user-images.githubusercontent.com/91870042/181748233-bb31a334-1a86-4eb4-a9ec-0da47029b408.png){: .align-center}

- 위의 상황을 만족하는 것을 가로 또는 세로를 기준으로 하여 탐색을 진행하면 정답을 구하는 것이 가능합니다. 특히 만약 세로나 가로의 칸 수가 홀수인 경우에는 어떤 색상이 3줄 이상 칠해져야 하므로, 해당 경우만 따로 판단하는 코드를 작성하면 됩니다.

```cpp
int check_possible(int val, ll targ) {
    ll possible = 0;
    bool over2 = false;

    // 같은 색으로 몇 줄 칠할 수 있는지?
    for (int i = 0; i < k; ++i) result[i] = a[i] / val;

    // 전체를 색칠할 수 있는지 확인
    for (int i = 0; i < k; ++i) {
        if (result[i] >= 2) possible += result[i];
        if (result[i] > 2) over2 = true;
    }

    // over2 : 마지막 1줄이 남는 경우를 채우기 위함
    // targ가 2로 나누어 떨어지는 경우, over2가 필요x
    return (possible >= targ) && (over2 || targ % 2 == 0);
}
```