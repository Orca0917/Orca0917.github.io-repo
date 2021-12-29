---
title:  "Codeforces Round #763 (Div.2)"
excerpt: "A, B solved, C upsolving"

categories:
  - codeforces-contest
tags:
  - [C++, 알고리즘, Div2]

toc: true
toc_sticky: true
 
date: 2021-12-29
last_modified_at: 2021-12-29
---

<br>

# A. Robot Cleaner

## 1. 문제 출처

[Problem - A - Codeforces](https://codeforces.com/contest/1623/problem/A)

## 2. 문제 해설 & 분류

풀이에는 2가지 방법이 존재한다. 하나는 시뮬레이션을 통한 정답의 도출이고, 하나는 연산을 통해서 정답을 알아낼 수 있다.

> 방법1: 시뮬레이션

실제로 로봇이 움직이는 방향으로 좌표를 이동하며, 벽에 닿았을 때 방향만 꺽어주는 형식으로 구현한다.
```cpp
while (dx != mx && dy != my) {

    // 다음 이동칸에서 벽에 부딪히는 경우
    if (mx + mv1 == 0 || mx + mv1 == n + 1) mv1 = -mv1;
    if (my + mv2 == 0 || my + mv2 == m + 1) mv2 = -mv2;
    
    // 로봇의 움직임대로 이동
    mx += mv1;
    my += mv2;
    
    // 이동횟수
    ++answer;
}
```

> 방법2: 연산

처음 시작할때 로봇의 방향은 항상 오른쪽 아래로 움직인다. 그렇다면, 청소하고 싶은 곳의 위치가 로봇보다 왼쪽에 있는 경우 반드시 오른쪽 벽에 부딪히고 돌아와야 한다.
또한, 하나더 고려해야 하는 것은 청소할 위치가 로봇보다 위에 있는 경우이다. 이 경우 마찬가지로 아래쪽 벽에 부딪히고 돌아와야 한다. 따라서 위의 2가지 이동 중에서 더 작은 이동 횟수를 출력한다. \\(answer = min(move_x, move_y)\\)

\\[
move_x = \begin{cases}
  dirt_x - init_x &\text{ if } dirt_x \geq init_x \\\\\\
  dirt_x - init_x + 2(n - dirt_x) &\text{ if } dirt_x < init_x
\end{cases}
\\]


분류: `브루트포스` `수학` `구현`

## 3. 정답 코드

```cpp
#include <bits/stdc++.h>
#define ll long long
#define pii pair<int, int>

using namespace std;

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);
    
    int testcase; cin >> testcase;
    while (testcase--) {
        int n, m, mx, my, dx, dy;
        cin >> n >> m >> mx >> my >> dx >> dy;

        int ansx = 0, ansy = 0;

        if (mx <= dx) {
            ansx = dx - mx;
        } else {
            ansx = dx - mx;
            ansx += (n - dx) * 2;
        }

        if (my <= dy) {
            ansy = dy - my;
        } else {
            ansy = dy - my;
            ansy += (m - dy) * 2;
        }

        cout << min(ansx, ansy) << "\n";
    }
       
    return 0;
}
```

<br>

# B. Game on Ranges

## 1. 문제 출처

[Problem - B - Codeforces](https://codeforces.com/contest/1623/problem/B)

## 2. 문제 해설 & 분류

어떤 구간 [1, n]이 있을 때, 각 구간 중 하나의 수 \\(k\\)를 선택해서, 구간을 [1, k-1], [k+1, n]으로 나눈다. 하지만, 문제에서 k의 값을 알려주지 않고, 결과로 나온 구간들만 주어졌을 때, 각 구간에대해서 선택한 \\(k\\)값이 무엇이었는지 출력하면 된다.

여러가지 풀이법이 존재하지만, 나는 `deque`을 사용해서 문제를 해결했다. 먼저 deque에 입력된 구간을 정렬한다. 구간을 [l, r]이라고 했을 때, 정렬하는 기준은 다음과 같다.
- 주어진 구간에서 l이 더 작은 것이 앞으로 올 수 있도록 한다.
- l이 동일하다면, 구간의 크기가 더 큰것이 앞으로 오게 한다.

이제 정렬이 완료되었다면, 가장 앞에 있는 원소부터 차례대로 문제를 해결한다. 가장 처음에 있는 원소는 당연히 전체의 구간을 포함하고 있는 원소일 것이다. 해당 원소를 꺼내어 `pop`연산을 수행한다. 그 다음 위에 있는 원소의 l값이 동일하다면, k의 값은 front()의 r + 1 값이다. 하지만 l값이 동일하지 않다면, 그 구간에서는 l을 선택한 것이라고 볼 수 있다. 이런식으로 마지막 원소까지 연산을 수행한다.

분류: `브루트포스` `구현` `정렬`

## 3. 정답 코드

```cpp
#include <bits/stdc++.h>
#define ll long long
#define pii pair<int, int>

using namespace std;

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);
    
    int testcase; cin >> testcase;
    while (testcase--) {
        int n; cin >> n;
        deque<pii> A;
        for (int i = 0; i < n; ++i) {
            int start, end; cin >> start >> end;
            A.push_back({start, end});
        }

        sort(A.begin(), A.end(), [](const pii &a, const pii &b) {
            if (a.first == b.first)
                return a.second - a.first > b.second - b.first;
            return a.first < b.first;
        });

        while (!A.empty()) {
            int start = A.front().first;
            int end = A.front().second;
            A.pop_front();

            if (start == end) {
                cout << start << " " << end << " " << start << "\n";
                continue;
            }

            int result = 0;
            if (start == A.front().first) result = A.front().second + 1;
            else result = start;
            cout << start << " " << end << " " << result << "\n";
        }
        cout << "\n";
    }
       
    return 0;
}
```

<br>

# C. Balanced Stone Heaps

## 1. 문제 출처

[Problem - C - Codeforces](https://codeforces.com/contest/1623/problem/C)

## 2. 문제 해설 & 분류

문제 마지막에 이런말이 주어져있다.

> What is the maximum number of stones in the smallest heap after process?

**최소 중 최대 구하기** 즉 `이분탐색`을 사용하라는 의미이다. 주머니에 존재하는 돌 중에서 가장 작은 것의 개수를 \\(mid\\)값으로 설정하고 이분탐색을 수행한다. 여기까지 생각을 했지만, `check()`함수를 구현하는데 있어서 어려움을 겪었다. 문제에서 주어진 testcase는 통과했지만, pretest2에서 막혀있었다.

핵심적인 생각은 "가장 뒤에 원소에서부터 앞에 있는 원소로 줄 수 잇는 만큼 모두 넘긴다. 단, 원래의 개수보다 더 많이 넘기는 것은 불가능하다." 이다. 그 이후, 모든 원소에 대해서 \\(mid\\)보다 더 적은 개수를 가진 주머니가 있다면, false를 return한다. 위의 생각을 다음과 같이 작성할 수 있다.

```cpp
// H[i] : i번째 주머니속에 존재한 돌의 수
// n : 전체 주머니의 수
auto check = [n, H](const int x) -> bool {
    // 연산에 사용하기 위한 복사본 생성
    vector<int> P = H;

    for (int i = n - 1; i >= 2; --i) {
        if (P[i] < x) return false;

        // 앞, 앞앞 주머니에 줄 수 있는 조약돌 수
        int extra = min(P[i] - x, H[i]);
        int d = extra / 3;

        P[i - 2] += 2 * d;
        P[i - 1] += d;
        P[i] -= 3 * d;
    }

    for (int i = 0; i < n; ++i) if (P[i] < x) return false;

    return true;
};
```
분류: `이분탐색` `그리디 알고리즘`


## 3. 정답 코드

```cpp
#include <bits/stdc++.h>
#define ll long long
#define pii pair<int, int>

using namespace std;

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);
    
    int testcase; cin >> testcase;
    while (testcase--) {
        int n; cin >> n;
        vector<int> H(n);
        for (int &h : H) cin >> h;

        auto check = [n, H](const int x) -> bool {
            // 연산에 사용하기 위한 복사본 생성
            vector<int> P = H;

            for (int i = n - 1; i >= 2; --i) {
                if (P[i] < x) return false;

                // 앞, 앞앞 주머니에 줄 수 있는 조약돌 수
                int extra = min(P[i] - x, H[i]);
                int d = extra / 3;

                P[i - 2] += 2 * d;
                P[i - 1] += d;
                P[i] -= 3 * d;
            }

            for (int i = 0; i < n; ++i) if (P[i] < x) return false;

            return true;
        };

        int lo = 1, hi = 1e9;
        int ans = 1;

        while (lo <= hi) {
            int mid = (lo + hi) / 2;
            if (check(mid)) {
                ans = mid;
                lo = mid + 1;
            } else {
                hi = mid - 1;
            }
        }

        cout << ans << "\n";
    }
    
    return 0;
}
```