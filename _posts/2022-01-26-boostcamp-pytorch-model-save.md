---
title:  "[PyTorch] 모델 저장, 불러오기"
excerpt: "PyTorch의 모델을 저장하고 불러오는 방법과 Transfer Learning을 활용한 모델의 학습 방법"

categories:
  - pytorch
tags:
  - [AI, Naver, BoostCamp, Python, Pytorch]
toc: true
toc_sticky: true
 
date: 2022-01-26 00:00:00
last_modified_at: 2022-01-26 00:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

PyTorch를 이용해서 직접 모델을 만들어서 학습시킨 경우, 종종 학습시킨 모델을 저장하거나 다른 사람이 잘 만들어놓은 모델을 불러와서 수정해서 쓰는 경우가 있다. 이번에는, 내가 만든 모델을 저장시키는 방법과 다른 모델을 불러와서 사용하는 방법에 대해서 알아본다.

<br>

# `torch.save()`: model의 저장

`torch.save()`를 이용해서 내가 생성한 모델의 학습 결과를 저장하는 것이 가능하며 다음 2가지를 저장시킬 수 있다.

1. 모델의 Architecture

    ```py
    torch.save(model, PATH)
    ```

    모델 자체를 저장하는 경우, Python의 `pickle`모듈을 사용하여 전체의 모듈을 저장한다. 이로 생기는 단점은 모델을 저장할 때, 사용한 특정 클래스나 구조에 얽매이게 된다. 이러한 이유로, 만들어둔 모델을 다른 프로젝트에서 사용하거나 수정하면 여러가지 이유로 제대로 동작하지 않는 경우가 생길 수 있다.

2. 모델의 Parameter

    ```py
    torch.save(model.state_dict(), PATH) # 모델을 저장할 때 권장되는 방식
    ```

위의 함수를 이용해서 저장시킨 모델의 확장자는 일반적으로 `.pt`, `.pth`로 설정하는데, `.pt`로 저장하는 것을 더 선호한다. `.pth`는 파이썬의 확장자에도 포함이 되어 있기 때문에 PyTorch의 확장자임을 알리는 `.pt`를 사용하는 것이 좋다.


## `state_dict()`가 하는 역할  
PyTorch에서 모델은 학습 가능한 변수인 가중치 또는 편향과 같은 정보를 모델의 Parameter로 저장하고 있다. 이 때, 각 모델의 Parameter는 `model.parameters()`로 접근이 가능하다.

`state_dict`는 하나의 모델에 포함된 레이어 별로, 방금 말한 Parameter와 Buffer 텐서를 매핑한 Python의 사전 객체이다.

```py
import torch
import torch.nn as nn

class TheModelClass(nn.Module):
    def __init__(self):
        super(TheModelClass, self).__init__()
        self.layer1 = nn.Sequential(
            nn.Conv2d(3, 16, kernel_size=3, stride=2, padding=0), 
            nn.BatchNorm2d(16), 
            
            nn.ReLU(), 
            nn.MaxPool2d(kernel_size=2, stride=2) 
        )
        self.drop_out = nn.Dropout() 
        self.fc1 = nn.Linear(3 * 3 * 64, 1000) 
        self.fc2 = nn.Linear(1000, 1)
        
    def forward(self, x):
        out = self.layer1(x)
        out = out.view(out.size(0), -1)
        
        out = self.drop_out(out)
        out = self.fc1(out)
        out = self.fc2(out)
        
        return out
    
model = TheModelClass().cuda()

print("✔ 1. Model's parameter name")
for name, params in model.named_parameters():
    print(name)

print("\n✔ 2. Model's buffer name")
for name, buffs in model.named_buffers():
    print(name)
    
print("\n✔ 3. Model's state_dict:")
for param_tensor in model.state_dict():
    print(param_tensor, '\t', model.state_dict()[param_tensor].size())
```
바로 위의 코드를 실행한 결과는 다음과 같다.
```text
✔ 1. Model's parameter name
layer1.0.weight
layer1.0.bias
layer1.1.weight
layer1.1.bias
fc1.weight
fc1.bias
fc2.weight
fc2.bias

✔ 2. Model's buffer name
layer1.1.running_mean
layer1.1.running_var
layer1.1.num_batches_tracked

✔ 3. Model's state_dict:
layer1.0.weight 	 torch.Size([16, 3, 3, 3])
layer1.0.bias 	 torch.Size([16])
layer1.1.weight 	 torch.Size([16])
layer1.1.bias 	 torch.Size([16])
layer1.1.running_mean 	 torch.Size([16])
layer1.1.running_var 	 torch.Size([16])
layer1.1.num_batches_tracked 	 torch.Size([])
fc1.weight 	 torch.Size([1000, 576])
fc1.bias 	 torch.Size([1000])
fc2.weight 	 torch.Size([1, 1000])
fc2.bias 	 torch.Size([1])
```
하나 주의해야할 것은 Parmeter로 등록되지 않고 **Buffer에 저장된 값**들도 모두 state_dict에 저장되는 것을 알 수 있다.

## 예시: 모델의 전체를 저장

```py
# Model은 위에서 구현한 TheModelClass를 사용
model = TheModelClass().cuda()

MODEL_PATH = "saved"
if not os.path.exists(MODEL_PATH):
    os.makedirs(MODEL_PATH)
torch.save(model, os.path.join(MODEL_PATH, "model.pt"))
```

