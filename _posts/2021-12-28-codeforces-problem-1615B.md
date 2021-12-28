---
title:  "[C++] B. And It's Non-Zero (*1300)"
excerpt: "Codeforces Global Round 18 / 1615B"

categories:
  - codeforces-problem
tags:
  - [C++, 알고리즘, 비트마스킹, 수학, 그리디 알고리즘]

toc: true
toc_sticky: true
 
date: 2021-12-28
last_modified_at: 2021-12-28
---

# B. And It's Non-Zero

## 1. 문제 출처

[1615B : B. And It's Non-Zero](https://codeforces.com/problemset/problem/1615/B) `*1300`

## 2. 문제 해설
2개의 수 \\(l\\)과 \\(r\\)이 입력으로 들어올 때, \\(l\\)과 \\(r\\) 사이의 모든 수를 `AND`연산을 수행한다. 그 때의 결과값이 0 이 되지 않게 하기 위해서 최소한의 원소를 지우게 해야한다.

가장 먼저 생각 난 것은, \\(l\\)과 \\(r\\)사이의 존재하는 수를 순회하며 **각각의 비트가 꺼진 부분을 확인**한다. 이후, 그 사이의 수에 대해서 꺼진 비트들을 각 자리수에 맞추어서 전부 더한다. 그리고, 그 더한값 들 중 최소의 값이 정답이 된다. 하지만, 이 과정을 모든 테스트 케이스에 대해서 실행시키기에는 시간초과가 발생할 우려가 있기 때문에, 먼저 계산을 수행한다 (전처리)

1부터 20만 까지의 수에 대해서 각 자리수의 비트를 계산하고, 또 한번의 최적화를 수행하기 위해서 **누적합을 사용**한다. 누적합을 저장한 배열을 **digits[][]**배열에 저장을 했다. digits배열의 의미는 다음과 같다.

> digits[i][j] = 1 ~ i 번째 수까지 j번째 비트가 켜져 있는 것의 개수

이제 전처리가 완료되었기 때문에, 각 테스트 케이스에 대해서 연산만 수행하면 된다. 구간 [l, r]의 수의 합을 누적합을 이용해서 계산한다. \\(l\\)이 포함되어야 하기 때문에 누적합에서 \\(l - 1\\)을 빼주는 것에 유의해야 한다. 이후, <u>켜져 있는 비트수를 누적합에 저장했었기 때문에, 전체 개수에서 켜진 비트수를 빼서 꺼진 비트수를 계산한다</u>. 다시 그들의 최솟값을 출력한다.

## 3. 정답 코드

```cpp
#include <bits/stdc++.h>
#define ll long long
#define pii pair<int, int>

using namespace std;
vector<int> digits[200001];

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);
    
    const int inf = 1234567890;
    
    digits[0].resize(20, 0);
    for (int i = 1; i <= 200000; ++i) {
        digits[i].resize(20, 0);
        int bit = 1, k = 1;
        while (i >= bit) {
            digits[i][k] = digits[i - 1][k];
            if (i & bit) digits[i][k]++;
            bit = bit << 1;
            ++k;
        }
    }

    int testcase; cin >> testcase;

    while (testcase--) {
        int l, r; cin >> l >> r;
        int maxLen = ceil(log2(r)) + 1;
        
        vector<int> sum(20, 0);
        for (int i = 0; i < 20; ++i) {
            sum[i] = digits[r][i] - digits[l - 1][i];
        }
        
        int answer = inf;
        for (int i = 1; i <= maxLen; ++i) {
            answer = min(r - l + 1 - sum[i], answer);
        }
        cout << answer << "\n";

    }
       
    return 0;
}
```