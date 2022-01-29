---
title:  "Quickselect / nth_element"
excerpt: "모든 원소를 정렬하지 않고, 크기순으로 N번째 원소를 찾아내는 빠른 방법 O(n)"

categories:
  - concept
tags:
  - [정렬]

toc: true
toc_sticky: true
 
date: 2022-01-29 00:00:00
last_modified_at: 2022-01-29 00:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **Wikipedia - Quickselect**를 읽고 정리하는 글입니다.<br>
더 자세한 설명은 해당 문서에 더 상세하게 설명되어 있습니다.
{: .notice--info}

[🌏 Wikipedia: Quickselect](https://en.wikipedia.org/wiki/Quickselect)

**Quickselect** 알고리즘은 정렬이 되지 않은 벡터공간에서 K번째로 작은 원소를 찾는 알고리즘이다. 이 알고리즘은 `pivot`을 사용하는 Quicksort와 관련이 많다. 이 알고리즘은 Quicksort와 마찬가지로 Tony Hoare에 의해 개발이 되어서 `Hoare's selection algorithm`이라고도 한다.

# Algorithm
`Quickselect` 알고리즘이 작동하는 방식은 `Quick sort`와 유사한 부분을 많이 갖는다. Quick sort는 임의의 값을 pivot으로 선택하여 해당 pivot보다 낮은 값을 왼쪽에, 더 큰값을 pivot의 오른쪽으로 분할하는 방식을 진행했다. Quickselect도 마찬가지로 하나의 pivot을 정해서 둘로 분할한다.

이제 이후 과정에서 차이가 발생한다. Quicksort는 분할된 각 벡터(리스트)에 대해서 다시 재귀적으로 pivot정렬을 진행했다면, 이번에는 우리가 찾고자 하는 부분 즉, index범위에 K가 포함된 곳만 다시 pivot정렬을 진행한다.

![image](https://upload.wikimedia.org/wikipedia/commons/0/04/Selecting_quickselect_frames.gif){: .align-center width="70%"}

## Partition by pivot
Quick sort에는 `pivot`을 기준으로 작은 값과 큰 값으로 분할하는 과정이 있다. 해당 과정을 선형시간에 수행하는 알고리즘은 다음과 같다. 해당 과정은 *Lomuto partition scheme* 이다.
```cpp
// pivot을 기준으로 리스트를 분할
function partition(list, left, right, pivotIndex) is
    pivotValue := list[pivotIndex] // pivotIndex의 값을 pivotValue에 저장
    swap list[pivotIndex] and list[right] // pivot값을 리스트의 가장 마지막으로 이동
    storeIndex := left

    // pivot을 기준으로 좌측에는 작은값, 오른쪽에는 큰값으로 구분
    for i from left to right - 1 do
        if list[i] < pivotValue then
            swap list[storeIndex] and list[i]
            increment storeIndex

    swap list[right] and list[storeIndex]
    return storeIndex
```

## Quickselect pseudo code
위의 코드를 기반으로 Quickselect를 진행하는 *pseudo code* 는 다음과 같다.

```cpp
// [left, right] 에서 k번째로 작은수를 반환하는 코드
function select(list, left, right, k) is
    loop
        // list가 하나의 원소만 갖는 경우
        if left = right then
            return list[left]

        // [left, right]사이에 존재하는 특정원소를 pivot으로 선택한다.
        // e.g., left + floor(rand() % (right - left + 1))
        pivotIndex := ... 
        pivotIndex := partition(list, left, right, pivotIndex)

        if k = pivotIndex then
            return list[k]
        else if k < pivotIndex then
            right := pivotIndex - 1
        else
            left := pivotIndex + 1
```

## Quickselect with CPP
```cpp
int partition(vector<int> &arr, int left, int right, int pivotIndex) {
    int pivotValue = arr[pivotIndex];
    swap(arr[pivotIndex], arr[right]);
    
    int storeIndex = left;
    for (int i = left; i <= right; ++i)
        if (arr[i] < pivotValue) {
            swap(arr[i], arr[storeIndex]);
            ++storeIndex;
        }

    swap(arr[right], arr[storeIndex]);
    return storeIndex;
}

// 최악의 경우 O(N^2)이 되는 Quickselect
int Quickselect(vector<int> &arr, int left, int right, int k) {
    while (true) {
        if (left == right) return arr[left];

        int pivotIndex = left + floor(rand() % (right - left + 1));
        pivotIndex = partition(arr, left, right, pivotIndex);

        if (k == pivotIndex) return arr[k];
        else if (k < pivotIndex) right = pivotIndex - 1;
        else left = pivotIndex + 1;
    }
}
```

<br>

# Time complexity: random pivot
Quicksort와 비슷하게 Quickselect는 성능이 좋지만, 그 성능은 선택한 pivot에 따라서 차이가 많이 난다. 좋은 pivot을 선택하게 되면, partitioning과정이 효과적으로 이루어질 수 있지만, 그렇지 못한다면 pivot을 기준으로 모든 원소가 모두 왼쪽이나 오른쪽에만 계속 치우쳐져 있을 수 있다. 이렇게 되면 최악 시간복잡도는 \\(O(n^2)\\) 이다.

위의 문제를 해결하기 위해서 **좋은 pivot을 선택하는 과정**을 추가해야한다. 좋은 pivot이 되기 위해서 확인해야하는 것은 다음 2가지이다.

1. pivot을 선택할 때, 모든 원소를 선택하는 확률을 동일해야한다. (*Uniformly randomized*)
2. partitioning과정에서 한쪽의 원소가 \\(n \times 0.75\\)를 넘어가면 다시 돌아가서 새로운 pivot을 뽑는다.

해당 기법을 적용하면 시간복잡도를 \\(O(n)\\)으로 줄이는것이 가능하다. 더 자세한 내용은 [여기](https://2jinishappy.tistory.com/124)에서 확인할 수 있다.

<br>

# nth_element
위에서 설명한 **quickselect** 를 직접 구현해서 K번째 수를 구하지 않고, STL인 `nth_element`를 사용해서 구하는 방법도 존재한다.
```cpp
int get_kth_elem(vector<int> &arr, int k) {
    nth_element(arr.begin(), arr.begin() + k - 1, arr.end());
    return arr[k - 1]
}
```

<br>

# References

[📘 Wikipedia: Quickselect](https://en.wikipedia.org/wiki/Quickselect)

[📘 Array의 k번째 작은 element 찾기 - QuickSelect 알고리즘](https://2jinishappy.tistory.com/124)