## 예시2: 모델의 Parameter만 저장
```py
# Model은 위에서 구현한 TheModelClass를 사용
model = TheModelClass().cuda()

MODEL_PATH = "saved"
if not os.path.exists(MODEL_PATH):
    os.makedirs(MODEL_PATH)
torch.save(model.state_dict(), os.path.join(MODEL_PATH, "model.pt"))
```

<br>

# `torch.load()`: model 불러오기

```py
# 전체 모델을 저장한 겨웅
model = TheModelClass()
model = torch.load(MODEL_PATH)
model.eval()

# 모델의 state_dict를 저장
model = TheModelClass()
model.load_state_dict(torch.load(MODE_PATH))
model.eval()
```

위에서 `model.eval()`이 갖는 의미는 무엇일까. [🔍 What does model.eval() do in pytorch?](https://stackoverflow.com/questions/60018578/what-does-model-eval-do-in-pytorch/60018731#60018731)

`model.eval()`은 `nn.Module`에서 train_time과, eval_time에서 각각 다른작업을 수행하도록 만들어주는 함수이다. `model.eval()`을 설정하지 않았을 때, 잘못된 결과가 나올 수 있는 레이어는 **Dropout**, **BatchNorm** 레이어가 있다.

`model.eval()`을 사용하여 evaluation 하는 과정에서 사용하지 않아야 하는 레이어들을 알아서 사용하지 않도록 만들어주게 된다. 하지만 반드시 evaluation 과정이 끝나면 `model.train()`을 통해서 train모드로 다시 변경시켜야 한다.

<br>

# Checkpoints
어떠한 모델에서 추론이나 학습을 재개하거나 Early Stopping과 같은 기법을 적용해야 할 때, 학습의 결과물을 기록하는 것을 말한다.

Checkpoints에는 단순히 <u>모델의 state_dict뿐만아니라 Optimizer의 state_dict도 포함해서 저장</u>해야 한다. 필요에 따라서 중단 시점의 epoch, loss등 추가 정보를 저장할 수 있다.

여러가지 정보를 위해서 Python에서 제공하는 데이터 타입인 *Dict-type*으로 만든 후, 위에서 살펴본 `torch.save()`를 사용한다. 일반적으로 체크포인트를 저장할 때는 `.tar`확장자를 사용하는 것이 일반적인 규칙이다.

```py
# 추가 정보
EPOCH = 5
PATH = "saved/model.pt"
LOSS = 0.4

torch.save({
    'epoch': EPOCH,
    'model_state_dict': model.state_dict(),
    'optimizer_state_dict': optimizer.state_dict(),
    'loss': LOSS,
}, PATH)

model = TheModelClass()
optimizer = optim.SGD(model.parameters(), lr=0.1, momentum=0.9)

# Checkpoint에 저장한 정보 불러오기
checkpoint = torch.load(PATH)
model.load_state_dict(checkpoint['model_state_dict'])
optimizer.load_state_dict(checkpoint['optimizer_state_dict'])
epoch = checkpoint['epoch']
loss = checkpoint['loss']

# model.eval() or model.train() 실행
```

<br>

# Transfer Learning
딥러닝을 할 때, 직접 모델을 처음부터 끝까지 구현하지 않고 다른 사람이 잘 만들어 놓은 모델을 가져다가 사용하는 것이 일반적이다. 더 많은 데이터셋으로 부터 학습을 하는 것이 모델의 성능이 잘 나오고 이것을 가지고 와서 일부를 수정하여 가지고 있는 데이터셋의 좋은 결과를 얻게 하는 것이다. 

이렇게 불러온 모델을 가지고 학습시키는 것을 **Transfer Learning, 전이학습** 이라고 하며 잘 만들어진 모델은 `TorchVision`에서 공해주고 있다.

![image](https://user-images.githubusercontent.com/91870042/151110344-7c1e9373-654a-4c97-b636-39e944a84de7.png){: .align-center width="70%"}


## Freezing
이미 학습된 모델을 가지고 사용할 때, 모델의 일부분을 사용하지 않는 것을 말한다. 모든 레이어에 대해서 학습시키지 않고, 일부 레이어만 학습을 시키고자 할 때 사용한다.

```py
from torch import nn
from torchvision import models

class MyNewNet(nn.Module):   
    def __init__(self):
        super(MyNewNet, self).__init__()
        self.vgg19 = models.vgg19(pretrained=True)
        self.linear_layers = nn.Linear(1000, 1)


    # Defining the forward pass    
    def forward(self, x):
        x = self.vgg19(x)        
        return self.linear_layers(x)

device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')

my_model = MyNewNet()
my_model = my_model.to(device)

# ---------------- Freezing 부분 ------------------
for param in my_model.parameters():
    param.requires_grad = False

for param in my_model.linear_layers.parameters():
param.requires_grad = True
# -------------------------------------------------
```
<br>

# References

[📘 What does model.eval() do in pytorch?](https://stackoverflow.com/questions/60018578/what-does-model-eval-do-in-pytorch/60018731#60018731)

[📘 Pytorch에서 no_grad()와 eval()의 정확한 차이는 무엇일까?](https://coffeedjimmy.github.io/pytorch/2019/11/05/pytorch_nograd_vs_train_eval/)

[📘 SERIALIZATION SEMANTICS](https://pytorch.org/docs/stable/notes/serialization.html#saving-loading-tensors)

[📘 모델 저장하기 & 불러오기](https://tutorials.pytorch.kr/beginner/saving_loading_models.html)