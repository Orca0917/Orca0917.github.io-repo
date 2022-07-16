---
title:  "[PyTorch] Introduction to PyTorch"
excerpt: "딥러닝 프레임워크의 선두주자 중 하나인 PyTorch에 대한 소개"

categories:
  - pytorch
tags:
  - [AI, Naver, BoostCamp, Python, Pytorch]
toc: true
toc_sticky: true
 
date: 2022-01-24 00:00:00
last_modified_at: 2022-01-24 00:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

이전까지 배웠던, python의 기본문법과 딥러닝의 개념만 가지고 실제로 사용되는 프로젝트에서 구현을 하려면 매우 힘든길이 된다. 따라서 이미 구현된 다양한 내장함수를 이용하여 더 쉽고 간편하게 구현할 수 있는 프레임 워크의 도움을 받을 수 있다.

이런 딥러닝 프레임워크는 `PyTorch`뿐만 아니라 `TensorFlow`, `Keras`, `Microsoft CNTK`, `theano`등 다양한 종류가 있다. 그중에서 현재 많은 사람들에게 사랑받는 프레임워크는 Pytorch와 Tensorflow인데, 최근에는 PyTorch가 더 많은 주목을 받고 있다고 한다.

# Keras, TF, PyTorch
![image](https://user-images.githubusercontent.com/91870042/150709202-fbfe5493-41eb-4225-a5c1-2974b6330947.png){: .align-center}

<br>

# TF, PyTorch
두 프레임워크의 차이점은 많이 존재하지만 그 중에서 가장 눈여겨 볼 수 있는 것은 **계산 그래프**이다.

- `TensorFlow`: 계산 그래프를 먼저 정의하고 실행하는 **Define and Run**의 과정을 진행한다.
- `PyTorch`: 코드를 실행하면서 그래프를 생성하는 방식인 **Define by Run**의 과정을 진행한다. 특히 이렇게 생성되는 그래프를 Dynamic Computational Graph (DCG)라고 부른다.

<br>

# PyTorch의 장점
- Define By Run이 가능함으로써 결과를 즉시확인이 가능하다. 또한 pythonic code의 작성이 가능해진다.
- CPU의 지원과 API그리고 커뮤니티가 잘되어 있는것이 특징이다.
- 무엇보다도 사용자가 사용하기 편리하다는 점이 있다.
- `자동미분`을 지원하여 딥러닝 연산을 지원한다.
- 다양한 형태의 딥러닝의 계산을 도와주는 함수와 모델을 지원한다.

✔ 자동미분(Automatic Differentiation, auto-grad)<br>
컴퓨터 프로그램에 의해서 구체화된 함수의 미분을 산술적으로 계산할 때 사용하는 기술의 집합을 말하며 특히 오차역전파법에서 미분연쇄법칙을 이용함으로써 자동으로 복잡한 함수를 정확하고 효율적으로 미분하는 것이 가능하다.
{: .notice--success}

<br>

# References

[📘 딥러닝 프레임워크 비교 분석 PyTorch vs Keras vs TensorFlow](https://blog.naver.com/rlaghlfh/221494731829)

[📘 [TensorFlow] Gradient Tape을 이용한 자동 미분과 오차역전파](https://rfriend.tistory.com/556)
