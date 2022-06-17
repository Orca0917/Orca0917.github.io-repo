---
title:  "[C++] C. Paprika and Permutation (*1300)"
excerpt: "Codeforces Round #761 (Div. 2) / 1617C"

categories:
  - codeforces-problem
tags:
  - [C++, 알고리즘, 이분탐색, 수학, 그리디 알고리즘, 정렬]

toc: true
toc_sticky: true
 
date: 2021-12-28
last_modified_at: 2021-12-28
---

# C. Paprika and Permutation

## 1. 문제 출처

[1617C : C. Paprika and Permutation](https://codeforces.com/problemset/problem/1617/C) `*1300`

## 2. 문제 해설
중복되지 않는 \\(n\\)개의 수가 주어졌을 때, 이 수들을 적절히 바꿔서 1~n까지의 모든 수를 포함하도록 만드는 것이 목표이다. 어떤 수를 바꿀 수 있는 연산에는 모듈러 연산이 존재한다. 어떤 수에 대해서, 특정 인덱스 \\(i\\)를 선택하고, 그 수를 임의의 수 \\(x\\)로 나눈 나머지로 바꿀 수 있다.
\\[
    A[i] = A[i] \mod x
\\]

어떤 \\(A[i]\\) 의 수를 \\(val\\) 로 변경하고 싶다면 최소 \\(val \times 2 + 1\\) 이상인 수가 존재해야 한다. 또한, 바꾸는 연산횟수를 최소화해야하기 때문에 이미 1~n 사이의 수 중 등장하는 수가 있다면, 미리 기록한다(코드에서는 check[]로 표시한다). 해당하는 숫자를 제외한 나머지 수를 가지고 위와 같은 연산을 진행한다. 

가장 마지막 index부터 진행하는 이유는 앞에서 진행했을 때, **erase()연산의 시간이 더 오래 걸려서 시간초과가 발생하는 것을 방지**한다. 마지막 index부터 할 경우, 가장 뒤의 원소만 pop하면 되기 때문에 훨씬 효율적이다. pop한 원소는 index값으로 변환이 가능하다는 의미이다.

## 3. 정답 코드

```cpp
#include <bits/stdc++.h>
#define ll long long
#define pii pair<int, int>

using namespace std;
vector<bool> check(100001, false);

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);

    
    int testcase; cin >> testcase;
    while (testcase--) {
        int n; cin >> n;
        vector<ll> A;
        fill(check.begin(), check.end(), false);

        for (int i = 0; i < n; ++i) {
            int x; cin >> x;
            if (x <= n && check[x] == false) check[x] = true;
            else A.push_back(x);
        }

        sort(A.begin(), A.end());

        bool canMake = true;
        int result = 0;
        int matched = 0;

        for (int i = n; i >= 1; --i) {
           if (check[i]) continue;

           if (A.back() < 2 * i + 1) {
               canMake = false;
               break;
           }

           A.pop_back();
           ++result;
        }
        
        if (!canMake) cout << "-1\n";
        else cout << result << "\n";
    }
       
    return 0;
}
```