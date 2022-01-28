---
title:  "[PyTorch] Troubleshooting"
excerpt: "PyTorch를 사용하면서 자주 발생하는 OOM 에러와 이를 해결하는 방법"

categories:
  - pytorch
tags:
  - [AI, Naver, BoostCamp, Python, Pytorch]
toc: true
toc_sticky: true
 
date: 2022-01-27 02:00:00
last_modified_at: 2022-01-27 02:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# OOM (Out Of Memory)
OOM에러는 왜 발생했는지 알기가 어렵고, 어디서 발생했는지 알기어렵다. 또한 Error backtraking을 하게 되면 전혀 모르는 깊은 곳에서 발생했다고 나오며, 메모리 에러가 발생한 이전상황을 파악하는 것이 어렵다.

1차적인 해결방법은 먼저, 배치크기를 줄이고 GPU의 캐시를 지우고 다시 실행시키는 과정을 거친다. 하지만 이외에도 안되는 경우에는 학습하는데 있어서 발생할 수 있는 원인들을 살펴볼 필요가 있다.

## GPU Util(GPUtil)
GPUtil은 nvidia-smi 처럼 GPU의 상태를 보여주는 모듈이며 각 iteration마다 메모리가 늘어나는지 확인하기 용이하다. (nvidia-smi는 iteration이 돌면서 메모리가 증가하는 것을 확인하는 것이 불가능하다. "현재시점의 상태만 보여준다!")

```py
import GPUtil
GPUtil.showUtilization()

>>> | ID | GPU | MEM |
    ------------------
    |  0 | 13% | 11% |
```

## empty_cache()
`torch.cuda.empty_cache()`를 사용해보는 것이 도움이 될 수 있다. 해당 코드를 실행하게 되면 사용되지 않는 GPU의 캐시르 정리하고 사용할 수 있는 메모리를 확보한다. `del` 키워드는 단순히 연결관계만 끊어주기 때문에 메모리 사용량을 줄여주지는 않는다.

예를 들어, `backward()`가 일어날때, 메모리 버퍼에 계속 데이터가 누적되어서 OOM이 발생할 수도 있다.

```py
import torch
from GPUtil import showUtilization as gpu_usage

print('Initial GPU Usage')
gpu_usage()

tensorList = []
for x in range(10):
    tensorList.append(torch.randn(10000000, 10).cuda())

print('GPU Usage after allocationg a bunch of Tensors')
gpu_usage()

del tensorList
print('GPU Usage after deleting the Tensors')
gpu_usage()

print('GPU Usage after emptying the cache')
torch.cuda.empty_cache()
gpu_usage()

>>> Initial GPU Usage
    | ID | GPU | MEM |
    ------------------
    |  0 |  1% | 12% |

    GPU Usage after allocationg a bunch of Tensors
    | ID | GPU | MEM |
    ------------------
    |  0 | 42% | 58% |

    GPU Usage after deleting the Tensors
    | ID | GPU | MEM |
    ------------------
    |  0 | 42% | 58% |

    GPU Usage after emptying the cache
    | ID | GPU | MEM |
    ------------------
    |  0 | 42% | 11% |
```

## 반복문 내에 축적되는 tensor 변수
`torch.tensor`로 처리된 변수는 GPU내에 있는 메모리 공간을 사용한다. 이 변수는 반복문내에 연산이 있는 경우, GPU에 계산 그래프를 생성해서 메모리를 잠식하는 현상이 나타난다.

```py
# 예시
total_loss = 0
for _ in range(10000):
    optimizer.zero_grad()
    predict = model(train_x)
    cost = criterion(predict)
    cost.backward()
    optimizer.step()
    total_loss += loss
```

### `.item()`의 사용
위의 문제를 해결하기 위해서, 1차원의 텐서인경우 `.item()`을 사용해서 python의 기본객체로 변환하여 처리하는 것이 가능하다.
```py
total_loss = 0
for _ in range(10000):
    loss = torch.randn(3, 4).mean()
    loss.requires_grad = True
    total_loss += loss.item() # total_loss += float(loss)
```

### `del`의 사용
반복문 내에서만 실행되고 반복문 밖에서는 필요없는 변수는 적절한 삭제가 필요하다. Python의 메모리 배치 특성상 반복문이 종료해도 메모리를 차지하기 때문에 `del`키워드를 사용해서 메모리 관계를 끊어주는 것이 좋다.

```py
for x in range(10):
    i = x
print(i) # 9 -> 반복문 외에서도 변수 값이 출력된다.
```
```py
# del을 사용한 메모리의 효율적 사용
for i in range(10):
    intermediate = f(input[i])
    result += g(intermediate)

del intermediate
output = h(result)

return output
```

## batch 크기 줄이기
너무 큰 크기의 배치사이즈가 배정되어서 OOM이 발생될 수 있기 때문에, 최소 1까지 시험을 해본다.
```py
oom = False
try:
    run_model(batch_size)
except RuntimeError:
    oom = True

if oom:
    for _ in range(batch_size)
        run_modle(1)
```

## `torch.no_grad()`
추론하는 단계에서는 gradient의 계산이 필요하지 않기 때문에, 이전의 포스팅에서 말했던 것과 같이 `torch.no_grad()`를 사용한다. 이렇게 되면 역전파에서 발생할 수 있는 메모리의 버퍼에서 부터 자유로워지는 것이 가능하다.

```py
with torch.no_grad():
    for data, target in test_loader:
        output = network(data)
        test_loss += F.nll_loss(output, target, size_average=False).item()
        predict = output.data.max(1, keepdim=True)[1]
        correct += pred.eq(target.data.view_as(pred)).sum()
```

## etc.
- Google Colab 환경에서는 너무 큰 사이즈의 모델을 실행하지 않는것이 좋다. ex. linear, CNN, LSTM
- CNN의 대부분의 에러는 input, output의 크기가 맞지 않아서 발생하는 경우가 대부분이다. 따라서 `torchsummary`로 사이즈를 맞추어줘야 한다.
- 텐서의 float precision값을 16비트로 줄여서 사용한다. (하나의 값이 차지하는 메모리의 크기를 줄여준다.)

<br>

# References

[📘 이유를 알 수 없는 GPU 에러 정리(device-side assert, CUDA error, CUDNN_STATUS_NOT_INITIALIZED 등등…)](https://brstar96.github.io/shoveling/device_error_summary/)