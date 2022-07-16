---
title:  "[PyTorch] torch.nn: AutoGrad & Optimizer"
excerpt: "PyTorch의 Module, Parameter, Backward과정의 소개"

categories:
  - pytorch
tags:
  - [AI, Naver, BoostCamp, Python, Pytorch]
toc: true
toc_sticky: true
 
date: 2022-01-25 00:00:00
last_modified_at: 2022-01-25 00:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# torch.nn.Module

> Base class for all neural network modules.  
> Your models should also subclass this class.  
> Modules can also contain other Modules, allowing to nest them in a tree structure. - PyTorch docs.

`torch.nn.Module`은 신경망 모듈에서 사용하는 기능들을 모아놓은 하나의 상자이다. 이 상자에는 다시 또 다른 모듈을 넣을 수 있다. 모듈을 구성하는 것은 온전히 설계자의 몫이며 넣고 싶은 기능들을 모아서 나만의 모델을 만드는 것이 가능하다.

```py
import torch.nn as nn
import torch.nn.functional as F

class Model(nn.Module):
    def __init__(self):
        super(Model, self).__init__()
        self.conv1 = nn.Conv2d(1, 20, 5)
        self.conv2 = nn.Conv2d(20, 20, 5)

    def forward(self, x):
        x = F.relu(self.conv1(x))
        return F.relu(self.conv2(x))

model = Model()
```

위는 모듈을 상속받은 하나의 모델이다. 기본적으로 모델은 `__init__`의 구현과 `forward()`의 구현이 요구된다. 또한 `torch.nn.Module`을 상속해야한다. 이를 상속함으로써 해당 클래스의 기능을 그대로 가져와서 사용하는 것이 가능해진다.

## \_\_init\_\_
`__init__()`은 생성자로서 클래스가 호출되는 경우 바로 실행이 이루어지는 부분이다. 여기에서는 모델에서 사용이 될 모듈들을 정의하고 내가 원하는 기능을 담아 넣거나 초기화를 진행한다.

이 생성자에서는 반드시 상위클래스를 호출하여 똑같이 `__init__()`을 해야한다. 부모클래스에서도 똑같이 초기화가 일어나고 부모클래스의 속성을 받아올 수 있도록 해줘야 한다. 만약 이런 초기화가 일어나지 않는다면 부모클래스의 속성을 사용하는것이 불가능하다.

