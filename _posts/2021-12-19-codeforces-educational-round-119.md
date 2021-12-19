---
title:  "Educational Codeforces Round #119 (Div.2)"
excerpt: "A, B Solved, C, E upsolving"

categories:
  - codeforces-contest
tags:
  - [C++, Algorithm, Div2]

toc: true
toc_sticky: true
 
date: 2021-12-19
last_modified_at: 2021-12-19
---

<br>

# <span style = "color: #00adb5">A. Equal or Not Equal</span>

## 1. 문제 출처

[Problem - A - Codeforces](https://codeforces.com/contest/1620/problem/A)

## 2. 문제 해설 & 분류

환형 순열이 주어졌을 때, 주어진 조건들을 만족하는 순열이 존재하는지를 찾는 문제이다. 주어지는 조건에는 다음 2가지가 존재한다.
1. `E` : \\(a_i\\)와 \\(a_{i+1}\\)이 **동일하다**는 것을 나타낸다.
2. `N` : \\(a_i\\)와 \\(a_{i+1}\\)이 **다르다**는 것을 나타낸다.

이제 모든 조건이 E인 경우부터, N을 하나씩 추가해가면서 한 번 생각해보자.
1. 모든 조건이 `E`로만 이루어진 경우  
    모든 수가 동일하면 해당 환형 수열을 만들 수 있기 때문에 가능하다.

2. `N`이 하나만 포함되어 있는 경우  
    환형 수열인데, 한 곳이 틀어진 경우는 불가능하다. 적어도 `N`이 2개 이상 포함되어 있어야 환형 수열이 생성된다.

    예를 들어, "EENE"인 경우를 생각해보자. a[0] = a[1] = a[2] != a[3] = a[0].   
    `!=`를 기준으로 정리했을 때, a[0] != a[0]과 같은 식이 만들어지게 되어 오류가 발생한다. 그렇기 때문에 `N`이 하나만 있는 경우가 불가능하다.

따라서 주어진 조건에서 `N`의 등장횟수를 세고, 그 개수가 1인 경우만 `NO`를 출력하면 되는 문제이다.

시간복잡도: \\(O(N)\\)

분류: `constructive algorithms` `implementations`

## 3. 정답 코드
```cpp
#include <bits/stdc++.h>
 
using namespace std;
 
int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
 
    int testcase; cin >> testcase;
    while (testcase--) {
        string s; cin >> s;

        int cnt = 0;
        for (char c : s) if (c == 'N') ++cnt;

        if (cnt == 1) cout << "NO\n";
        else cout << "YES\n";
    }
 
    return 0;
}
```

<br>

# <span style = "color: #00adb5">B. Triangles on a Rectangle</span>

## 1. 문제 출처

[Problem - B - Codeforces](https://codeforces.com/contest/1620/problem/B)

## 2. 문제 해설 & 분류

높이가 \\(h\\)이고, 너비가 \\(w\\)인 직사각형이 주어졌을 때, 각 변위에 여러개의 점들이 놓여져 있다. 그 점들 중, 한 변에서 2점을 선택하고 나머지 변에서 한 점을 선택하여 삼각형을 만들고 만들어지는 넓이의 최댓값을 출력하는 문제이다.

각 변위에는 최소 2개 이상의 점이 주어지므로, 삼각형의 넓이를 최대로 만들기 위해서는 밑변의 길이가 가장 커야한다. 그렇기 때문에 한 변에 주어지는 가장 작은 점과 가장 큰 점을 선택하여 밑변으로 설정한다. 다시, 넓이를 최대화 하기 위해서 수직으로 가장 높이 있는 지점을 선택한다.

\begin{aligned}
    (P_{end} - P_{start}) \times h \\\\\\
    (P_{end} - P_{start}) \times w
\end{aligned}

수식으로 나타내면 위와 같다. x축과 평행한 변을 밑변으로 선택한다면, 높이 \\(h\\)를 선택하고, y축과 평행한 변을 밑변으로 선택한다면, 너비 \\(x\\)를 높이로 설정해야 한다.

시간복잡도: \\(O(n)\\)

분류: `geometry` `greedt` `math`

## 3. 정답 코드
```cpp
#include <bits/stdc++.h>
#define ll long long
using namespace std;
 
int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
 
    int testcase; cin >> testcase;
    while (testcase--) {
        ll w, h; cin >> w >> h;
        vector<ll> answer;
        for (int i = 0; i < 2; ++i) {
            int k; cin >> k;
            vector<ll> arr;
            for (int j = 0; j < k; ++j) {
                int temp; cin >> temp;
                arr.push_back(temp);
            }
            ll len = arr.back() - arr[0];
            ll S = len * h;
            answer.push_back(S);
        }
 
        for (int i = 0; i < 2; ++i) {
            int k; cin >> k;
            vector<ll> arr;
            for (int j = 0; j < k; ++j) {
                int temp; cin >> temp;
                arr.push_back(temp);
            }
            ll len = arr.back() - arr[0];
            ll S = len * w;
            answer.push_back(S);
        }
 
        cout << *max_element(answer.begin(), answer.end()) << "\n";
    }
 
    return 0;
}
```

<br>

# <span style = "color: #00adb5">C. BA-String </span>

## 1. 문제 출처

[Problem - C - Codeforces](https://codeforces.com/contest/1620/problem/C)

## 2. 문제 해설 & 분류

가장 먼저, 문자열에서 등장하는 `*`의 세그먼트를 모두 찾아낸다. 예를 들어 \\(t\\) 개의 `*`세그먼트가 존재한다고 하면, 각각의 세그먼트에 존재하는 `*`의 개수를 \\(C_1, C_2,\dots , C_t\\)라고 하자. 이렇게 나타내면, \\(i\\) 번째 세그먼트에는 문자 'b'가 최대 \\(C_i \times k\\) 개 만큼 존재할 수 있음을 알 수 있다.

어떤 두 문자열을 비교할 때, 각 세그먼트에 등장하는 'b'의 개수만을 이용해서 비교할 수 있음에 주목해야 한다. 문자열 a 에서 등장하는 `*` 세그먼트를 \\(A_1, A_2, \dots , A_t\\)라고 하고, 문자열 b 에서 등장하는 세그먼트를 \\(B_1, B_2, \dots, B_t\\)라고 하자. a < b가 되기 위해서는 \\(A_i < B_i\\)인 부분이 존재해야 한다. 

그렇기 때문에 사실 순열 \\(A_1, A_2, \dots, A_t\\)를 각각 수로 나타낼 수 있다. A와 B의 비교를 통해서 더 작은것이 사전순으로 앞으로 오기 때문이다.

이번에는 각 세그먼트에 최대 몇자리의 문자 b가 들어올 수 있는지 확인해야 한다. 하나의 `*`에는 \\(k\\) 개의 문자 b가 들어갈 수 있기 때문에 A_i에는 \\(0 \sim C_i \times k\\)개 만큼 들어갈 수 있다.

문자열의 가장 마지막의 세그먼트에 들어가야하는 문자 b의 개수는 x를 \\(C_i \times k\\)로 나눈 나머지이다. 몫을 다시 x로 업데이트 하여 그 이전의 세그먼트에도 이와 같이 반복적으로 계산해준다. 이렇게 총 \\(t\\)번의 단계를 거치면 문자 b가 각 세그먼트에 몇 개 있어야 하는지 파악할 수 있다.

분류: `brute force` `greedy` `implementation` `dp` `math`


## 3. 정답 코드
```cpp
#include <bits/stdc++.h>
#define ll long long
using namespace std;

int n; // 문자열의 길이
int k; // * 하나에 등장할 수 있는 b의 수
ll x; // 사전순으로 정렬했을 때, 만들어진 x번째 문자열

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);

    int testcase; cin >> testcase;
    while (testcase--) {
        cin >> n >> k >> x;

        x -= 1; // 가장 처음 문자열은 b를 하나도 포함하지 않는다.

        string s; // 입력받은 문자열
        cin >> s;

        reverse(s.begin(), s.end());
        ll index = 0;

        string answer;
        while (index < n) {
            if (s[index] == 'a')
                answer += 'a';
            else {
                ll j = index;

                // 연속된 * 의 개수를 구한다.
                while (j + 1 < n) {
                    if (s[j + 1] == '*') ++j;
                    else break;
                }
                
                // 사용할 수 있는 문자 b의 개수
                ll cur = (j - index + 1) * k + 1;

                // 전체 개수 % cur만큼 b를 append
                for (ll i = 0; i < (x % cur); ++i) answer += 'b';

                x /= cur;
                index = j;
            }
            ++index;
        }

        reverse(answer.begin(), answer.end());
        cout << answer << "\n";
    }

    return 0;
}
```

<br>

# <span style = "color: #00adb5">E. Replace the Numbers </span>

## 1. 문제 출처

[Problem - E - Codeforces](https://codeforces.com/contest/1620/problem/E)

## 2. 문제 해설 & 분류

주어지는 쿼리를 거꾸로 생각해보자. 모든 변경사항을 적용하였을 때, \\(x\\)가 최종적으로 어떤 수로 변경하는지 알려주는 배열 \\(p[x]\\)를 만든다.

1. 현재 쿼리가 1번인 경우 - 배열에 값을 추가  
단순하게 answer배열에 값을 추가한다.

2. 현재 쿼리가 2번인 경우 - 값을 업데이트  
parent[x]의 값을 변경해야 한다. x의 값을 전부 y로 변경해야 하기 때문에 \\(p[x] = p[y]\\)로 변경한다.

각 쿼리의 시간복잡도는 \\(O(1)\\)이므로, 전체 시간복잡도는 모든 원소를 순회해야 하기 때문에 \\(O(n)\\)이 된다.

분류: `data structures` `dsu` `trees`


## 3. 정답 코드
```cpp
#include <bits/stdc++.h>
#define ll long long
using namespace std;

struct QueryInfo {
    int option;
    int x;
    int y;
};

int query; // 들어오는 쿼리의 개수
int parent[500001];

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);

    cin >> query;
    stack<QueryInfo> q;
    while (query--) {
        int option; cin >> option;
        if (option == 1) {
            int num; cin >> num;
            q.push({option, num, -1});
        } else {
            int original, change; cin >> original >> change;
            q.push({option, original, change});
        }
    }

    iota(parent, parent + 500001, 0);

    vector<int> answer;
    while (!q.empty()) {
        QueryInfo now = q.top(); q.pop();

        if (now.option == 1) {
            answer.push_back(parent[now.x]);
        } else {
            parent[now.x] = parent[now.y];
        }
    }

    reverse(answer.begin(), answer.end());
    for (int element : answer) cout << element << " ";
    cout << "\n";

    return 0;
}
```