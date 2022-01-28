---
title:  "[PyTorch] PyTorch Basics"
excerpt: "PyTorch에서 데이터들의 연산을 위한 Tensor사용법, 자동미분 기능인 AutoGrad"

categories:
  - pytorch
tags:
  - [AI, Naver, BoostCamp, Python, Pytorch]
toc: true
toc_sticky: true
 
date: 2022-01-24 01:00:00
last_modified_at: 2022-01-24 01:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# PyTorch 패키지
- torch : Tensor와 Numpy에서 제공하는 기본 수학 함수들이 포함된 메인 네임스페이스
- torch.autograd : 자동미분(auto grad)를 위한 함수들이 포함되어 있다.
- torch.nn : 신경망을 구축하기 위한 데이터 구조, 레이어가 정의되어 있다. (RNN, LSTM, ReLU, MSELoss)
- torch.optim : PyTorch Dataset의 Parameter을 최적화 해주는 알고리즘이 구현되어 있다. (SGD)
- torch.utils.data : SGD의 반복연산을 실행할 때 사용하는 미니 배치용 함수가 포함되어 있다.
- torch.onnx : Tensorflow, Keras와 같은 다른 딥러닝 프레임워크와 PyTorch 를 연결시킬 때 사용한다.

# Tensor
`텐서(Tensor)`는 다차원 Array를 표현하는 PyTorch의 클래스로서 numpy의 ndarray와 동일하다고 볼 수 있다. 마찬가지로 `TensorFlow`의 tensor와도 그 개념이 같다.

다음은 numpy에서 ndarray를 생성하는 코드와 pytorch에서 tensor를 생성하는 코드이다. 두 개념이 동일하기 때문에 코드역시 비슷하게 구성되어 있다.

## ndarray(Numpy), tensor(PyTorch)
```py
import numpy as np

n_array = np.arange(10).reshape(2, 5)
print(n_array)
print("ndim :", n_array.ndim, "shape :", n_array.shape)
```
```py
import torch

t_array = torch.FloatTensor(n_array)
print(t_array)
print("ndim :", t_array.ndim, "shape :", t_array.shape)
```

## Array to Tensor
```py
data = [[3, 5], [10, 5]]
x_data = torch.tensor(data) # list형태의 데이터를 torch를 사용해 tensor로 변환
x_data
>>> tensor([[ 3,  5],
            [10,  5]])

nd_array_ex = np.array(data)
tensor_array = torch.from_numpy(nd_array_ex)
tensor_array
>>> tensor([[ 3,  5],
            [10,  5]], dtype=torch.int32)
```

## Tensor의 자료형
텐서가 가질 수 있는 자료형은 기본적으로 numpy에서 사용하는 자료형과 동일하다. 추가된 자료형이 있다면, GPU를 사용한 연산을 지원하는 자료형이다.

`torch.cuda.FloatTensor`, `torch.cuda.DoubleTensor` 등.

추가로, 기본적인 문법 역시 numpy의 문법이 PyTorch에서도 그대로 적용이 된다. numpy에서 유용하게 사용한 **slicing**, numpy의 메소드를 그대로 사용할 수 있다.

```py
data = [[3, 5, 20], [10, 5, 50], [1, 5, 10]]
x_data = torch.tensor(data)

x_data[1:]
>>> tensor([[10,  5, 50],
            [ 1,  5, 10]])

x_data[:2, 1:]
>>> tensor([[ 5, 20],
            [ 5, 50]])

x_data.flatten()
>>> tensor([ 3,  5, 20, 10,  5, 50,  1,  5, 10])

torch.ones_like(x_data) # 꽤 자주 사용한다!
>>> tensor([[1, 1, 1],
            [1, 1, 1],
            [1, 1, 1]])

x_data.numpy()
>>> array([[ 3,  5, 20],
           [10,  5, 50],
           [ 1,  5, 10]], dtype=int64)

x_data.shape
>>> torch.Size([3, 3])

x_data.dtype
>>> torch.int64
```

<br>

# Tensor handling

## view

numpy의 `reshape()`와 동일하게 tensor의 모양을 변화시키는 함수이다. 하지만 둘 사이에는 약간의 차이가 존재한다.

