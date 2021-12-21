---
title:  "Codeforces Round #755 (Div.2)"
excerpt: "A, B, C solved"

categories:
  - codeforces-contest
tags:
  - [C++, Algorithm, Div2]

toc: true
toc_sticky: true
 
date: 2021-12-21
last_modified_at: 2021-12-21
---

<br>

# <span style = "color: #00adb5">A. Mathmatical Addition</span>

## 1. 문제 출처

[Problem - A - Codeforces](https://codeforces.com/contest/1584/problem/A) `*800`

## 2. 문제 설명

문제에서 1 이상의 두 수 u, v가 주어졌을 때, \\(\frac{x}{u}+\frac{y}{v}=\frac{x+y}{u+v}\\)를 만족하는 \\(x, y\\)를 찾는 문제이다. 단, 문제에서 \\(x,y\\)가 0으로 주어지는 경우는 입력되지 않는다.

## 3. 문제 해설 & 분류

위의 수식을 풀면 다음과 같다.
\begin{aligned}
    xv+uy&=\frac{x+y}{y+v}\\\\\\
    x+y&=(y+v)(xv+uy)\\\\\\
    xv^2+yu^2&=0
\end{aligned}

따라서 입력으로 \\(u, v\\)가 주어졌다면, \\(x=-u^2, y=v^2\\)을 출력하면 된다.

시간복잡도: \\(O(1)\\)

분류: `math` `number theory`

## 4. 정답 코드

```cpp
#include <bits/stdc++.h>
#define ll long long
#define pii pair<int, int>
#define MOD 1'000'000'007

using namespace std;

int testcase;
int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);

    cin >> testcase;
    while (testcase--) {
        ll u, v; cin >> u >> v; 
        ll x1 = u * u, x2 = v * v;
        cout << x1 << " " << -x2 << "\n";
    }

    return 0;
}
```

<br>

# <span style = "color: #00adb5">B. Coloring Rectangles</span>

## 1. 문제 출처

[Problem - B - Codeforces](https://codeforces.com/contest/1589/problem/B) `*1000`

## 2. 문제 설명

\\(n\times m\\)크기의 직사각형이 주어졌을 때, 이 직사각형을 가로 세로로 적절히 자른다. 이 직사각형을 나눌때, 크기가 \\(1\times1\\)이 되는 사각형이 나오면 안된다. 또한 나눠진 모든 사각형에 대해서 각 칸에 파란색으로 칠할 수 있는데, 파란색 또는 빨간색 끼리는 서로 인접해서는 안된다. 

이때, 처음에 주어진 직사각형을 적절히 나누어 파란색을 가장 적게 칠하는 수를 구하는 문제이다. 결과적으로 색칠된 파란색 칸의 개수를 구하면 된다.

## 3. 문제 해설 & 분류

가장 효과적으로 나눌 수 있는 것은 \\(1\times3\\)크기의 직사각형을 만든 후, 가장 중앙에 파란색을 칠하는 것이다.  

![image](https://user-images.githubusercontent.com/91870042/146893595-8a0696d0-9b0b-46a2-b11f-bcf2e156fd8e.png){: .align-center}


그러면, 먼저 주어진 직사각형을 \\(1\times3\\)크기의 직사각형으로 나누고 남은 것은 4종류 밖에 없다.

- 남은 칸이 없는 경우 : 파란색을 칠하지 않아도 된다.
- 남은 칸이 1칸인 경우\\((1\times1)\\) : 파란색을 1칸 칠해야 한다.
- 남은 칸이 2칸인 경우\\((2\times1)\\) : 파란색을 1칸 칠해야 한다.
- 남은 칸이 4칸인 경우\\((2\times2)\\) : 파란색을 2칸 칠해야 한다.

![image](https://user-images.githubusercontent.com/91870042/146893641-819f5ddf-182d-4036-9b87-dc9792c3a314.png){: .align-center}

![image](https://user-images.githubusercontent.com/91870042/146893661-df30d78a-11cb-44e3-a0ea-b5f2c6a1f3ea.png){: .align-center}

분류: `greedy`

## 4. 정답 코드

```cpp
#include <bits/stdc++.h>
#define ll long long
#define pii pair<int, int>
#define MOD 1'000'000'007

using namespace std;

int testcase;
int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);

    cin >> testcase;
    while (testcase--) {
        int n, m; cin >> n >> m;
        if (n > m) swap(n, m);

        int width = (m / 3) * n;
        m %= 3;

        int height = (n / 3) * m;
        n %= 3;

        int r = 0;
        if (n * m == 0) {
            r = 0;
        } else if (n * m == 1) {
            r = 1;
        } else if (n * m == 2) {
            r = 1;
        } else if (n * m == 4) {
            r = 2;
        }
        cout << width + height + r << "\n";
    }

    return 0;
}
```

<br>

# <span style = "color: #00adb5">C. Two Arrays</span>

## 1. 문제 출처

[Problem - C - Codeforces](https://codeforces.com/contest/1589/problem/C) `*900`

## 2. 문제 설명

길이가 \\(n\\)인 2개의 배열 \\(a, b\\)가 주어진다. \\(a\\)의 배열을 가지고 특정한 연산을 수행하여 배열 \\(b\\)와 동일하게 만들고 싶다. \\(a\\)에서 할 수 있는 연산은 \\(0\le k\le n\\)인 \\(k\\)를 선택하고, 배열\\(a\\)에서 \\(k\\)개의 수를 선택한 다음, 각 수에 1을 더한다. 이 \\(k\\)개의 더한 결과들은 다시 배열\\(a\\)에 어디든지 삽입될 수 있다.

그럴 때, 단 1번의 연산 수행으로 배열\\(a\\)와 배열\\(b\\)가 동일해질 수 있는지 출력하면 되는 문제이다.

## 3. 문제 해설 & 분류

두 배열 \\(a, b\\)를 정렬한 후, 각 인덱스에 대한 차이가 0 또는 1인지만 확인하면 된다.

분류: `brute force` `greedy` `math` `sortings`


## 4. 정답 코드

```cpp
#include <bits/stdc++.h>
#define ll long long
#define pii pair<int, int>
#define MOD 1'000'000'007

using namespace std;

int testcase;
int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);

    cin >> testcase;
    while (testcase--) {
        int n; cin >> n;
        vector<int> a(n), b(n);
        for (auto &e : a) cin >> e;
        for (auto &e : b) cin >> e;

        sort(a.begin(), a.end());
        sort(b.begin(), b.end());

        vector<int> diff(n);
        for (int i = 0; i < n; ++i)
            diff[i] = b[i] - a[i];
        
        bool flag = true;
        for (int i = 0; i < n && flag; ++i) {
            if (diff[i] != 0 && diff[i] != 1)
                flag = false;
        }

        if (flag) cout << "YES\n";
        else cout << "NO\n";

    }

    return 0;
}
```