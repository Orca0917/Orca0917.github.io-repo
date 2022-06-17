---
title:  "[PyTorch] PyTorch 모델의 모니터링"
excerpt: "Tensorboard, weight & biases 에 대한 소개"

categories:
  - pytorch
tags:
  - [AI, Naver, BoostCamp, Python, Pytorch]
toc: true
toc_sticky: true
 
date: 2022-01-26 01:00:00
last_modified_at: 2022-01-26 01:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Tensorboard

![image](https://user-images.githubusercontent.com/91870042/151142595-b4d9bafa-503b-4b1f-9df6-89518e71ce5d.png){: .align-center width="70%"}


텐서보드는 머신러닝 실헝에 있어서 필요한 **시각화 도구를 제공**해준다. 주요 특징은 다음과 같다.

- TensorFlow의 프로젝트로 만들어진 시각화 도구
- 계산 그래프, metric, 학습 결과를 시각적으로 보여준다
- TensorFlow 뿐만아니라 PyTorch도 연결해서 사용하는 것이 가능하다.
- 사실상, 딥러닝 시각화 도구의 표준이다.
- 실시간으로 훈련하는 도중에도 시각화 표현 가능

텐서보드가 공식적으로 제공하는 기능은 [텐서보드 홈페이지](https://www.tensorflow.org/tensorboard?hl=ko)를 보면 소개되어 있다. 텐서보드에서 저장가능한 값에는 `scalar`, `graph`, `histogram`, `image`, `mesh`가 존재하며 이외에도 `text`와 같은 것들이 존재한다.

- scalar: metric 등 상수값의 연속을 표시한다. (accuracy, loss, precision 등을 표현)
- graph: 모델의 **계산그래프**를 그려서 보여준다.
- histogram: weight등의 값을 분포로 표현한다. 어떠한 분포로 구성이 되어있는지 확인할 수 있다.
- image: 예측 값과 실제값을 비교할 때 사용한다.
- mesh: 3D 형태의 데이터를 표현해줄 수 있다.

```py
import os
import numpy
from torch.utils.tensorboard import SummaryWriter

logs_base_dir = "logs"  # Tensorboard의 기록을 위한 디렉토리를 생성한다.
os.makedirs(logs_base_dir, exist_ok=True)

writer = SummaryWriter(logs_base_dir)
for i in range(100):
    writer.add_scalar('Loss/train', np.random.random(), i)
    writer.add_scalar('Loss/test', np.random.random(), i)
    writer.add_scalar('Accuracy/train', np.random.random(), i)
    writer.add_scalar('Accuracy/test', np.random.random(), i)
writer.flush() # or writer.close()

%load_ext tensorboard   # jupyter에서 tensorboard 실행
%tensorboard --logdir {logs_base_dir} # 파일의 위치를 지정
```

<br>

# weight & biases

![image](https://user-images.githubusercontent.com/91870042/151141596-4ef364c6-1341-4aa3-bc92-20414a2386d5.png){: .align-center width="70%"}

- 머신러닝 실험을 원활히 지원하기 위한 상용도구
- 협업, code versioning, 실험 결과기록을 제공한다
- MLOps의 대표적인 도구

W&B에서 제공하는 대표적인 기능은 다음 4가지가 있다.
- Dashboard: 실험을 추적해서 시각화하여 보여준다.
- Artifacts: Dataset과 Model을 버전화시켜준다.
- Sweep: *Hyper Parameter를 최적화*시킨다.
- Report: 재현 가능 결과를 저장하거나 공유한다.


```py
import wandb

EPOCHS = 100
BATCH_SIZE = 32
LEARNING_RATE = 0.001

config={"epochs": EPOCHS, "batch_size": BATCH_SIZE, "learning_rate" : LEARNING_RATE}
wandb.init(project="my-test-project", config=config)

for e in range(1, EPOCHS+1):
    epoch_loss = 0
    epoch_acc = 0
    for X_batch, y_batch in train_dataset:
        X_batch, y_batch = X_batch.to(device), y_batch.to(device).type(torch.cuda.FloatTensor)
        optimizer.zero_grad()        
        y_pred = model(X_batch)
               
        loss = criterion(y_pred, y_batch.unsqueeze(1))
        acc = binary_acc(y_pred, y_batch.unsqueeze(1))
        
        loss.backward()
        optimizer.step()
        
        epoch_loss += loss.item()
        epoch_acc += acc.item()
        
        
    train_loss = epoch_loss/len(train_dataset)
    train_acc = epoch_acc/len(train_dataset)
    print(f'Epoch {e+0:03}: | Loss: {train_loss:.5f} | Acc: {train_acc:.3f}')
    wandb.log({'accuracy': train_acc, 'loss': train_loss})
```

<br>

# References

[🌏 W&B Dashboard](https://imgur.com/gallery/lApZE7b)

[📘 Weights & Biases를 통한 데이터 과학(Data Science) 실험 관리](https://wandb.ai/wandb_fc/korean/reports/Weights-Biases-Data-Science---Vmlldzo4MDEwNzc)

[📘 Tensorboard-텐서보드 시작하기](https://gooopy.tistory.com/98)

[📘 텐서보드: TensorFlow 시각화 도구](https://www.tensorflow.org/tensorboard?hl=ko)