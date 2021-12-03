---
title:  "Codeforces Round #752 (Div.2)"
excerpt: "A, B, C solved, D upsolving"

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

# <span style = "color: #00adb5">A. Era</span>

## 1. 문제 출처

[Problem - A - Codeforces](https://codeforces.com/contest/1604/problem/A) `*800`

## 2. 문제 설명

길이가 \\(n\\)인 수열이 주어진다. 이 수열에 대해서 특정한 연산을 수행할 수 있는데, 이 연산은 어떠한 수 \\(k\\)를 지정하여 수열의 맨앞과 맨뒤를 포함한 어느 위치에서 \\(k\\)를 삽입하는 것이다. 최종적으로 구하고 싶은 것은 \\(arr[i]\le i\\)를 만족하게 하기 위해서 몇 번의 연산을 수행하는지를 구하는 문제이다.

## 3. 문제 해설 & 분류

각 원소에 대해서 \\(arr[i] >i\\)를 만족한다면, 앞에 원소가 \\(arr[i]-i\\)개가 필요하다는 의미이다. 따라서 \\(max(res, arr[i]-i)\\)를 구하면 된다.

분류: `greedy`

## 4. 정답 코드

```cpp
#include <bits/stdc++.h>
using namespace std;

int testcase, n, arr[101];
int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);

    cin >> testcase;
    while (testcase--) {
        cin >> n;
        int M = 0, result = 0;
        for (int i = 1; i <= n; ++i) {
            cin >> arr[i];
            result = max(result, arr[i] - i);
        }

        cout << result << "\n";
    }
    return 0;
}
```
<br>

# <span style = "color: #00adb5">B. XOR Specia-LIS-t</span>

## 1. 문제 출처

[Problem - B - Codeforces](https://codeforces.com/contest/1604/problem/B) `*1100`

## 2. 문제 설명

길이가 \\(n\\)인 수열이 주어진다. 이 수열을 \\(k\\)개의 부분 수열로 나누고, 각 부분 수열의 *LIS(Longest Increasing Subsequence)* 를 구하여 \\(h_{i}\\) \\((0<i<k)\\)에 저장한다. 다시, 각각의 \\(h_{i}\\)에 대해서 `XOR` 연산을 수행한 결과가 0이 되게 하고 싶다. 문제는 어떠한 수열이 주어졌을 때, 그 수열을 위와 같은 연산을 통해 *XOR*값이 0이 될 수 있는지 가능성을 판단하는 문제이다.

## 3. 문제 해설 & 분류

먼저 길이가 \\(n\\)인 수열을 길이가 1인 부분수열 \\(n\\)개로 나눈다. 길이가 1인 수열의 *LIS*값은 1이기 때문에, \\(n\\)의 값이 짝수라면 항상 0을 만들 수 있게 된다. 이제 남은 홀수에 대해서 판단만 하면된다.

\\(n\\)이 홀수라면, **어떠한 두 수를 묶어서 하나로** 보게 되면 전체의 개수는 짝수라고 볼 수 있다. 이에, 특정한 두 개의 수를 부분 수열로 묶어서 봐야한다. 그 2개의 특정한 수는 *LIS*값이 동일하게 1인, 그런 수를 찾아야 한다. **어떠한 2개의 수의 *LIS*가 1이기 위해서는 두 수중 앞에 있는 수가 더 크거나 같으면 된다. 그런 연속된 두 수가 원래의 수열에 존재한다면 원하는 0을 만들 수 있다.**

이제 남은 경우의 수는 하나, \\(n\\)이 홀수이고, 모든 수는 증가하는 수열인 경우이다. 해당 수열을 먼저 적절히 나누어서 0을 만들 수 있다고 **가정**하자. 해당 수열을 특정한 묶음으로 나누었을 때, 결국의 그 수열에 포함된 원소의 개수가 LIS의 길이와 동일하다. 그 길이가 X라고 했을 때, 결국에는 XOR결과가 0이 되기 위해서는 **길이가 X인 부분수열이 하나 더 필요**하다. **결국 수열의 원소의 수가 짝수**이다는 것인데, 이것은 처음에 가정된 것과 **모순**이 된다. 따라서 이번 경우에는 0을 만들 수 없다.

## 4. 정답 코드

```cpp
#include <bits/stdc++.h>

using namespace std;
int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);
    int testcase; cin >> testcase;
    while (testcase--) {
        int n; cin >> n;
        vector<int> arr(n);
        bool hasDec = false;
        int bef = 0;
        for (int i = 0; i < n; ++i) {
            cin >> arr[i];
            if (arr[i] <= bef) hasDec = true;
            bef = arr[i];
        }
        if (n % 2 == 0) cout << "YES\n";
        else {
            if (hasDec) cout << "YES\n";
            else cout << "NO\n";
        }
    }
    return 0;
}
```
<br>

# <span style = "color: #00adb5">C. Di-visible Confusion</span>

## 1. 문제 출처

[Problem - C - Codeforces](https://codeforces.com/contest/1604/problem/C) `*1300`

## 2. 문제 설명

길이가 \\(n\\)인 수열이 주어진다. 이 수열을 특정한 연산을 수행하여 모든 원소를 제거하는 것이 목표이다. 특정한 연산은\\(arr[i]\\)를 \\(i+1\\)로 나눈 나머지가 0이 아니라면 그 원소를 삭제한다. 해당 원소를 삭제하게 되면, 뒤에 있는 원소들은 앞으로 한 칸씩 당겨지게 된다. 이런식으로 특정한 연산을 수행하여 모든 원소를 제거할 수 있는지의 여부를 출력하면 되는 문제이다.

## 3. 문제 해설 & 분류

어떠한 수\\(arr[i]\\)가 지워졌을 때 영향이 있는 수는 배열\\(arr\\)에서 구간\\([i+1, n]\\)이다. 또한\\(arr[i]\\)에 대해서 나누어 질 수 있는 수는 \\([2, i+1]\\)이다. 이 점을 이용해서 각 수에 대해 그수에 맞는 구간 중 나눈 나머지가 0이 아닌것이 모두 최소 하나라도 있으면 모든 원소를 제거할 수 있음을 알게된다.

만약,\\(arr[i]\\)가 구간\\([2, i +1]\\)사이에 수 중 하나라도 나누어 떨어지지 않는다면, 모든 수 \\(i\\)에 대해서 \\(1\\)부터 \\(n\\)까지 검사를 하면 된다. 하지만, 만약\\(arr[i]\\)가 구간 \\([2, i+1]\\)의 모든 수로 나누어 떨어진다면,\\(arr[i]\\)는 \\(LCM(2, 3, \dots,i+1)\\)로 나누어떨어지는 것과 동일하다. 만약 \\(i=22\\)이라면, 그때의 \\(LCM\\)값은 \\(10^9\\)를 넘어가게 된다. 따라서 나누어 떨어지는지의 여부를 판단하는 과정에서 우리는 최대 22번만큼만 검사하게 된다.

시간복잡도: \\(O(n+21^2)\\)

분류: `brute force` `greedy` `math` `number theory`

## 4. 정답 코드

```cpp
#include <bits/stdc++.h>

using namespace std;

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);

    int testcase; cin >> testcase;
    while (testcase--) {
        int n; cin >> n;
        vector<int> arr(n + 1);
        bool result = true;
        for (int i = 1; i <= n; ++i) cin >> arr[i];
        for (int i = 1; i <= n; ++i) {
            bool flag = false;
            for (int j = 2; j <= i + 1 && !flag; ++j) // 22번넘게 검사X
                if (arr[i] % j) flag = true;
            result &= flag;
        }

        if (result) cout << "YES\n";
        else cout << "NO\n";
    }

    return 0;
}
```
<br>

# <span style = "color: #00adb5">D. Moderate Modular Mode</span>

## 1. 문제 출처

[Problem - D - Codeforces](https://codeforces.com/contest/1604/problem/D) `*1600`

## 2. 문제 설명

짝수인 어떤 양의 정수 \\(x, y\\)가 주어진다. 그럴 때, \\(n % x = y % n\\)을 만족하는 1이상 \\(2\times10^{18}\\)이하의 수 \\(n\\)을 찾는 문제이다.

## 3. 문제 해설 & 분류

먼저 \\(x > y\\)일 때는 \\(n\\)값이 \\(x+y\\)와 같다. 반대로 \\(x \le y\\)인 경우를 찾아봐야 한다.

- **가설1. \\(n\\)은 \\(x\\)보다 작지 않다.**
    - 증명: \\(n < x\\)라고 한다면, \\(n \,mod\, x = n\\)일 것이다. 하지만, \\(y \,mod\, n\\)의 값은 \\(n\\)보다 작아지게 되어 우리가 원하는 답을 찾을 수 없다.
- **가설2. \\(n\\)은 \\(y\\)보다 크지 않다.**
    - 증명: \\(n > y\\)라고 한다면, \\(n \,mod\, x < x\\)일 것이다. 하지만, \\(y \,mod\, n\\)의 값은 \\(y\\)와 동일하다. 위에서 \\(x \le y\\)인 경우로 제한하고 있기 때문에 모순이다.

수평선 위에 그림을 그린다고 생각해보자. 원점(0)에서 시작해서 \\(x\\)만큼 점프해 나가되, \\(y\\)를 넘지않는 마지막 지점까지 점프하자. 그때의 위치를 \\(p\\)라고 하면, \\(p = y - y \,mod\, x\\)로 나타낼 수 있다. 따라서 \\(y\\)와 \\(p\\)사이의 중간 지점 + \\(x\\)를 \\(n\\)으로 설정하면 우리가 구하고자 하는 값을 찾을 수 있게 된다.
> 입력으로 주어지는 두 수(\\(x, y\\))가 짝수이므로 \\(p\\)의 값은 반드시 짝수일 수 밖에 없다.

![image](https://user-images.githubusercontent.com/91870042/144611447-3280e5ab-f567-467b-88c5-1e3285faa21d.png)

출처: https://codeforces.com/blog/entry/96460

분류: `math` `number theory`

## 4. 정답 코드

```cpp
#include <bits/stdc++.h>
using namespace std;
int main() {
    ios_base::sync_with_stdio(false); cin.tie(0);
    int testcase; cin >> testcase;
    while (testcase--) {
        int x, y; cin >> x >> y;
        if (x > y) {
            cout << x + y << "\n";
        } else {
            cout << y - (y % x) / 2 << "\n";
        }
    }
    return 0;
}
```