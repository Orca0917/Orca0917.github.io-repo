---
title:  "[PyTorch] Hyperparameter Tuning"
excerpt: "PyTorch에서 초매개변수를 최적으로 설정하도록 도와주는 Ray Tune 프레임워크와 기본적인 Grid & Random, Bayesian parameter search 방법론에 대한 소개"

categories:
  - pytorch
tags:
  - [AI, Naver, BoostCamp, Python, Pytorch]
toc: true
toc_sticky: true
 
date: 2022-01-27 01:00:00
last_modified_at: 2022-01-27 01:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

사람이 직접 설정하는 매개변수인 **Hyper parameter**를 어떻게 설정해야 정확도를 높일 수 있을지, 어떻게 조정할지에 대해 Ray Tune모듈을 사용하여 알아본다.

# 정확도를 높이는 방법

정확도를 높이기 위해서는 크게 3가지 방법이 존재한다.

1. 모델을 바꾸기
2. 데이터의 추가 / 변경
3. Hyperparameter Tuning

일반적으로 위에서 가장 효과적으로 정확도를 높일 수 있는 방법은 데이터를 추가하거나 변경하는 작업이다. 그 다음으로 효과적인 것은 모델이고 마지막으로 마지막 0.01% 까지 올리기 위해서 Hyper parameter Tuning을 사용한다.

> 데이터 > 모델 > Hyper Parameter

모델은 일반적으로 문제에 대해서 거의 고정이 되어서 사용되기 때문에 일반적으로 좋은 데이터를 이용해서 학습하는 것이 일반적이다.

<br>

# Hyperparameter Tuning

모델 스스로 학습하지 않는 값을 사람이 직접 지정하는 것을 hyper parameter라고 한다. 대표적으로 학습률(learning rate), 모델의 크기, optimizer등이 있다. 예전에는 Hyper parameter를 수정하여 성능이 크게 좌우되었지만 최근에는 많은 데이터로 이것을 극복해 나가고 있다.

---

![image](https://user-images.githubusercontent.com/91870042/151476866-85e7c020-656d-46e9-8a32-ba53c757f4c7.png){: .align-center width="70%"}

## Grid Sampling
**그리드 샘플링**은 불연속 hyper parameter를 지원하며 작동원리는 hyper parameter 각각에 적용할 값들을 정해두고 모든 조합에 대해서 어떠한 성능이 나오는지 기록한 후, 최고의 성능을 내는 hypter parameter로 설정하는 방법이다.

이 그리드 샘플링은 간단하지만, hyper parameter의 개수가 많아지게 되면 필요한 연산횟수가 매우 많아지게 되어 tuning하는데 있어서 어려움을 겪을 수 있다.

## Random Sampling
**랜덤 샘플링**은 hyper parameter로 설정할 값을 미리 정해두는 것이 아니라 각 매개변수가 가질 수 있는 최솟값과 최댓값을 지정한 후, 그 내에서 무작위로 추출하여 성능을 확인하는 것이다. 그리드 샘플링과 유사한 구조를 갖지만, 단지 무작위로 매개변수를 추출하여 시도한다는 것이 차이점을 갖는다.

일반적으로 시간대비 성능은 랜덤 샘플링이 더 좋다고 평가되고 있다.

## Bayesian Sampling
**Bayesian 샘플링**은 Bayesian최적화 알고리즘을 기반으로 작동한다. 핵심은 이전 샘플에서 수행한 방식을 기반으로 샘플을 선택하여 새로 선택한 샘플이 기본 metric을 향상 시키도록 한다. 이런 아이디어를 `베이즈이론`과 `가우시안 프로세스`에 적용시켜서 만든 것이 Bayesian Sampling이다.

<br>

# Ray
multi-node, multi processing을 지원하는 모듈로서 ML/DL의 병렬처리를 위해서 개발이 되었다. 현재 분산병렬 ML/DL의 표준이며 우리가 원하는 hyperparameter search를 위한 다양한 모듈을 제공한다.

```py
from ray import tune
from ray.tune import CLIReporter
from ray.tune.schedulers import ASHAScheduler

data_dir = os.path.abspath("./data")
load_data(data_dir)

# Config에 search space를 지정한다.
config = {
    'l1': tune.sample_from(lambda _: 2 ** np.random.randint(2, 9))
    'l2': tune.sample_from(lambda _: 2 ** np.random.randint(2, 9))
    'lr': tune.loguniform(1e-4, 1e-1),
    'batch_size': tune.choice([2, 4, 8, 16])
}

# 학습 스케줄링 알고리즘 설정
scheduler = ASHAScheduler(
        metric="loss",
        mode="min",
        max_t=max_num_epochs,
        grace_period=1,
        reduction_factor=2)

# 결과 출력 양식 지정
reporter = CLIReporter(
        metric_columns=["loss", "accuracy", "training_iteration"])

# 병렬 처리 양식으로 학습 실행
# train_cifar -> 학습하는 과정이 "함수"로 구현이 되어 있어야 한다.
result = tune.run(
        partial(train_cifar, data_dir=data_dir),
        resources_per_trial={"cpu": 2, "gpu": gpus_per_trial},
        config=config,
        num_samples=num_samples,
        scheduler=scheduler,
        progress_reporter=reporter)

# 최적의 성능을 내는 매개변수, 오차, 정확도 출력
best_trial = result.get_best_trial("loss", "min", "last")
    print("Best trial config: {}".format(best_trial.config))
    print("Best trial final validation loss: {}".format(
        best_trial.last_result["loss"]))
    print("Best trial final validation accuracy: {}".format(
        best_trial.last_result["accuracy"]))
```

<br>

# References

[📘 Hyperparameter optimization](https://blog.naver.com/PostView.naver?blogId=cjh226&logNo=221408767297&categoryNo=16&parentCategoryNo=0&viewDate=&currentPage=1&postListTopCurrentPage=1&from=postView)

[📘 4. 머신러닝 알고리즘 : 확률론과 나이브 베이즈(Naive Bayes)분류](https://nittaku.tistory.com/283)

[📘 Azure Machine Learning을 사용하여 모델을 튜닝하는 하이퍼 매개 변수](https://docs.microsoft.com/ko-kr/azure/machine-learning/how-to-tune-hyperparameters)

