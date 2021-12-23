---
title:  "트라이 / Trie"
excerpt: "여러 문자열들을 저장하고, 효율적으로 탐색하기 위한 자료구조"

categories:
  - concept
tags:
  - [그래프, 문자열, 트라이]

toc: true
toc_sticky: true
 
date: 2021-12-19
last_modified_at: 2021-12-19
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **\<알고리즘 문제해결 전략2 - 구종만\>**의 26장 트라이 부분을 읽고 정리하는 글입니다.<br>
더 자세한 관련 문제와의 설명은 책에 더 상세하게 설명되어 있습니다.
{: .notice--info}

# <span style = "color: #00adb5">도입</span>
정수형 변수나 실수형 변수와 다르게, 문자열을 비교하는데는 최악의 경우 분자열의 길이에 비례하는 시간이 걸린다. \\(N\\)개의 원소를 갖는 이진트리에서 원하는 원소를 찾기 위해서는 \\(O(lgN)\\)번의 비교를 해서 찾아냈었다.

원소간의 비교를 상수시간에 할 수 있을 때는, 탐색의 시간복잡도가 \\(O(lgN)\\)이지만, 문자열은 그 길이에 비례하는 시간이 걸리므로, 최대길이 \\(M\\)을 곱하여 \\(O(MlgN)\\)이 최종 시간 복잡도이다. 이러한 문제를 해결하기 위해서 고안된 특화 자료구조인 `트라이`가 있다. 

`트라이`는 **문자열의 집합을 표현하는 트리 자료구조**로 집합 내에서 원하는 원소를 찾는 작업을 \\(O(M)\\)시간만에 해낼 수 있다.

<br>

# <span style = "color: #00adb5">트라이의 이해</span>

![image](https://user-images.githubusercontent.com/91870042/146554534-d794356a-6e3b-42fe-b892-5b7b6df556e0.png){: .align-center}

위의 그림은 트라이의 한 예제를 보여준다. 그림과 같이 트라이는 *집합에 포함된 문자열의 접두사들에 대응되는 노드들이 서로 연결된 트리* 이다. 두 노드를 연결하는 **간선은 덧붙인 글자에 해당**이 된다. 트라이의 루트는 항상 길이 0인 문자열(빈 문자열)에 대응된다. 

트라이의 중요한 속성은 루트에서 한 노드까지 내려가는 경로에서 만나는 글자들을 모으면 해당 노드에 대응되는 접두사를 얻을 수 있다. 트라이의 한 노드를 표현하는 객체는 **자손 노드들을 가리키는 포인터 목록**과 이 노드가 **종료 노드인지를 나타내는 불린 값 변수**로 구성이 된다. 

트라이 자료구조는 각 노드별로 알파벳 a부터 z까지 저장할 수 있는 공간이 필요하여 메모리를 엄청나게 낭비하게 되지만, 다음 노드를 찾는 과정에서 어떤 탐색도 필요하지 않다는 장점이 있다. 다음은 트라이를 작성한 예제 코드이다.

<br>

# <span style = "color: #00adb5">트라이의 구현</span>
```cpp
const int ALPHABETS = 26;
int toNumber(char ch) {return ch - 'A';}
struct TrieNode {
    TrieNode* children[ALPHABETS];
    bool terminal;

    TrieNode(): terminal(false) {
        memset(children, 0, sizeof(children));
    }

    ~TrieNode() {
        for (int i = 0; i < ALPHABETS; ++i)
            if (children[i])
                delete children[i];
    }

    void insert(const char* key) {
        if (*key == 0) terminal = true;
        else {
            int next = toNumber(*key);
            if (children[next] == NULL)
                children[next] = new TrieNode();
            children[next]->insert(key + 1);
        }
    }

    TrieNode* find(const char* key) {
        if (*key == 0) return this;
        int next = toNumber(*key);
        if (children[next] == NULL) return NULL;
        return children[next]->find(key + 1);
    }
};
```

분석
---
위의 코드에서 `toNumber()`은 알파벳을 숫자로 변환해주는 함수이다. 알파벳이 대문자가 아니라, 소문자로 등장하는 경우, 이 부분만 바꾸어서 문제를 해결하면 된다.

`find()`와 `insert()`의 시간복잡도는 문자열의 길이 만큼 재귀호출을 수행하기 때문에, 두 함수 모두 문자열의 길이에 선형 비례한다. 이런 탐색속도는 다른 어떤 자료구조보다 빠르기 때문에, 트라이는 빠른 속도가 필요한 검색 엔진이나 기타 문자열 처리 프로그램에서 자주 사용이 된다.

단점
---
트라이의 최대 단점은 요구하는 메모리 공간이 너무 크다는 것이다. 문자열의 길이의 총합이 100만이 되면, 요구하는 메모리가 200MB가 된다. 트라이에서 메모리를 절약하기 위한 많은 기법들 (트리플 어레이 트라이, triple-array-trie)이 있지만 프로그래밍 대회에서 사용하기에는 복잡하고 시간이 오래걸린다. 일반적으로 대회에서 트라이를 사용해야하는 경우, 문자열의 길이가 길지 않게 주어진다.