참고: [stackoverflow: Why is the super constructor necessary in PyTorch custom modules?](https://stackoverflow.com/questions/63058355/why-is-the-super-constructor-necessary-in-pytorch-custom-modules)

```py
class LR(nn.Module):
    
    # Constructor
    def __init__(self, input_size, output_size):
        
        # Inherit from parent
        super(LR, self).__init__()
        self.test = 1
        self.linear = nn.Linear(input_size, output_size)
        
    
    # Prediction function
    def forward(self, x):
        out = self.linear(x)
        return out
```

위의 코드에서 부모클래스를 초기화하지 않고, `self.linear = nn.Linear(input_size, output_size)` 가 실행된다면 내부적으로는 `__setattr__()`를 부른다. 해당 부분의 코드를 살펴보자.

```py
def __setattr__(self, name: str, value: Union[Tensor, 'Module']) -> None:
    # [...]
    modules = self.__dict__.get('_modules')
    if isinstance(value, Module):
        if modules is None:
            raise AttributeError("cannot assign module before Module.__init__() call")
        remove_from(self.__dict__, self._parameters, self._buffers, self._non_persistent_buffers_set)
        modules[name] = value
```

3번째 줄을 보면 `_modules`라는 속성을 가지고 있어야 하는데, 이 속성을 초기화하여 가져와야만 한다. 해당속성이 존재하지 않는다면 **AttributeError**를 발생시키는 것을 알 수 있다.

이 속성(`_modules`)은 부모클래스의 `__init__()`에서 초기화가 이루어지기 때문에 `super().__init__` 을 실행시키면 내가 호출한 모델의 클래스에도 해당 속성을 상속받아 사용하는 것이 가능해진다.

다음은, 부모의 `__init__` 코드이다. 최하단을 보면 `_modules`의 초기화가 이루어지고 있다.

```py
def __init__(self):
    """
    Initializes internal Module state, shared by both nn.Module and ScriptModule.
    """
    torch._C._log_api_usage_once("python.nn_module")

    self.training = True
    self._parameters = OrderedDict()
    self._buffers = OrderedDict()
    self._non_persistent_buffers_set = set()
    self._backward_hooks = OrderedDict()
    self._forward_hooks = OrderedDict()
    self._forward_pre_hooks = OrderedDict()
    self._state_dict_hooks = OrderedDict()
    self._load_state_dict_pre_hooks = OrderedDict()
    self._modules = OrderedDict() 
```

## forward
`forward`는 호출한 모델에서 인자를 전달받아서 연산된 결과를 반환하는 것을 구현한다. 모델을 생성하고 만들어진 모델에 값을 넣었을 때, `forward`가 불러져서 계산된다.

반면에, `backward`를 구현하지 않아도 되는 것은 `autograd`를 사용하기 때문에 자동으로 정의가 이루어지기 때문이다.

<br>

# torch.nn.Parameter
모델에서 사용하는 텐서를 저장하고 보관하는 역할을 수행한다. **RNN**을 예시로 들어보자. RNN은 항상 마지막 Hidden State를 저장하고 그 Hidden State와 새로 들어온 입력에 대해서 다시 Hidden state를 만들어 주었다.

이렇기 때문에 모델이 호출이 되더라도 그 모델에 저장이 되는 변수가 필요해졌다. Parameter로 등록을 하게 되면, 해당 텐서의 저장이 가능하며 `parameters()`에 등록되어 어떤 것들이 있는지 확인하는 것도 가능하다.

```py
class MyLinear(nn.Module):
    def __init__(self, in_features, out_features, bias=True):
        super().__init__()
        self.in_features = in_features
        self.out_features = out_features
        
        self.weights = nn.Parameter(torch.randn(in_features, out_features))
        self.bias = nn.Parameter(torch.randn(out_features))
        
    def forward(self, x: Tensor):
        return x @ self.weights + self.bias

```

기본적으로 Parameter로 등록이 이루어지면, 추가로 `requires_grad=True` 라는 속성이 붙는다. 이는 어떤 함수에 대해서 기울기를 계산하여 저장하는 역할을 수행한다.

<br>

# backward
`backward`는 모델의 레이어에 존재하는 Parameter의 미분을 수행하거나 `forward`를 통해 얻는 결과 \\(\hat{y}\\) 와 실제값 \\(y\\) 의 차이에 대해서 미분을 한다.

```py
import torch

x = torch.ones(3, 3, requires_grad=True)
y = x + 3
z = 3 * (y ** 2)
o = z.mean()
o.backward()

print(x.grad())
>>> tensor([[2.6667, 2.6667, 2.6667],
            [2.6667, 2.6667, 2.6667],
            [2.6667, 2.6667, 2.6667]])
```

아래는 위 코드의 동작과정을 설명한다.

\begin{aligned}
    y &= x + 3\\\\\\
    z &= 3y^2\\\\\\
    o &= \frac{1}{9}z
\end{aligned}

$$ \frac{\partial o}{\partial z} \frac{\partial z}{\partial y} \frac{\partial y}{\partial x} = \frac{1}{9}\times 6y\times 1$$

마지막으로 `[optimizer].step()`를 사용하여 Parameter를 업데이트 할 수 있다.

```py
# 테스트 데이터의 생성
import numpy as np

x_values = [i for i in range(11)]
x_train = np.array(x_values, dtype=np.float32)
x_train = x_train.reshape(-1, 1)

y_values = [2*i + 1 for i in x_values]
y_train = np.array(y_values, dtype=np.float32)
y_train = y_train.reshape(-1, 1)
```

```py
# 선형회귀 모델의 생성
import torch
from torch.autograd import Variable

class LinearRegression(torch.nn.Module):
    def __init__(self, inputSize, outputSize):
        super(LinearRegression, self).__init__()
        self.linear = torch.nn.Linear(inputSize, outputSize)
        
    def forward(self, x):
        out = self.linear(x)
        return out
```

```py
inputDim = 1
outputDim = 1
learningRate = 0.01
epochs = 1000

model = LinearRegression(inputDim, outputDim)

if torch.cuda.is_available():
    model.cuda()

criterion = torch.nn.MSELoss()
optimizer = torch.optim.SGD(model.parameters(), lr=learningRate)

for epoch in range(epochs):
    if torch.cuda.is_available():
        inputs = Variable(torch.from_numpy(x_train).cuda())
        labels = Variable(torch.from_numpy(y_train).cuda())
    else:
        inputs = Variable(torch.from_numpy(x_train))
        labels = Variable(torch.from_numpy(y_train))

    outputs = model(inputs) # y-hat 계산
    loss = criterion(outputs, labels) # y-hat과 y의 오차 계산 (MSE Loss)

    
    optimizer.zero_grad() # ❗ Gradient를 0으로 초기화
    loss.backward()
    optimizer.step()
    
    print('epoch {}, loss {}'.format(epoch, loss.item()))
```
```py
# 결과값 확인
with torch.no_grad():
    if torch.cuda.is_available():
        predicted = model(Variable(torch.from_numpy(x_train).cuda())).cpu().data.numpy()
    else:
        predicted = model(Variable(torch.from_numpy(x_train))).data.numpy()
    print(predicted)
```

## `optimizer.zero_grad()` 로 설정하는 이유

`optimizer.zero_grad()`를 설정하는 것은, 자동미분이 진행되면서 parameter의 gradient값이 다음의
미분에도 간섭을 주기 때문에, 초기화를 진행하고 학습을 진행한다. 여기서 간섭을 주는것은 미분값인 gradient를 계석 누적하여 저장한다.

초기화를 진행하지 않는다면, 모델의 Parameter와 유사한 `Buffer`에 누적이 되어 원하는 결과값이 나오지 않을 수 있다. 하지만, 이러한 연산은 RNN의 모델에서는 계속 누적하여 연산을 해야하기 때문에 도움이 된다.

참고1: [stackoverflow: Why do we need to call zero_grad() in PyTorch?](https://stackoverflow.com/questions/48001598/why-do-we-need-to-call-zero-grad-in-pytorch)

참고2: [★ 5. optimizer.zero_grad()가 필요한 이유](https://wikidocs.net/53560)

## 직접 구현하는 backward
```py
class LR(nn.Module):
    def __init__(self, dim, lr=torch.scalar_tensor(0.01)):
        super(LR, self).__init__()
        self.W = torch.zeros(dim, 1, dtype=torch.float) # Weight
        self.b = torch.scalar_tensor(0) # bias
        self.grad = {
            "dw": torch.zeros(dim, 1, dtype=torch.float),
            "db": torch.scalar_tensor(0)
        }
        self.lr = lr
    
    def sigmoid(self, z):
        return 1 / (1 + torch.exp(-z))
    
    def forward(self, x):
        z = x @ self.W.T + self.b
        return self.sigmoid(z)
    
    def backward(self, x, y_hat, y):
        self.grad['dw'] = (1 / x.shape[1]) * (x @ (y_hat - y).T)
        self.grad['db'] = (1 / x.shape[1]) * torch.sum(y_hat - y)

    def optimize(self):
        self.W -= self.lr * self.grad['dw']
        self.b -= self.lr * self.grad['db']        
```

<br>

# References

[📘 Pytorch에서는 왜 항상 optimizer.zero_grad()를 해줄까?](https://stackoverflow.com/questions/48001598/why-do-we-need-to-call-zero-grad-in-pytorch)

[📘 TORCH.AUTOGRAD.FUNCTION.BACKWARD](https://pytorch.org/docs/stable/generated/torch.autograd.Function.backward.html?highlight=backward#torch.autograd.Function.backward)

[📘 Understanding torch.nn.Parameter](https://stackoverflow.com/questions/50935345/understanding-torch-nn-parameter)

[📘 Why is the super constructor necessary in PyTorch custom modules?](https://stackoverflow.com/questions/63058355/why-is-the-super-constructor-necessary-in-pytorch-custom-modules)