---
title:  "[Recommender System] Approximate Nearest Neighbor"
excerpt: "유사한 벡터를 빠르게 찾는 ANN 기법에 대한 설명"

categories:
  - RecSys
tags:
  - [AI, Naver, BoostCamp, Recommender System]
toc: true
toc_sticky: true
use_math: true
 
date: 2022-03-14 11:00:00
last_modified_at: 2022-03-14 11:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Approximate Nearest Neighbor

Approximate Nearest Neightbor의 정의와 필요성에 대해 배우고 ANN알고리즘을 제공하는 라이브러리에 대해서 알아보자

## ANN의 필요성

[여기](http://localhost:4000/recsys/boostcamp-recsys-knncf/#knn-cf%EC%9D%98-%EC%95%84%EC%9D%B4%EB%94%94%EC%96%B4)에서 한 번 언급한 적이 있는 내용으로 가장 가까운 벡터를 찾기 위한 연산을 효율적으로 하기 위해서 필요하다.

### Nearest Neighbor

Nearest Neighbor(NN)은 벡터공간에서 내가 찾고자하는 벡터와 가장 유사한 벡터가 무엇인지 찾는 알고리즘이다. MF를 가지고 추천아이템을 서빙한다면, 유저 $u$와 비슷한 유저 $v$를 찾는 문제가 될 것이고, 아이템 $i$와 비슷한 아이템 $j$를 추천하는 문제가 될 것이다.

### Brute Force KNN

![image](https://user-images.githubusercontent.com/91870042/158064994-683b7bb2-92a2-4a35-bd38-2c08b8043d2f.png){: .align-center width="70%"}

NN을 정확하게 구하기 위해서 모든 벡터에 대해서 유사도 비교를 수행하는 것을 말한다. 벡터의 개수와 그 차원의 크기에 비례한 비교연산이 필요하게 된다. 100차원을 갖는 벡터가 100만개 존재한다고 하면 모든 NN을 구하기 위해서는 46시간이 소모된다. 그렇기 때문에 **정확도를 조금 포기하더라도 아주 빠른 속도로 주어진 벡터에 근접한 다른 이웃벡터를 찾는 알고리즘이 필요**하다.

### ANNOY

![image](https://user-images.githubusercontent.com/91870042/158065078-8f3e49c0-b523-4fc6-bbb9-19cad3a8b137.png){: .align-center width="70%"}

**ANNOY**는 음악제공 회사인 spotify에서 개발한 tree-based ANN기법으로 *주어진 벡터들을 여러개의 subset으로 나누어 tree형태의 자료구조로 구성하고, 이를 기반으로 탐색하는 방법*을 말한다.

ANNOY의 알고리즘의 작동방식은 다음과 같다.

1. 벡터 공간에 존재하는 임의의 두 점을 선택하고, 두 점 사이의 hyperplane으로 벡터공간을 나눈다.
2. Subspace에 있는 점들의 개수를 node로 하여 binary tree를 생성/갱신 한다.
3. Subspace내에 있는 점들의 개수가 $K$개를 초과한다면 다시 1번부터 진행한다.

![image](https://user-images.githubusercontent.com/91870042/158065215-b3a6d326-0d74-4518-a346-afe67ff02958.png){: .align-center width="50%"}

위에서 만들어진 binary tree를 간단하게 살펴보면 최상단의 subspace로 부터 하위 subset이 어떻게 나뉘는지를 저장하고 있다.

### ANNOY의 문제점과 해결 방안

ANNOY의 문제점은 가장 근접한 점이 트리구조에서 다른 노드에 존재하는 경우, 해당 점은 후보 subset에 포함될 수 없다는 문제가 있다. 좀 더 쉽게 말해서, 실제로는 입력 벡터 $v$와 가까이 존재하지만, 다른 subset으로 나눠진다면 유사하다고 판단하지 않는다는 것이다.

이 문제점을 해결하기 위해서 2가지 방법이 제안되었다.

1. *우선순위큐 (Priority Queue)를 사용하여 가까운 다른 트리의 노드를 탐색할 수 있도록 한다*
2. *하나의 벡터공간에 대해서 여러개의 Binary Tree를 생성하여 병렬적으로 탐색*

![gif image](https://erikbern.com/assets/2015/09/animated.gif){: .align-center width="50%"}

### ANNOY의 특징

- Search Index를 생성하는 것이 다른 ANN기법에 비해 간단하고 가볍다.
    - 아이템 개수가 많지 않고 벡터의 차원이 낮은 경우 사용하기에 적합하다.
    - 하지만, GPU연산은 지원하지 않는다!
- Search 해야 할 이웃의 개수를 알고리즘이 보장한다.
- 파라미터 조정을 통해 accuracy ↔ speed의 trade-off를 조정할 수 있다.
- 단, 기존 생성된 binary tree에 새로운 데이터를 추가할 수 없다.

## 기타 ANN 기법

### HNSW

![image](https://user-images.githubusercontent.com/91870042/158065736-4b0290f2-e3fa-4204-9a34-b447fa1e4579.png){: .align-center width="50%"}

**Hierarchical Navigable Small World Graphs(NNSW)**는 임베딩 공간에 표현된 벡터를 모두 사용하는 것이 아니라, 여러개의 레이어를 사용해서 더 효과적인 탐색이 이루어지도록 만들어준다.

먼저 벡터를 그래프의 노드로 표현하고 인접한 노드를 간선으로 연결한다. Layer을 여러개 두어 최상위 Layer로 갈수록 노드의 개수가 감소하도록 만든다. 이 때 상위 Layer로 올릴 노드는 Random Sampling을 진행한다. 그리고 계층적으로 탐색을 진행하게 하여 탐색속도를 빠르게 한다.

작동방식은 다음과 같다.

1. 최상위 Layer에서 **임의의 노드에서 선택**
2. 현재 Layer에서 **타겟 노드와 가장 가까운 노드로 이동**
3. 현재 Layer에서 더 가까워질 수 없다면 하위 Layer로 이동
4. **타겟 노드에 도착할 때까지** 2번과 3번을 반복
5. 2번~4번을 진행할 때, 방문했던 노드들만 후보로 하여 NN을 탐색한다.

이 HNSW를 Python 에서 지원하는 대표적인 라이브러리에는 `nmslib`와 `faiss`가 있다.

### IVF

![image](https://user-images.githubusercontent.com/91870042/158065922-94f92c53-6e37-482e-a117-cbd758181861.png){: .align-center width="50%"}

**Inverted File Index (IVF)**는 주어진 벡터공간을 $n$개의 조각(클러스터)로 나눠서 같은 조각에 속한 벡터끼리 유사하다고 판단하는 방법이다. 벡터의 index를 조각별로 inverted list에 저장하고 query를 통해서 유사한 벡터를 찾고자 할때, 해당 클러스터에 존재하는 벡터에 대해서만 탐색을 진행하게 된다.

이때 정확도를 높이기 위해서는 조각의 수를 늘리면 되지만, 너무 커지게 되면 연산량이 많아져 속도가 느려진다는 trade-off 가 발생한다.

### Product Quantization - Compression

![image](https://user-images.githubusercontent.com/91870042/158065961-52805f7b-7af9-40f6-b515-eed81d29c1b8.png){: .align-center width="50%"}

기존의 벡터를 $n$개의 서브벡터로 나누고, 각 서브벡터에 대해서 *k-mean clustering*을 수행하여 centroid 값을 구한다. 이런식으로 나머지 모든 벡터에 대해서도 똑같은 연산을 하여 각각 $n$개의 centroid값을 얻어내서 압축하여 표현하는 것을 말한다.

기존에 벡터가 갖는 정확한 값은 잃어버리게 되지만, centroid값을 통해서 최대한 유사하게 유지시킬 수 있다. 두 벡터간의 유사도를 구할 때, 미리 구한 centroid 사이의 유사도를 연산하기 때문에 시간이 오래 걸리지 않게 된다.

추가로, IVF와 PQ를 동시에 사용하는 IVFPQ가 있으며 이를 통해 더 빠르고 효율적인 ANN을 만들어볼 수 있다. 이 IVFPQ는 `faiss` 라이브러리에서 제공하고 있다.