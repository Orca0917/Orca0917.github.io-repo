---
title:  "Codeforces Round #754 (Div.2)"
excerpt: "A, B, C solved, D upsolving"

categories:
  - codeforces-contest
tags:
  - [C++, 알고리즘, Div2]

toc: true
toc_sticky: true
 
date: 2021-12-03
last_modified_at: 2021-12-03
---

<br>

# <span style = "color: #00adb5">A. A.M. Deviation</span>

## 1. 문제 출처

[Problem - A - Codeforces](https://codeforces.com/contest/1605/problem/A) `*800`

## 2. 문제 설명

문제에서 3개의 수 \\(a_{1}, a_{2},a_{3}\\)이 주어졌을 때, 어떠한 연산을 수행해서 등차수열을 이루도록 만들고 싶다. 결과적으로 목표는 어떠한 연산을 수행했을 때, \\(2a_{2}=a_{1}+a_{3}\\)을 만족하면 된다. 어떠한 연산과정은 다음과 같다. 서로 다른 2개의 수를 선택하여 한쪽에는 +1을 다른 한쪽에는 -1을 한다.

입력에 대한 출력은, 해당 연산을 수행해서 \\(\|2a_{2}-a_{1}-a_{3}\|\\)가 최소가 되는 값을 출력하면 된다.

## 3. 문제 해설 & 분류

먼저, 처음으로 입력된 3개의 수가 이미 등차수열을 이루지 않은 상태임을 가정한다. 이미 등차수열을 이루었다면, 정답으로 0을 출력한다.

연산을 수행하기 위해서 2개의 수를 선택하는데, \\(a_{1}, a_{3}\\)를 선택했다고 하자. 결과적으로 2개의 수를 더한 값을 빼는것인데, 이렇게 \\(a_{1},a_{3}\\)을 선택하는 것은 의미가 없다. 따라서 우리는 \\(a_{1},a_2{}\\)를 선택해야함을 알 수 있다. 그러면 다음 2개의 과정으로 나눌 수 있다.

1. \\(a_{1}\\)을 1증가시키고, \\(a_{2}\\)를 1감소시키는 방법 : 결과적으로 \\(a_1+a_3-2a_2\\)의 값이 3 증가한다.
2. \\(a_{1}\\)을 1감소시키고, \\(a_2\\)를 1증가시키는 방법 : 결과적으로 \\(a_1+a_3-2a_2\\)의 값이 3 감소한다.

이것을 통해, 우리는 어떠한 연산을 수행하던지 간에 3의 배수만큼 결과값을 증가하거나 감소시킬 수 있는 것이며, \\(a_1+a_3-2a_2\\)를 3으로 나눈 나머지 값(modulo 3)은 절대 변하지 않음을 알 수 있다.
- \\(a_1 + a_3 - 2a_2 \equiv0 \,mod\,3 : \|2a_{2}-a_{1}-a_{3}\|=0=0\\)
- \\(a_1+a_3-2a_2\equiv1\,\,mod\,\,3 : \|2a_{2}-a_{1}-a_{3}\|=\|1\|=1\\)
- \\(a_1+a_3-2a_2\equiv2\,\,mod\,\,3 : \|2a_{2}-a_{1}-a_{3}\|=\|2-3\|=1\\) // 원래의 결과에서 3을 빼서 더 적게 만든다.

시간복잡도: \\(O(1)\\)

분류: `math` `number theory`

## 4. 정답 코드

```cpp
#include <bits/stdc++.h>
using namespace std;
 
int32_t main()
{
    ios_base::sync_with_stdio(0); 
    cin.tie(0); cout.tie(0);
    int T; cin >> T;
    while(T--)
    {
        int a, b, c; cin >> a >> b >> c;
        cout << ((a + c - 2 * b) % 3 == 0 ? 0 : 1) << "\n"; 
    }        
    return 0;
}
```
<br>
# <span style = "color: #00adb5">B. Reverse Sort</span>

## 1. 문제 출처

[Problem - B - Codeforces](https://codeforces.com/contest/1605/problem/B) `*1000`

## 2. 문제 설명

길이가 \\(n\\)인 0과 1로 이루어진 문자열 \\(s\\)가 있다. 그리고 정렬을 통해서 문자열 내의 문자들이 감소하지 않게 만들고 싶다. 이 정렬을 하기 위해서 수행하는 과정은 다음과 같다.

1. 증가하지 않는 원소들을 원하는 만큼 선택한다. 수식으로 표현하면, \\(1\le k \le n\\)을 만족하는 \\(k\\)를 선택하고, \\(s_{i_1}\ge s_{i_2}\ge \dots\ge s_{i_k}\\) 이면서, \\(1\le i_1 <i_2<\dots<i_k\le n\\)을 만족하는 \\(k\\)개의 인덱스를 선택한다.
2. 선택한 원소들을 좌우로 반전시켜서 다시 제자리에 넣는다. 수식으로, \\(s_{i_1}\\)과 \\(s_{i_k}\\)을 바꾸고, \\(s_{i_2}\\)과 \\(s_{i_k-1}\\)을 바꾸고, \\(\dots\\) \\(s_{i_{\lfloor k/2\rfloor}}\\)와, \\(s_{i_{\lceil k/2\rceil + 1}}\\)을 바꾼다.

최소한의 연산을 수행해서 모든 원소를 정렬시키고 싶다면, 몇 번의 연산을 수행해야 하고 몇 번째 원소를 바꿔야 하는지 각 연산 횟수에 맞춰서 출력하는 문제이다.

## 3. 문제 해설 & 분류

0과 1로 이루어진 문자열을 정렬하기 위해서 최대 1번의 연산을 수행하면 모든 문자들을 정렬할 수 있다. 문자열\\(s\\)에 포함된 0의 개수를 \\(cnt_0\\)이라고 하고, 1의 개수를 \\(cnt_1\\)이라고 하자. 최종적으로 정렬된 문자열의 결과는 \\(cnt_0\\)개 만큼 0으로 채워지고, 뒤로는 나머지 \\(cnt_1\\)의 개수만큼 1로 채워진다. (감소하지 않는 순서로 정렬되기 때문)

문자열\\(s\\)에서 처음 \\(cnt_0\\)개의 문자 중 1이 등장하는 횟수는 문자열\\(s\\)에서 뒤에서 \\(cnt_1\\)개의 문자 중 0이 등장하는 횟수와 동일하다. 문자열이 이미 정렬되어 있지 않다면, 처음 \\(cnt_0\\)개에서 1이 등장하는 횟수 * 2의 길이만큼 바꾸어 주면 된다.

시간복잡도: \\(O(n)\\)

분류: `그리디 알고리즘` `정렬`

## 4. 정답 코드

```cpp
#include <bits/stdc++.h>
using namespace std;
 
int32_t main()
{
    ios_base::sync_with_stdio(0); 
    cin.tie(0); cout.tie(0);
    int T; cin >> T;
    while(T--)
    {
        int n; cin >> n;
        string s; cin >> s;
        if(is_sorted(s.begin(), s.end()))
        {
            cout << 0 << "\n";
            continue;
        }
        
        string t = s;
        sort(t.begin(), t.end());
        cout << 1 << "\n";
        vector<int> ans;
        for(int i = 0; i < n; i++)
        {
            if(s[i] != t[i])
                ans.push_back(i+1);
        }
        cout << ans.size() << " ";
        for(int i = 0; i < ans.size(); i++)
            cout << ans[i] << " \n"[i+1 == ans.size()];
    }        
    return 0;
}
```
<br>
# <span style = "color: #00adb5">C. Minimum Extraction</span>

## 1. 문제 출처

[Problem - C - Codeforces](https://codeforces.com/contest/1605/problem/C) `*1400`

## 2. 문제 설명

\\(a,b,c\\)로 이루어진 길이\\(n\\)의 문자열 \\(s\\)가 있다. 이 문자열에서 다음 조건을 모두 만족하는 가장 짧은 길이의 부분 문자열을 찾고 싶다. 여기서 말하는 부분 문자열은 원 문자열 \\(s\\)에서 앞 또는 뒤의 원소를 제거하여 만든 문자열이다.

- 부분 문자열의 길이는 최소 2이어야 한다.
- \\(a\\)가 등장하는 횟수는 \\(b\\)가 등장하는 횟수보다 많아야 한다.
- \\(a\\)가 등장하는 횟수는 \\(c\\)가 등장하는 횟수보다 많아야 한다.

출력 결과로 위의 조건들을 모두 만족시키는 가장 짧은 부분 분자열의 길이를 출력한다.

## 3. 문제 해설 & 분류

처음 문제를 풀었을 때는, 가능한 부분 문자열을 찾아서 KMP알고리즘을 사용해 해당 문자열이 존재하는지 찾아보았다. 가능한 부분 문자열은 `aa` `aba` `aca` `abca` `acba` `abbacca` `accabba` 로 많지 않았기에 가능했다.

정답으로 올라온 코드는 문자열을 순회하면서 확인하는 코드인데, KMP탐색이 더 빠르기에 따로 해설을 하지는 않는다.

분류: `brute force` `greedy` `implementation` `strings` `kmp search`


## 4. 정답 코드

```cpp
#include <bits/stdc++.h>
#define INF 1234567890
#define pii pair<int, int>
#define ll long long
#define MOD 1'000'000'007
 
using namespace std;
 
vector<int> getPI(const string &needle) {
    int n = needle.length();
    vector<int> pi(n, 0);
    int begin = 1, matched = 0;
    while (begin + matched < n) {
        if (needle[begin + matched] == needle[matched]) {
            ++matched;
            pi[begin + matched - 1] = matched;
        } else {
            if (matched == 0) ++begin;
            else {
                begin += matched - pi[matched - 1];
                matched = pi[matched - 1];
            }
        }
    }
    return pi;
}
 
bool kmpSearch(const string &hayStack, const string &needle) {
    int n = hayStack.length(), m = needle.length();
    vector<int> pi = getPI(needle);
 
    int begin = 0, matched = 0;
    while (begin <= n - m) {
        if (matched < m && hayStack[begin + matched] == needle[matched]) {
            ++matched;
            if (matched == m) {
                return true;
            }
        } else {
            if (matched == 0) ++begin;
            else {
                begin += matched - pi[matched - 1];
                matched = pi[matched - 1];
            }
        }
    }
 
    return false;
}
 
int testcase;
int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    cout.tie(nullptr);
 
    cin >> testcase;
    while (testcase--) {
        int n; cin >> n;
        string s; cin >> s;
        vector<string> candidate;
        candidate.push_back("aa");
        candidate.push_back("aba");
        candidate.push_back("aca");
        candidate.push_back("abca");
        candidate.push_back("acba");
        candidate.push_back("abbacca");
        candidate.push_back("accabba");
        int answer = -1;
        for (int i = 0; i < 7; ++i) {
            if (kmpSearch(s, candidate[i])) {
                answer = candidate[i].length();
                break;
            }
        }
        cout << answer << "\n";
    }
 
    return 0;
}
```