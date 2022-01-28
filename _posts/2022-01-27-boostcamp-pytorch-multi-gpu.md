---
title:  "[PyTorch] Multi-GPU 학습"
excerpt: "PyTorch에서 Multi GPU를 사용하기 위해 딥러닝의 모델과 데이터를 병렬화 하는 과정"

categories:
  - pytorch
tags:
  - [AI, Naver, BoostCamp, Python, Pytorch]
toc: true
toc_sticky: true
 
date: 2022-01-27 00:00:00
last_modified_at: 2022-01-27 00:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

이번에는 PyTorch를 이용해서 Multi-GPU를 학습하는 방법에 대해서 소개한다. 이게 중요한 이유는 옛날에는 적은 데이터로 학습을 진행하고 구조를 단순화하여 메모리를 적게 사용하도록 했다면 요즘에는 데이터의 양이 많아졌고, 뛰어난 성능의 GPU가 있어 이를 많이 사용해서 더 성능이 좋은 모델을 만드는 것이 일상화가 되어가기 때문이다.

성능이 좋은 모델을 만들기 위해서 지금 당장 GPU가 1개 있다고 하더라도 모델을 학습하지 못하거나 정말 오랜 시간이 걸리게 된다. 그렇기 때문에 여러개의 GPU를 사용한 Multi-GPU학습 방법에 대해서 알아봐야 한다.

# 기본 Concpet
- single GPU vs multi GPU : 하나의 GPU, 2개 이상의 GPU
- GPU vs node: GPU 1개, 컴퓨터(System) 1개
- Single Node Single GPU: 하나의 컴퓨터에 하나의 GPU
- Single Node Multi GPU: 하나의 컴퓨터에 여러개의 GPU
- Multi Node Multi GPU: 여러개의 컴퓨터에 여러개의 GPU (서버실)

<br>

# Model Parallel, Data Parallel

다중 GPU를 이용해서 학습하는 방법에는 2가지가 존재한다. 하나는 **모델을 병렬화**하여 학습시키는 것이고, 다른 하나는 **데이터를 병렬화**하여 학습시키는 것이다.

- `Model Parallel` : 모델을 나눠서 병렬적으로 학습
- `Data Parallel` : 데이터를 나눠서 병렬적으로 학습