[🔍 What's the difference between reshape and view in pytorch? - stackoverflow](https://stackoverflow.com/questions/49643225/whats-the-difference-between-reshape-and-view-in-pytorch)

- `reshape()`: 가능하면 input의 view를 반환하며, 그렇지 못한 경우에는 **contiguous**한 텐서로 복하사여 view를 반환한다.
- `view()`: 기존의 데이터와 같은 메모리 공간을 공유하며, **contiguous**해야만 함수가 실행된다.

한 번 예시를 통해서 알아보자.

1. 함수명에서 알 수 있다시피, `torch.veiw`는 원래의 텐서의 view를 생성한다. 이렇게 생성된 텐서는 항상 원본 텐서와 메모리를 공유한다. 그렇기 때문에 원래의 텐서 값이 변경되면 `torch.veiw`로 생성된 텐서의 값도 변경이 이루어진다.

    ```py
    z = torch.zeros(3, 2) # 원본 텐서 z를 생성 (0으로 채워짐)
    x = z.view(2, 3) # 원본 텐서 z에 대한 view를 생성
    z.fill_(1) # 원본 텐서의 값 변경이 일어남
    x # view에도 값의 변경이 일어남을 확인
    >>> tensor([[1., 1., 1.],
                [1., 1., 1.]])
    ```

2. `torch.view`로 새롭게 생서된 텐서가 원본 텐서와 값을 공유하는 것을 보장하기 위해, `torch.view`는 생성된 2개의 텐서간에 **연속성에 대한 제약**을 둔다. 추가로, 두 텐서의 모양이 호환되는 경우에도 `torch.view`에서는 오류가 발생할 수 있다.

    ```py
    z = torch.zeros(3, 2)
    y = z.t() # 텐서 z이 transpose
    y.size()
    >>> torch.Size([2, 3])

    y.view(6)
    >>> Traceback (most recent call last):
        File "<stdin>", line 1, in <module>
        RuntimeError: invalid argument 2: view size is not compatible with input tensor's
        size and stride (at least one dimension spans across two contiguous subspaces).
        Call .contiguous() before .view().
    ```

3. 반면에 `torch.reshape`는 연속성에 대한 제약을 부과하지 않는다. 그렇기 때문에 당연히 원본 텐서와의 값을 공유한다는 보장도 없게 된다. `torch.reshape`를 하는 경우, 원본 텐서의 view형태가 될 수도 있고, 전혀 값을 공유하지 않는 새로운 텐서로 생성될 수 있다.

    ```py
    z = torch.zeros(3, 2)
    y = z.reshape(6)
    x = z.t().reshape(6)

    z.fill_(1)
    >>> tensor([[1., 1.],
                [1., 1.],
                [1., 1.]])

    y
    >>> tensor([1., 1., 1., 1., 1., 1.]) # reshape()했음에도 값을 공유: view형태

    x
    >>> tensor([0., 0., 0., 0., 0., 0.]) # reshape()를 하여 값이 공유되지 않는 형태
  ```

## squeeze, unsqueeze

![image](https://i.stack.imgur.com/9AJJA.png)

[🔍 Pytorch squeeze and unsqueeze - stackoverflow](https://stackoverflow.com/questions/61598771/pytorch-squeeze-and-unsqueeze)

- `torch.squeeze`: 차원의 개수가 1인 차원을 삭제한다.
- `torch.unsqueeze`: 차원의 개수가 1인 차원을 추가한다.

다음 텐서를 보면, 2번째 차원의 개수가 1인 것을 알 수 있다. 이때, `torch.squeeze`를 사용하면 해당 차원을 삭제하는 것이 가능하다.

```py
tensor_ex = torch.rand(size=(2, 1, 2))
tensor_ex
>>> tensor([[[0.8369, 0.3459]],
            [[0.5851, 0.1484]]])

print(tensor_ex.squeeze())
>>> tensor([[0.8369, 0.3459],
            [0.5851, 0.1484]])

print(tensor_ex.squeeze().shape)
>>> torch.Size([2, 2])
```

```py
tensor_ex = torch.rand(size=(2, 2))

tensor_ex.unsqueeze(0).shape
>>> torch.Size([1, 2, 2])

tensor_ex.unsqueeze(1).shape
>>> torch.Size([2, 1, 2])

tensor_ex.unsqueeze(2).shape
torch.Size([2, 2, 1])
```

<br>

# Tensor Operations
텐서의 연산은 numpy에서 제공하는 연산과 동일하게 수행된다. 기본적으로 텐서간의 연산에서는 동일한 크기를 가진것 끼리 연산이 된다.

## 텐서간의 사칙연산
```py
n1 = np.arange(10).reshape(2, 5)
n2 = np.arange(10).reshape(5, 2)
t1 = torch.FloatTensor(n1)
t2 = torch.FloatTensor(n2)

t1 + t1
t1 - t1
t1 + 10
```

## 텐서간의 곱셈연산
하지만, 행렬의 곱셈 연산을 수행할 때는 `pytorch.dot`이 아닌 `pytorch.mm`이나 `pytorch.matmul`을 사용한다.

```py
t1.mm(t2) # 행렬의 곱셈 실행
>>> tensor([[ 60.,  70.],
            [160., 195.]])

t1.dot(t2)
>>> RuntimeError: 1D tensors expected, but got 2D and 2D tensors

t1.matmul(t2)
>>> tensor([[ 60.,  70.],
            [160., 195.]])
```

### mm과 matmul의 차이

`pytorch.mm`과 `pytorch.matmul`의 차이는 연산에서 **boradcasting**을 지원하는지의 여부에서 발생한다. 먼저 `pytorch.mm`은 broadcasting을 지원하지 않고, `pytorch.matmul`는 broadcasting을 지원한다.

```py
a = torch.rand(5, 2, 3)
b = torch.rand(3)

a.mm(b)
>>> NameError: name 'torch' is not defined

a.matmul(b)
tensor([[0.6014, 0.8045],
        [0.9585, 1.1915],
        [0.9335, 0.8395],
        [1.3930, 0.8872],
        [1.1288, 1.4620]])
```

> `matmul`의 연산은 `mm`이 행렬 a의 행에 대해서 수행된다고 생각해도 된다.

```py
a[0].mm(torch.unsqueeze(b, 1))
a[1].mm(torch.unsqueeze(b, 1))
a[2].mm(torch.unsqueeze(b, 1))
a[3].mm(torch.unsqueeze(b, 1))
a[4].mm(torch.unsqueeze(b, 1))
```

## ML, DL에서 사용하는 연산
PyTorch는 `torch.nn.functional`이라는 모듈을 통해서 다양한 수식변환을 지원한다. 기본적인 활성함수를 지원하고 그 외에도 원핫벡터로의 변환등 여러가지 연산들이 포함되어 있다.

```py
import torch.nn.functional as F

tensor = torch.FloatTensor([0.5, 0.7, 0.1])
h_tensor = F.softmax(tensor, dim = 0)
h_tensor
>>> tensor([0.3458, 0.4224, 0.2318])

y = torch.randint(5, (10, 5))
y_label = y.argmax(dim = 1)
F.one_hot(y_label)

>>> tensor([[0, 0, 1, 0],
            [0, 0, 1, 0],
            [0, 1, 0, 0],
            [1, 0, 0, 0],
            [0, 1, 0, 0],
            [0, 0, 0, 1],
            [0, 1, 0, 0],
            [0, 1, 0, 0],
            [0, 0, 0, 1],
            [0, 0, 1, 0]])
```

추가로, 가능한 모든 쌍을 고려해주는 cartesian_product를 지원한다.

```py
import itertools
a = [1, 2, 3]
b = [4, 5]
list(itertools.product(a, b))
>>> [(1, 4), (1, 5), (2, 4), (2, 5), (3, 4), (3, 5)]

tensor_a = torch.tensor(a)
tensor_b = torch.tensor(b)
torch.cartesian_prod(tensor_a, tensor_b)
>>> tensor([[1, 4],
            [1, 5],
            [2, 4],
            [2, 5],
            [3, 4],
            [3, 5]])
```

## autograd
PyTorch의 핵심이라고 할 수 있는 **자동미분**연산을 지원한다. 이 연산을 수행하기 위해서는 `pytorch.backward()` 함수를 사용한다.

예를 들어서 다음 함수가 존재한다고 할 때, \\(z\\)를 \\(w\\)에 대해서 미분을 진행할 수 있다.

$$ y = w^2 $$

$$ z = 10y^2 + 25 $$

$$ \frac{\partial z}{\partial w} = 20w $$

```py
w = torch.tensor(2.0, requires_grad  = True)
y = w**2
z = 10*y + 25
z.backward()
w.grad
>>> tensor(40.)
```

또 다른 예제로 다음을 한 번 보자. 미분을 진행하고 거기에 해당하는 값을 대입할 때, list형태로 전달을 해서 여러 값에 대한 미분값을 확인하는 것이 가능하다.

$$ Q = 3a^3 - b^2 $$

$$ \frac{\partial Q}{\partial a} = 9a^2 $$

$$ \frac{\partial Q}{\partial b} = -2b $$

```py
a = torch.tensor([2., 3.], requires_grad=True)
b = torch.tensor([6., 4.], requires_grad=True)

Q = 3*a**3 - b**2
external_grad = torch.tensor([1., 1.]) # 미분을 해서 알아내고 싶은 값의 수 만큼. 
Q.backward(gradient = external_grad)

a.grad
>>> tensor([36., 81.])

b.grad
>>> tensor([-12.,  -8.])
```

위의 코드에서 `external_grad`는 역전파를 진행하면서 알아내고 싶은 값의 개수를 알려주는 역할을 진행한다. 최종적으로 나온 미분값에 external_grad값을 곱해져서 결과로 출력되는데 영향을 주고 싶지 않을 경우 1로 설정한다.

<br>

# References

[📘 [PyTorch] view, reshape, transpose, permute함수의 차이와 contiguous](https://sanghyu.tistory.com/3)

[📘 What's the difference between reshape and view in pytorch?](https://stackoverflow.com/questions/49643225/whats-the-difference-between-reshape-and-view-in-pytorch)

[📘 Pytorch squeeze and unsqueeze - stackoverflow](https://stackoverflow.com/questions/61598771/pytorch-squeeze-and-unsqueeze)

[📘 [DL, PyTorch] 차원재구성 - tistory](https://anweh.tistory.com/12)