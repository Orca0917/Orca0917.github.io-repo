---
title:  "Codeforces Round #750 (Div.2)"
excerpt: "A, B, C, D 풀이"

categories:
  - codeforces
tags:
  - [C++, Algorithm, Div2]

toc: true
toc_sticky: true
 
date: 2021-12-03
last_modified_at: 2021-12-03
---

<br>

# <span style = "color: #00adb5">A. Luntik and Concerts</span>
## 1. 문제 출처

[Problem - A - Codeforces](https://codeforces.com/contest/1582/problem/A) `*800`

## 2. 문제 해석

1분 길이의 곡 a개, 2분 길이의 곡 b개, 3분 길이의 곡 c개가 있을 때, 곡들을 적절히 분배하여 2개의 묶음으로 만들어야 한다. 모든 노래는 묶음들에 포함되어 있어야 하며, 가능한 두 노래 묶음의 재생시간 차이를 최소화 해야 한다. 이때 노래 묶음의 최소 재생시간 차이를 구하는 문제이다.

## 3. 문제 해설 & 분류

전체 곡의 총 길이를 \\(s\\)라고 하였을 때, \\(s=a+2b+3c\\)이다. 문제 조건에서 \\(a,b,c≥1\\) 이라고 하였으므로 그리디 알고리즘에 의해서 모든 길이(최대 \\(s\\))의 묶음을 만들 수 있다. 따라서, \\(s\\)의 값이 홀수 이면 1, 짝수이면 0의 값을 출력하면 된다. 다시 말해, 같은 묶음으로 만들어야 하기 때문에 전체 재생시간에서 2로 나누었을 때, 나머지가 0이면 동일한 재생시간으로 나눌 수 있는 것이고, 1이라면 최소 차이가 1인 두 묶음으로 나눌 수 있는 것이다.

분류: `math`

## 4. 정답 코드

```cpp
#include <bits/stdc++.h>
#define ll long long
using namespace std;
 
int testcase;
 
int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);
 
    cin >> testcase;
    while (testcase--) {
        ll a, b, c; cin >> a >> b >> c;
        ll sum = a + b * 2 + c * 3;
        cout << sum % 2 << "\n";
    }
 
    return 0;
}
```
<br>

# <span style = "color: #00adb5">B. Luntik and Subsequences</span>

## 1. 문제 출처

[Problem - B - Codeforces](https://codeforces.com/contest/1582/problem/B) `*900`

## 2. 문제 해석

크기 \\(n\\)의 배열 \\(a\\)가 있을 때, \\(a\\)의 모든 원소의 합을 \\(s\\)라고 하며, \\(a\\)의 부분수열의 합이 \\(s-1\\)이 되었을 때 "nearly full"이라고 한다. 어떠한 크기 \\(n\\)의 배열 \\(a\\)가 주어졌을 때, "nearly full"이 되는 경우의 수를 구하는 문제이다.

## 3. 문제 해설 & 분류

총 합이 \\(s\\)일 때, 부분 수열의 합이 \\(s-1\\)로 만들기 위해서는 수열 \\(a\\)에서 1을 제외하는 경우만 있다. 또한 추가로 0이 존재한다면, 몇 개를 선택해도 결과에는 영향이 없다. 0의 개수를 \\(a\\), 1의 개수를 \\(b\\)라고 한다면 \\((a \times 2^{b})\\)를 계산하면 된다.

분류: `combinatorics` `math`

## 4. 정답 코드

```cpp
#include <bits/stdc++.h>
 
using namespace std;
 
int testcase;
int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);
 
    cin >> testcase;
    while (testcase--) {
        int n; cin >> n;
        long long zeroCnt = 0, oneCnt = 0;
        while (n--) {
            int k; cin >> k;
            if (k == 0) zeroCnt++;
            else if (k == 1) oneCnt++;
        }
 
        cout << (long long)pow(2, zeroCnt) * oneCnt << "\n";
    }
 
    return 0;
}
```

<br>

# <span style = "color: #00adb5">C. Grandma Capa Knits a Scart</span>

## 1. 문제 출처

[Problem - C - Codeforces](https://codeforces.com/contest/1582/problem/C) `*1200`

## 2. 문제 해석

길이가 \\(n\\)인 문자열 \\(s\\)가 주어졌을 때, \\(s\\)에서 최대 1개의 문자를 선택하여 해당 문자만 언제든지 삭제하여 문자열 \\(s\\)가 **[팰린드롬](https://ko.wikipedia.org/wiki/%ED%9A%8C%EB%AC%B8)**이 되게 만드는 문제이었다. 결과적으로 선택한 문자를 몇 번 삭제하였는지를 출력하면 된다. 팰린드롬을 생성할 수 없다면, -1을 출력한다.

## 3. 문제 해설 & 분류

문자열의 위치를 가리키는 인덱스 start, end를 두어서 start = 0, end = n - 1부터 시작합니다. 점점 거리를 좁혀오면서 다음 2가지 상황을 만날 수 있다.

- s[start] = s[end] : 팰린드롬에 만족하므로, start와 end의 인덱스를 각각 뒤로, 앞으로 이동한다.
- s[start] ≠ s[end] : 팰린드롬에 만족하지 못하여 둘 중 하나의 문자를 삭제해야 한다. 아직 어떤 문자를 삭제해야 팰린드롬이 완성되는지 알지 못하므로 두 경우 모두 확인을 진행한다.

위의 2가지 경우에 대해서는 `checkPalin()`함수에서 정의되어 있다.

`palin()`함수는 삭제할 수 있는 문자가 정해졌을 때, 팰린드롬인지를 확인하는 함수이다. 이 함수에서는 위와 같이 거리를 좁혀오면서 확인을 하지만 총 4가지의 상황을 만나게 된다.

- s[start] = s[end] : 팰린드롬에 만족하므로, start와 end의 인덱스를 각각 뒤로, 앞으로 이동한다.
- s[start] ≠ s[end] && s[start] = c : 팰린드롬은 아니지만, 문자를 삭제할 수 있으므로 삭제한다.
- s[start] ≠ s[end] && s[end] = c : 팰린드롬은 아니지만, 문자를 삭제할 수 있으므로 삭제한다.
- s[start] ≠ s[end] && s[start] ≠ c && s[end] ≠ c : 팰린드롬이 아니며, 문자를 삭제할 수도 없다. 더 이상의 탐색은 무의미하며 팰린드롬을 절대 만들 수 없다.

`palin()`함수의 반환값은 크게 2종류 이다.

1. **return -1; // 팰린드롬 생성에 실패**
2. **return ret; // 팰린드롬 생성에 성공하였으며, 문자를 삭제한 횟수를 반환**

ret1 = 최초로 일치하지 않는 시점에서 s[start]의 문자를 삭제하기로 하였을 때

ret2 = 최초로 일치하지 않는 시점에서 s[end]의 문자를 삭제하기로 하였을 때

- ret1 = -1 && ret2 = -1 : 두 경우 모두 팰린드롬을 생성하지 못한다.
- ret1 = -1 && ret2 ≠ -1 : s[start]문자를 삭제하기로 하였을 때는 팰린드롬 생성이 불가능하지만, s[end]문자를 선택하였을 때는 팰린드롬 생성에 성공하였다.
- ret1 ≠ -1 && ret2 = -1 : s[end]문자를 삭제하기로 하였을 때는 팰린드롬 생성이 불가능하지만, s[start]문자를 선택하였을 때는 팰린드롬 생성에 성공하였다.
- ret1 ≠ -1 && ret2 ≠ -1 : 두 경우 모두 팰린드롬 생성에 성공하였으므로, 더 적은 횟수를 반환한다.

분류: `brute force` `data structures` `greedy` `strings` `two pointers`

## 4. 정답 코드

```cpp
#include <bits/stdc++.h>
 
using namespace std;
 
int testcase, n;
string s;
 
int palin(int start, int end, char c) {
    int ret = 1;
    while (start < end) {
        if (s[start] == s[end]) {
            ++start;
            --end;
        } else {
            if (s[start] == c) {
                ++ret;
                ++start;
            } else if (s[end] == c) {
                ++ret;
                --end;
            } else {
                return -1;
            }
        }
    }
   
    return ret;
}
 
int checkPalin(int start, int end) {
    int ret = 0;
    while (start < end) {
        if (s[start] == s[end]) {
            ++start;
            --end;
        } else {
            int ret1 = palin(start + 1, end, s[start]);
            int ret2 = palin(start, end - 1, s[end]);
 
            if (ret1 < 0 && ret2 < 0) return -1;
            else if (ret1 < 0) return ret2;
            else if (ret2 < 0) return ret1;
            else return min(ret1, ret2);
        }
    }
 
    return ret;
}
 
 
int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);
 
    cin >> testcase;
    while (testcase--) {
        cin >> n >> s;
        cout << checkPalin(0, n - 1) << "\n";
    }
 
    return 0;
}
```
<br>

# <span style = "color: #00adb5">D. Vupsen, Pupsen and 0</span>

## 1. 문제 출처

[Problem - D - Codeforces](https://codeforces.com/contest/1582/problem/D) `*1600`

## 2. 문제 해석

길이 \\(n\\)의 수열 \\(a\\)가 주어지고, 각 원소는 0이 아닌 정수로 이루어져 있다. 또 다른 길이 \\(n\\)의 수열 \\(b\\)가 있다고 하고, 각 원소 역시 0이 아닌 정수로 이루어져 있어야 한다. 2개의 수열 \\(a\\)와 \\(b\\)가 있을 때, \\(\sum_{i=1}^{n}(a_{i}\times b_{i}) = 0\\)을 만족하는 수열 \\(b\\)를 구해야 하는 문제이다.

## 3. 문제 해설 & 분류

먼저 수열의 길이 \\(n\\)에 대해서 그 값이 짝수일 때와 홀수일 때로 구분할 수 있다. 짝수와 홀수로 구분하는 이유는 서로 인접한 수끼리 묶어서 0을 만들기 위함이다.

- \\(n\\)이 짝수인 경우
    
    길이가 2인 수열 \\(a = \{x,\ y\}\\)가 있다고 하면, 수열 \\(b\\)는  \\(b=\{y,\ -x\}\\) 과 같이 나타낼 수 있다. 각 인덱스에 맞추어 계산하면, \\(xy-xy=0\\)이 되기 때문이다. 이런식으로 계속 나아가게 되면 결국 총 합이 0을 만족시키는 수열 \\(b\\)를 만들 수 있다.
    
- \\(n\\)이 홀수인 경우
    
    \\(n\\)이 홀수일 때는 앞에 3개의 수에 대해서만 처리를 하면 나머지 \\(n-3\\)개의 수에 대해서는 \\(n\\)이 짝수인것과 같이 연산을 진행하면 된다.
    
    길이가 3인 수열 \\(a = \{x,\ y,\ z\}\\)가 있다고 하면 위와 비슷하게 진행한다. 하나의 원소를 다른 나머지 index에 넣고 하나만 계산을 진행하면 된다. \\(a\\)의 첫번째 원소 \\(x\\)를 다른 인덱스에 넣게 되면 \\(b = \{k,\ x, \ x\}\\)와 같이 나타낼 수 있다. 두 수열을 곱하였을 때 0이 되는 정보를 이용하면 \\(k\\)의 값이 \\(-(y+z)\\)가 되어야하는 것을 알게 된다. \\(b = \{-(y+z),\ x, \ x\}\\)로 다시 표현이 가능하다.
    
    하지만 문제에서 **수열 \\(b\\)의 모든 원소도 0이 되면 안된다**고 했기에, \\(y+z\\)의 값이 0이 아닌지만 확인하면 된다.
    
    - \\((x+y)\ != \ 0\\)
    - \\((x+z)\ != \ 0\\)
    - \\((y+z)\ != \ 0\\)

분류: `constructive algorithms` `math`

## 4. 정답 코드

```cpp
#include <iostream>
#include <vector>
#include <cmath>
#define ll long long
using namespace std;

int testcase, n;
vector<ll> arr;

void solution() {
    int idx = 0;
    if (n % 2) {
        if (arr[0] + arr[1] != 0) cout << -arr[2] << " " << -arr[2] << " " << arr[0] + arr[1] << " ";
        else if (arr[1] + arr[2] != 0) cout << arr[2] + arr[1] << " " << -arr[0] << " " << -arr[0] << " ";
        else cout << -arr[1] << " " << arr[0] + arr[2] << " " << -arr[1] << " ";
        idx = 3;
    }

    while (idx < n) {
        cout << -arr[idx + 1] << " " << arr[idx] << " ";
        idx += 2;
    }
    cout << "\n";
}

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);

    cin >> testcase;
    while (testcase--) {
        cin >> n;
        arr.resize(n);
        for (auto &e : arr) cin >> e;

        solution();
    }

    return 0;
}
```