![image](https://user-images.githubusercontent.com/91870042/151470401-d178e4b9-dbba-4990-8d6d-f3610d0fa7c2.png){: .align-center width="70%"}


## Model Parallel

여러개의 GPU를 사용하면 모델의 학습을 각각 분산시키게 되어 효율을 더 높일 수 있다. 하지만, 모델의 파이프라인에서 병목현상이 발생할 수 있기 때문에 모델의 병렬화는 고난이도 과제라고 평가받고 있다.

✔ 파이프라인(pipe line)<br>
머신러닝 파이프라인은 데이터 수집부터 전처리, 학습 모델 배포, 예측까지의 모든 과정을 순차적으로 처리하도록 설계된 머신러닝의 구조이다. 이는 기계학습 모델을 생성하는데 필요한 workflow를 체계화하고 자동화하는 방법이다. 다음은 ML Pipeline의 예시이다.
{: .notice--success}

![image](https://user-images.githubusercontent.com/91870042/151323432-61dff34a-2867-4d55-bb18-eb71014d76ea.png){: .align-center width="70%"}

---

모델의 병렬화를 진행할 때 생길 수 있는 문제점은 GPU가 각 모델을 학습시키지만 **서로 겹치지 않게 학습이 되는 경우**이다. 이렇게 되면 병렬화하는 효과를 받을 수 없다. 모델의 병렬화를 사용하면서 효과적으로 빠르게 학습시키기 위해서는 데이터를 각 GPU에서 처리하되 겹치게 처리를 해야한다.

![image](https://user-images.githubusercontent.com/91870042/151324265-d3096dbd-ebd3-4044-947d-ced1140d01c6.png){: .align-center width="70%"}

```py
class ModelParallelResNet50(ResNet):
    def __init__(self, *args, **kwargs):
        super(ModelParallelResNet50, self).__init__(
            Bottleneck, [3, 4, 6, 3], num_classes=num_classes, *args, **kwargs
        )

        # 첫번째 모델을 cuda:0 에 할당
        self.seq1 = nn.Sequential(
          self.conv1, self.bn1, self.relu, self.maxpool, self.layer1, self.layer2
        ).to('cuda:0')

        # 두번째 모델을 cuda:1 에 할당
        self.seq2 = nn.Sequential(
          self.layer3, self.layer4, self.avgpool
        ).to('cuda:1')

        self.fc.to('cuda:1')
    
    # 두 모델의 연결
    def forward(self, x):
      x = self.seq2(self.seq1(x).to('cuda:1'))
      return self.fc(x.view(x.size(0), 1))
```

## Data Parallel

데이터를 병렬화하여 GPU에 나눠서 할당한 후, 결과의 평균을 취하는 방법을 사용한다. 미니배치의 수식과 유사한점을 갖지만 Data Parallel에서는 한 번에 여러개의 GPU에서 수행한다는 점이 다르다.

![image](https://user-images.githubusercontent.com/91870042/151471838-ca3e1ead-1ec3-4d05-b043-bf02c3cff4a6.png){: .align-center}

1. Scatter mini-batch inputs to GPUs: 데이터를 나누어서 각 GPU에게 할당한다.
2. Replicate model on GPUs: 학습에 사용할 모델을 각 GPU에게 복사한다.
3. Parallel forward passes: GPU마다 입력받은 데이터에 대해 병렬적으로 연산을 수행한다.
4. Gather outputs on GPU-1: 1번 GPU에게 연산의 결과를 모두 합친다. 
5. Compute loss gradients on GPU-1: 1번 GPU에서 받은 모든 연산결과에 대해서 loss값을 계산한다.
6. Scatter gradients to GPUs: 연산된 loss값을 다시 각 GPU에게 돌려준다.
7. Parallel backward passes: 각각의 GPU에서 받은 loss값을 가지고 역전파를 수행하고 gradient값을 구한다.
8. Reduce gradients to GPU-1: GPU-1에서 구해진 gradient 값들을 모두 더해서 평균을 낸다.

<br>
PyTorch에서는 위의 Data Parallel을 수행하기 위해서 2가지 방식을 제공한다.

- `DataParallel` : 데이터를 분배한 후 평균을 취해서 계산한다. GPU사용에서 위에서 볼 수 있듯이 1번 GPU가 혼자서 많이 일하는 것과 같은 불균형의 문제가 발생한다. 따라서 batch 크기를 적게만들어야 한다. `GIL`문제.
- `DistributedDataParallel` : 각 CPU마다 process를 생성하여 개별 GPU에게 할당한다. 기본적으로 DataParallel을 하지만 개별적으로 연산의 평균을 구한다. (각각의 GPU에서 forward, backward 모두 수행.)

✔ GIL(Global-Interface-Lock)<br>
GIL은 하나의 therad를 사용해서 Python Interpreter를 제어하는 mutex이다. 이로서 한번에 하나의 기본 thread만 실행하는 것이 가능해진다. 이것이 생긴 이유는 다른 thread로 부터 안전하지 않은 코드를 공유하는 것을 피하기 위해서 있는데, OS의 mutex/lock 이라고 생각하면 된다.
{: .notice--success}

### DataParallel

```py
parallel_model = torch.nn.DataParallel(model)

predictions = parallel_model(inputs) # Forward
loss = loss_function(predictions, labels) # compute loss(cost)
loss.mean().backward() # average, backward
optimizer.step() # optimizer step

predictions = parallel_model(inputs) # forward
```

### DistributedDataParallel
```py
train_sampler = torch.utils.data.distributed.DistributedSampler(train_data)

train_loader = torch.utils.data.DataLoader(train_data, batch_size=20, shuffle=False, pin_memory=True, num_workers=3, sampler=train_sampler)

from multiprocessing import Pool

def f(x):
  return x ** 2

def main():
  n_gpus = torch.cuda.device_count()
  toroch.multiprocessing.spawn(main_worker, nprocs=n_gpus, args=(n_gpus, ))

def main_worker(gpu, n_gpus):

  image_size = 224
  batch_size = 512
  num_worker = 8
  epochs = 100

  batch_size = batch_size // n_gpus
  num_worker = num_worker // n_gpus

  torch.distributed.init_process_group(
    backend='nccl', init_method='tcp://127.0.0.1:2568', world_size=n_gpus, rank=gpu
  )

  model = MODEL

  torch.cuda.set_device(gpu)
  model = model.cuda(gpu)
  model = torch.nn.parallel.DistributedDataParallel(model, device_ids=[gpu])

if __name__ == '__main__':
  with Pool(5) as p:
    print(p.map(f, [1, 2, 3]))
```

✔ Pin-memory<br>
데이터를 CPU로 읽은 다음 GPU로 보내기 위해서는 GPU와 통신하기 위한 CPU의 메모리 공간이 필요하다. 이때 메모리를 할당 시키는 기법을 memory pinning이라고 한다. 이 memory pinning을 하는데 사용하는 메모리의 종류는 pinned memory와 pageable memory가 있는데 일반적으로 pinned memory가 더 빠르다.
{: .notice--success}

![image](https://user-images.githubusercontent.com/91870042/151473883-06c6c98d-11f0-489b-8957-2c6f8234fb1f.png){: .align-center width="70%"}

<br>

# References

[📘 Global interpreter lock - Wikipedia](https://en.wikipedia.org/wiki/Global_interpreter_lock)

[📘 Python의 GIL](https://kimeuichan.github.io/posts/python-gil/)

[📘 머신러닝 파이프라인이란? - ML Pipeline에 대하여](https://lsjsj92.tistory.com/579)

[📘 머신러닝 파이프라인 (Machine Learning Pipeline)](http://blog.skby.net/%EB%A8%B8%EC%8B%A0%EB%9F%AC%EB%8B%9D-%ED%8C%8C%EC%9D%B4%ED%94%84%EB%9D%BC%EC%9D%B8-machine-learning-pipeline/)

[📘 num_workers & pin_memory in DataLoader](https://cvml.tistory.com/24)
