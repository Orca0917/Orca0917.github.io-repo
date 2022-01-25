---
title:  "[PyTorch] DataSet & DataLoader"
excerpt: "데이터 입력형태를 정의하는 Dataset과 Batch단위로 데이터를 로딩하는 DataLoader"

categories:
  - pytorch
tags:
  - [AI, Naver, BoostCamp, Python, Pytorch]
toc: true
toc_sticky: true
 
date: 2022-01-25 01:00:00
last_modified_at: 2022-01-25 01:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

딥러닝 모델을 학습시키기 위해서 굉장히 방대한 양의 데이터를 이용하다보니, 시간이 매우 오래걸려서 비효율적인 일이 발생한다. 모든 데이터셋을 이용해서 학습하는 것이 아니라 필요에 따라서 필요한 데이터들만 불러서 학습시킬 수 있는 **Custom Dataset**이 필요해졌다. 또한 가변적인 길이의 input에 대해 batch를 생성하기 위해서 **custom Dataloader**가 졌다.

![image](https://user-images.githubusercontent.com/91870042/151001981-d60079cf-fe54-4367-b29b-eac1a3951ce3.png){: .align-center}

아래는 딥러닝 모델을 학습하는 전체적인 과정을 보여준다.

![image](https://user-images.githubusercontent.com/91870042/150999927-9ea1f35c-60ee-4664-b2bf-e422d654d314.png){: .align-center}

# Dataset
`Dataset`클래스는 데이터의 입력 형태를 정의하는 클래스이며 여러가지 종류의 입력에 대해서 동일하게 처리한다. 이렇기 때문에 데이터의 입력하는 방식에 대한 표준화라고 부르기도 한다. 직접 재정의하여 이미지, 텍스트, 오디오에 대해서 다른 입력을 정의할 수 있다. 다음은 `Dataset`의 기본 구성이다.

```py
# Map-style dataset 예제
import torch
from torch.utils.data import Dataset

class CustomDataset(Dataset):
    def __init__(self, text, labels): 
        self.data = text
        self.label = labels

    def __len__(self):
        return len(self.label)

    def __getitem__(self, idx):
        text = self.data[idx]
        label = self.label[idx]
        sample = {"text": text, "Class": label}
        return sample
```

기본적으로 customDataset을 생성할 때, 다음 3가지 함수로 구성이 된다. 이러한 방식은 `Map-style dataset`이라고 부른다. Dataset을 생성할 때는 데이터의 형태에 따라서 각 함수를 다르게 정의해야한다. Dataset의 동작과정을 보면, 처음에 데이터를 입력을 받고 설정을 초기화한 후, `__getitem__`을 이용해서 데이터를 처리한다.

- \_\_init\_\_  
    
    `__init__`은 전반적으로 필요한 변수를 선언하며 데이터의 위치, 파일명을 초기화하고 불러오는 작업을 수행한다. 이렇게 하게되면 모든 데이터를 메모리에 올리지 않아도 되서 효율적이다. 또한, 이미지를 처리하는 transforms 들도 이곳에서 정의한다. 그 개수가 여러 개인경우 `Compose`를 통해서 정의한다.
- \_\_len\_\_ 

    불러온 데이터의 길이를 반환한다. 일반적으로 저장한 `self.data`와 `self.label`의 길이가 동일하므로 `len(self.label)`로 반환한다.
- \_\_getitem\_\_

    불러온 데이터에서 입력으로 받은 `index`번째 데이터를 반환할 수 있도록 만들어야 하며, 반환값의 데이터 타입은 **텐서**형식이어야 한다.

<br>

# DataLoader
`DataLoader`는 Dataset에서 생성된 데이터를 **배치단위**로 학습시키는 역할을 한다. DataLoader는 GPU에게 연산을 시키기 직전의 데이터 변환을 담당한다. 주로, 데이터의 Tensor로의 변환이나 Batch처리가 주된 목적이다. 

![image](https://user-images.githubusercontent.com/91870042/151015183-88068dd9-212c-408c-935d-251e38c5d1be.png){: .align-center}

PyTorch에 작성된 DataLoader의 정의를 살펴보면 다음과 같다.

```py
DataLoader(dataset, batch_size=1, shuffle=False, sampler=None,
           batch_sampler=None, num_workers=0, collate_fn=None,
           pin_memory=False, drop_last=False, timeout=0,
           worker_init_fn=None, *, prefetch_factor=2,
           persistent_workers=False)
```

- dataset: 위에서 생성한 dataset이 전달된다.
- batch_size: 배치단위의 크기를 결정한다.
- shuffle: 배치단위로 생성된 데이터를 섞어서 사용할지 결정한다.
- sampler: 데이터의 index를 원하는 방식대로 조절한다.
- batch_sampler: 위와 동일
- num_workers: 멀티프로세싱을 하면서 데이터로딩에 사용하는 서브 프로세스의 개수를 결정한다.
- collate_fn: Map-style Dataset에서 sample list를 배치단위로 바꾸는 역할을 하며 zero-padding, Variable Size 데이터 등 사이즈를 맞추기 위해서 사용한다.
- pin_memory: 텐서를 CUDA의 고정메모리에 올릴지 결정한다.
- drop_last: 데이터가 10개인데 배치사이즈가 3이라면, 마지막 배치는 그 크기가 1이된다. 이렇게 남은 배치는 loss를 구하기 힘들어지는 경우가 생길 수 있기 때문에 사용하지 않게 설정할 수 있다.
- time_out: DataLoader가 데이터를 불러오는데 제한시간을 부여한다

## RandomSampler와 Shuffle
```py
# 1. Shuffle을 사용하는 경우
DataLoader(CustomDataset, batch_size=10, shuffle=True)

# 2. Sampler를 사용하는 경우
randSampler = RandomSampler(CustomDataset)
DataLoader(CustomDataset, batch_size=10, sampler=randSampler)
```

위의 상황에서 발생하는 차이점을 알기 위해서, `DataLoader`클래스에 존재하는 `__init__`부분에서 각각 `shuffle`과 `sampler`가 어떻게 적용되는지 살펴보았다.

1. Shuffle=True 가 적용되었을 때
   
    ```py
    if sampler is None:  # give default samplers
        if self._dataset_kind == _DatasetKind.Iterable:
            # See NOTE [ Custom Samplers and IterableDataset ]
            sampler = _InfiniteConstantSampler()
        else:  # map-style
            if shuffle:
                sampler = RandomSampler(dataset)
            else:
                sampler = SequentialSampler(dataset)
    ```
    Dataset에서 Map-style Dataset으로 정의하고, `shuffle=True` 옵션을 주면 내부에서는 `RandomSampler` 를 사용하는 것을 알 수 있다.

2. sampler=RandomSampler(Dataset)가 적용되었을 때
   
    ```py
    self.batch_size = batch_size
    self.drop_last = drop_last
    self.sampler = sampler
    self.batch_sampler = batch_sampler
    ```
    sampler가 정의되어서 넘겨지게 되면 (위에서 shuffle에 의해서 정의된 것 포함) `self.sampler`에 할당되는 것을 확인할 수 있다.

3. sampler가 정의되었음에도 shuffle=True가 된 경우

    ```py
    if sampler is not None and shuffle:
        raise ValueError('sampler option is mutually exclusive with '
                        'shuffle')
    ```
    ValueError를 발생시킴을 알 수 있다.

    ![image](https://user-images.githubusercontent.com/91870042/151020407-0933af67-1540-48cc-bca6-a434a32c7ea0.png){: .align-center}


## 예제
```py
import torch
from torch.utils.data import Dataset

class CustomDataset(Dataset):
    def __init__(self, text, labels): 
        self.data = text
        self.label = labels

    def __len__(self):
        return len(self.label)

    def __getitem__(self, idx):
        text = self.data[idx]
        label = self.label[idx]
        sample = {"text": text, "Class": label}
        return sample

text = ['Happy', 'Amazing', 'Sad', 'Unhappy', 'Glum']
labels = ['Positive', 'Positive', 'Negative', 'Negative', 'Negative']

MyDataset = CustomDataset(text, labels)
MyDataLoader = DataLoader(MyDataset, batch_size=2, shuffle=True)

for dataset in MyDataLoader:
    print(dataset)

# 데이터에 대해서 크기가 2인 배치로 입력을 받았으며, 데이터의 shuffle이 이루어짐
>>> {'text': ['Glum', 'Amazing'], 'Class': ['Negative', 'Positive']}
    {'text': ['Happy', 'Unhappy'], 'Class': ['Positive', 'Negative']}
    {'text': ['Sad'], 'Class': ['Negative']}
```

<br>

# References

[📘 TORCH.UTILS.DATA](https://pytorch.org/docs/stable/data.html)

[📘 [Pytorch] DataLoader parameter별 용도](https://subinium.github.io/pytorch-dataloader/)

[📘 [Pytorch] 파이토치의 Custom dataset과 DataLoader 이해하기](https://didu-story.tistory.com/85)

[📘 [PyTorch] dataset과 dataloder 설명 및 custom dataset & dataloader 만들기](https://sanghyu.tistory.com/90)