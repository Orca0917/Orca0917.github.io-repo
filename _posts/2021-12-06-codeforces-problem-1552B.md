---
title:  "[C++] B. Running for Gold (*1500)"
excerpt: "Codeforces Global Round15 / 1552B"

categories:
  - codeforces-problem
tags:
  - [C++, Algorithm, Combinatorics, Graphs, Greedy, Sortings]

toc: true
toc_sticky: true
 
date: 2021-12-06
last_modified_at: 2021-12-06
---

# <span style = "color: #00adb5">B. Running for Gold</span>
## 1. 문제 출처

[1552B : Codeforces Global Round 15](https://codeforces.com/contest/1552/problem/B) `*1500`

## 2. 문제 해설 & 분류
\\(n\\)명의 운동선수와 그 이전 5경기의 기록이 주어졌을 때, 이번 경기에서 우승하게 될 사람을 예측하는 문제이다. 처음에 1번째 운동선수가 우승할 것이라고 지정을 하고 반복문을 돌면서 확인한다. 확인해야 할 것은 내가 그 운동선수를 이길 수 있다면, 우승할 선수를 나로 지정하는 것이다.

하지만 여기서 확인해야 하는 것은, 우승선수로 지정했어도 모순이 생기는 경우가 발생한다. 이런 경우가 발생하는 이유는 1번 선수가 2번선수를 이기고, 2번선수는 3번선수를 이기고, 3번선수는 1번 선수를 이기는 경우, 즉 사이클이 생기는 경우에 발생한다. 따라서 이런 과정을 확인하기 위해 다시 한번 For문을 반복하면서 모순이 있는지 확인한다.

추가로, `operator < `를 사용해서, 비교하는 함수를 통한 구현 방법도 있다. 해당 함수를 구현하기 위해서 사용한 코드는 다음과 같다.
```cpp
bool operator < (cosnt Rank &a, const Rank &b) {
    int winCnt = 0;
    for (int i = 0; i < 5; ++i) cnt += (a.r[i] < b.r[i]);
    return winCnt >= 3;
}
```

분류: `Combinatorics`, `Graphs`, `Greedy`, `Sortings`

## 3. 정답 코드

```cpp
#include <bits/stdc++.h>
#define pii pair<int, int>
using namespace std;
 
struct Rank {
    int r[5];
};
 
int main() {
    ios_base::sync_with_stdio(false); cin.tie(0);
    int testcase; cin >> testcase;
    while (testcase--) {
        int n; cin >> n;
        vector<Rank> athlete(n);
 
        for (int i = 0; i < n; ++i)
            for (int j = 0; j < 5; ++j)
                cin >> athlete[i].r[j];
 
        int winner = 0;
        for (int i = 0; i < n; ++i) {
            int winCnt = 0;
            for (int j = 0; j < 5; ++j) {
                winCnt += (athlete[i].r[j] < athlete[winner].r[j]);
            }
            if (winCnt >= 3) winner = i;
        }
 
        for (int i = 0; i < n; ++i) {
            int winCnt = 0;
            for (int j = 0; j < 5; ++j) {
                winCnt += (athlete[i].r[j] < athlete[winner].r[j]);
            }
            if (winCnt >= 3) {
                cout << "-1\n";
                goto A;
            }
        }
 
        cout << winner + 1 << "\n";
 
        A:
            continue;
    }
    return 0;
}
```