---
title:  "Codeforces Round #753 (Div.3)"
excerpt: "A, B solved, C, D, E upsolving"

categories:
  - codeforces-contest
tags:
  - [C++, Algorithm, Div3]

toc: true
toc_sticky: true
 
date: 2021-12-03
last_modified_at: 2021-12-03
---

<br>

# <span style = "color: #00adb5">A. Linear Keyboard</span>

## 1. 문제 출처

[Problem - A - Codeforces](https://codeforces.com/contest/1607/problem/A) `*800`

## 2. 문제 설명

한 줄에 26개의 키로 이루어진 키보드가 입력으로 주어진다. 어떤 소문자로 이루어진 단어 \\(S\\)를 입력하기 위해 얼마나 손을 움직여야 하는지 알고 싶다. 단어를 키보드로 입력할 때는 맨 앞글자 부터 차례대로 입력을 진행한다.

## 3. 문제 해설 & 분류

주어지는 글자 사이의 간격만 계산하여 출력하면 된다. 키보드의 배열은 입력으로 주어져서 abc..와 같은 알파벳 철자순이 아닐 수 있기 때문에 `map`을 사용하여 각 글자가 몇 번째에 위치한지 기록하게 하여 해결할 수 있다.

분류: `implementation` `strings`

## 4. 정답 코드

```cpp
#include <bits/stdc++.h>
using namespace std;
map<char, int> m;
int testcase;
int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);
 
    cin >> testcase;
    while (testcase--) {
        m.clear();
        string s; cin >> s;
        for (int i = 0; i < s.length(); ++i) m.insert({s[i], i});
        string targ; cin >> targ;
 
        long long answer = 0;
        for (int i = 1; i < targ.length(); ++i) {
            answer += abs(m[targ[i]] - m[targ[i - 1]]);
        }
 
        cout << answer << "\n";
    }
 
    return 0;
}
```
<br>

# <span style = "color: #00adb5">B. Odd Grasshopper</span>

## 1. 문제 출처

[Problem - B - Codeforces](https://codeforces.com/contest/1607/problem/B) `*900`

## 2. 문제 설명

어떤 메뚜기가 있다. 이 메뚜기는 수평선 상으로만 움직일 수 있고, 왼쪽 또는 오른쪽으로 움직일 수 있다. 메뚜기가 이동하는 거리는 뛰기 시작한 시간만큼 이동한다. 예를 들어, 1초 뒤에 1만큼 움직일 수 있다면, 2초 뒤에는 2만큼, 3초뒤에는 3... 만큼 움직일 수 있다. 왼쪽으로 이동하기 위해서는 현재 위치의 좌표가 짝수이어야 하고, 오른쪽으로 이동하려면 홀수 이어야한다. 메뚜기의 시작위치와 지난 시간t을 입력받았을 때, \\(t\\)초 뒤의 메뚜기의 위치를 구하는 문제이다.

## 3. 문제 해설 & 분류

t초뒤의 메뚜기의 위치를 \\(G_{t}\\)라고 하면, 위의 조건을 수식으로 표현하면 다음과 같이 표현할 수 있다.

$$G_{t} = \left\{\begin{matrix}G_{t-1} + t \,\,\, (G_{t-1}\,mod\,2 = 1) & \\ G_{t-1} - t \,\,\, (G_{t-1}\,mod\,2 = 0) &  \end{matrix}\right.$$

위의 수식에 대해 시작 위치가 짝수 일때와 홀수 일때로 구분하여 쭉 나열하면 다음 표가 나온다.

![image](https://user-images.githubusercontent.com/91870042/144617696-e4437b58-9fbf-4b2c-8f13-b0fc4079be62.png)


표에 적힌 각 수는 처음 좌표가 0, 1이었을 때 최종 도달 지점이다. 자세히 살펴보면 4개씩 끊어서 규칙이 있음을 확인할 수 있다. 규칙도 짝수와 홀수로 나누어서 살펴보면 다음과 같다

> 먼저 `짝수` 인 경우이다. 지난 시간을 4로 나눈 나머지의 값이 0, 1, 2, 3일 때 변화량은 다음과 같이 표시할 수 있다. `mod == 0` : 처음 위치 그대로, `mod == 1` : 처음위치에서 -\\(t\\)만큼 이동, `mod == 2` : 처음 위치 +1로 이동, `mod == 3` : 처음 위치에서 \\(t+1\\)만큼 추가로 이동.
> 

> 그 다음 `홀수` 인 경우이다. 마찬가지로 지난 시간을 4로 나눈 나머지로 이동한 결과를 관찰한다. `mod == 0` : 시작 위치 그대로, `mod == 1` : 시작 위치에서 +\\(t\\)만큼 이동, `mod == 2` : 시작위치에서 -1만큼 이동, `mod == 3` : 시작위치에서 \\(-(t+1)\\)만큼 이동.
> 

분류: `math`

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
        long long start, times; cin >> start >> times;
        
        if (start % 2 == 0) {
            if (times <= 1) start -= times;
            else {
                int mod = (times - 2) % 4;
                if (mod == 0) start += 1;
                else if (mod == 1) start += (times + 1);
                else if (mod == 3) start -= times;
            }
        } else {
            int mod = times % 4;
            if (mod == 1) start += times;
            else if (mod == 2) start -= 1;
            else if (mod == 3) start -= (times + 1);
        }
        cout << start << "\n";
    }

    return 0;
}
```
<br>

# <span style = "color: #00adb5">C. Minimum Extraction</span>

## 1. 문제 출처

[Problem - C - Codeforces](https://codeforces.com/contest/1607/problem/C) `*1000`

## 2. 문제 설명

길이\\(n\\)의 배열 \\(a\\)가 있다. 이 배열에서 특정한 연산을 하여 남아 있는 원소 중 그 값이 제일 작은 것의 값을 최대화 하는것이 이 문제의 목표이다. 여기서 할 수 있는 연산은, 원소의 가장 작은 원소 \\(m\\)을 선택하고, 배열에서 \\(m\\)을 제거한다. 이후, 나머지 원소에 대해서 모두 \\(m\\)만큼을 뺀다. 이런식으로 연산을 원하는 횟수만큼 실행하여 남아있는 값의 최소값을 최대화하면 된다. 문제에서 배열의 크기와 배열의 원소가 주어지면 그 값을 출력하면 된다.

## 3. 문제 해설 & 분류

배열에서 가장 작은 원소를 순서대로 뽑아야 하기 때문에 가장 먼저 배열의 원소를 모두 정렬한다. 여기서 확인해야 하는 것은, 현재 어떤 원소 \\(m\\)을 뽑았다고 하면, 그 뒤의 모든 원소에 대해서도 동일한 연산을 하기 때문에 연속된 두 수 사이의 차이는 그대로 유지된다. 앞에서 얼마나 많이 원소가 뽑혀서 연산이 이루어졌던 간에, 내 바로 앞에 수도 동일한 연산을 받았기 때문에 최종적으로 어떤 원소의 결과 값은 내 원소의 값에서 바로 앞원소의 값을 뺀 것과 동일하다.

> 따라서 우리는 배열을 정렬하고, 그 차이 값이 가장 큰 것을 출력하면 되는 문제이다.
>

분류: `brute force` `sortings`

## 4. 정답 코드

```cpp
#include <bits/stdc++.h>

using namespace std;

int testcase;
vector<int> arr;

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);

    cin >> testcase;
    while (testcase--) {
        int n; cin >> n;
        arr.resize(n, 0);
        for (int i = 0; i < n; ++i) cin >> arr[i];
        sort(arr.begin(), arr.end());
        int answer = arr[0];
        for (int i = 1; i < n; ++i) answer = max(answer, arr[i] - arr[i - 1]);
        cout << answer << "\n";
    }

    return 0;
}
```
<br>

# <span style = "color: #00adb5">D. Blue-Red Permutation</span>

## 1. 문제 출처

[Problem - D - Codeforces](https://codeforces.com/contest/1607/problem/D) `*1300`

## 2. 문제 설명

길이\\(n\\)의 배열 \\(a\\)가 있을 때, 배열에 들어있는 숫자는 빨간색 또는 파란색으로 칠해져있다. 빨간색으로 칠해져있는 원소는 1만큼을 얼마든지 증가시킬 수 있고, 파란색으로 칠해져있는 원소는 1만큼을 얼마든지 감소시킬 수 있다. 이러한 연산을 수행하여서 결과적으로 배열에 있는 원소들이 1~\\(n\\)의 값을 이루도록 하게 할 수 있는지 출력하는 문제이다.

## 3. 문제 해설 & 분류

파란색 원소와 빨간색 원소를 담는 배열을 따로 두어서 먼저 정렬을 한다. 파란색 원소를 담은 배열은 모두 감소만 시킬 수 있다. 따라서 파란색 원소를 1부터 채운다고 생각하고 나머지 빨간색 원소들은 뒤의 숫자를 채운다고 생각하면 된다. 그렇기 때문에 정렬하면서, 파란색 원소를 채울 수 있는지, 빨간색 원소를 채울 수 있는지만 확인한다.

> 파란색 원소를 채울 수 없는 경우는 blue[i] < i인 경우이다. i번째로 작은 파란색 수로 i를 만들어야 하는데 그 값이 i보다 작아버리면 감소시키는 방법밖에 없어 만들 수 없기 때문이다.
> 

> 빨간색 원소를 채울 수 없는 경우는 red[i] > n - i - 1인 경우이다. 빨간색 원소는 큰수부터 작은 수로 정렬이 되어있는데, 빨간색에서 i번째로 큰수가 더 커버리다면 낮출 수 있는 방법이 없기 때문이다.
> 

분류: `greedy` `math` `sortings`

## 4. 정답 코드

```cpp
#include <bits/stdc++.h>

using namespace std;

vector<int> arr;
vector<int> red, blue;

int testcase;
int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);

    cin >> testcase;
    while (testcase--) {
        int n; cin >> n;
        arr.resize(n, 0);
        red.clear();
        blue.clear();

        for (int i = 0; i < n; ++i) cin >> arr[i];
        for (int i = 0; i < n; ++i) {
            char c; cin >> c;
            if (c == 'R')
                red.push_back(arr[i]);
            else
                blue.push_back(arr[i]);
        }

        sort(red.rbegin(), red.rend());
        sort(blue.begin(), blue.end());

        bool flag = false;
        for (int i = 0; i < red.size() && !flag; ++i) {
            if (red[i] > n - i) flag = true;
        }

        for (int i = 0; i < blue.size() && !flag; ++i) {
            if (blue[i] < i + 1) flag = true;
        }

        if (flag) cout << "NO\n";
        else cout << "YES\n";
    }
    return 0;
}
```
<br>

# <span style = "color: #00adb5">E. Robot on the Board 1</span>

## 1. 문제 출처

[Problem - E - Codeforces](https://codeforces.com/contest/1607/problem/E) `*1600`

## 2. 문제 설명

세로로 길이\\(n\\), 가로로 길이\\(m\\)만큼의 보드판이 있다. 이 보드판 위에서 로봇이 움직이고 로봇이 움직일 수 있는 방향은 "상, 하, 좌, 우"로 총 4가지가 있다. 각 명령은 U, D, L, R로 주어진다. 보드판의 크기와 로봇이 움직이는 명령이 주어졌을 때, 로봇이 최대한 오랬동안 보드판 위에서 움직일 수 있도록 하는 로봇의 최초 위치를 구하여라. 로봇은 보드판에서 벗어났을 때 더이상 움직이는 것이 불가능하다.

## 3. 문제 해설 & 분류

먼저 로봇이 보드판을 벗어나는 경우를 살펴본다. 보드판을 벗어나는 경우는 로봇이 이동한 최대 가로길이 또는 최대 세로길이가 판의 크기를 벗어난 경우이다. 그렇다면 가로와 세로 각각 로봇이 이동할 수 있는 최대 지점을 계산한다. 이를 다시 정리하면 다음과 같이 나타낼 수 있다.

> 로봇이 이동할 수 있는 최대 **가로** 길이 = (로봇이 갔던 최대 지점)-(로봇이 갔던 최소 지점) + 1
> 

> 로봇이 이동할 수 있는 최대 **세로** 길이 = (로봇이 갔던 최대 지점)-(로봇이 갔던 최소 지점) + 1
> 

위에서 계산한 길이가 각각 \\(m, n\\)을 넘어서면 보드판을 넘어갔다고 판단할 수 있다. 이어서 로봇이 시작한 지점을 구하기 위하는 방법을 살펴봐야 한다. 로봇이 위로 이동할 수 있었던 최대 길이와 로봇이 왼쪽으로 이동할 수 있었던 최대 길이를 선택하여 출력하면된다. 그렇게 해야 딱 맞춰서 보드판 내부를 돌아다닐 수 있기 때문이다.

- `로봇의 시작_세로` = `로봇이 위로 이동한 최대 거리` + 1

- `로봇의 시작_가로` = `로봇이 왼쪽으로 이동한 최대 거리` + 1

여기서 1씩 더해준 이유는 보드판의 좌측상단 시작점이 (1, 1)이기 때문이다.

분류: `implementation`

## 4. 정답 코드

```cpp
#include <bits/stdc++.h>
using namespace std;

int testcase, n, m;

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);

    cin >> testcase;
    while (testcase--) {
        cin >> n >> m;
        int height = 0, width = 0;
        int maxH = 0, minH = 0, maxW = 0, minW = 0;
        int ansH = 1, ansW = 1;

        string cmd; cin >> cmd;
        for (char c : cmd) {
            if (c == 'U') {
                --height;
                minH = min(minH, height);               
            } else if (c == 'D') {
                ++height;
                maxH = max(maxH, height);
            } else if (c == 'L') {
                --width;
                minW = min(minW, width);
            } else if (c == 'R') {
                ++width;
                maxW = max(maxW, width);
            }
            
            if (maxH - minH + 1 > n || maxW - minW + 1 > m) break;

            ansH = abs(minH) + 1;
            ansW = abs(minW) + 1;
        }

        cout << ansH << " " << ansW << "\n";
    }
    return 0;
}
```