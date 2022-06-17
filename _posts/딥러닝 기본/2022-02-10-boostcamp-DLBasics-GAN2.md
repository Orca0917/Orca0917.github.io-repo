---
title:  "[Deep Learning Basic] Generative Models Part2"
excerpt: "VAE(Variational Auto Encoder)와 GAN(Generative Adversarial Network) 대한 설명"

categories:
  - DLBasic
tags:
  - [AI, Naver, BoostCamp, Python, Math]
toc: true
toc_sticky: true

date: 2022-02-10 01:00:00
last_modified_at: 2022-02-10 01:00:00
---
📌 **알립니다!**<br>
이번에 작성되는 글은 **네이버 부스트캠프 AI Tech**를 수강하며 정리하는 글입니다.<br>
여기서 존재하는 강의 자료의 출처는 네이버 부스트코스/캠프에게 있습니다.
{: .notice--info}

# Latent Variable Models

- autoencoder는 generative model일까? → 그렇지 않다.
- 그러면 무엇때문에 variational auto-encoder는 generative model이 될 수 있을까?

<br>

# Variational Auto-encoder

- Variational Inference(VI)의 목적: **posterior distribution** 을 가장 잘 근사하는 **variational distribution** 찾기
    - **posterior distribution**: 나의 observation이 주어졌을 때, 내가 관심있어하는 확률변수의 확률분포 → 계산하기 어렵고, 불가능할때가 많다. 수식으로는 $$p_\theta(z\mid x)$$
    - **Variational distribution**: posterior distribution을 근사하는 분포. 수식으로는 $$q_\phi(z\mid x)$$
- 어떤걸 근사하는 것을 찾는다고 하면, 이 때 필요한 것은 loss function이다.
    - Variational Inference는 loss function으로 KL-divergence(쿨백-라이블러 발산)을 사용한다.
    - 결국, KL-divergence로 posterior distribution과 variational distribution의 차이를 줄이고자 함

![image](https://user-images.githubusercontent.com/91870042/153741633-fd0f5680-b809-4909-b44a-b2ed9bb4c62b.png){: .align-center width="70%"}

- 우리의 목적은 variational distribution을 찾아내는것 → Variational Auto-encoder의 Encoder부분
- posterior distribution을 모르는데, 어떻게 variational distribution을 찾아낼 수 있을까?
    - 예를 들어, loss function은 주어져있는데 target값을 모르는 것과 같다.
- 이것을 가능하게 해주는 것이 ELBO(Evidence Lower Bound) Trick이다.

![image](https://user-images.githubusercontent.com/91870042/153741636-eddf4153-dfd0-4355-b544-5f4ab961d17d.png){: .align-center width="70%"}

- 우리의 목적: posterior distribution와 varational distribution의 KL-Divergence를 줄이는 것
- 위가 불가능하므로, ELBO의 값을 크게 설정해서 Objective항의 값을 줄이는 방향으로 한다.
- 이제, 이 ELBO값을 계산하는 방법에 대해서 알아봐야 한다. ELBO는 다음처럼 나뉠 수 있다.

![image](https://user-images.githubusercontent.com/91870042/153741647-e4de2bb2-b0bb-4537-8c6a-04bead4915ef.png){: .align-center width="70%"}

- ELBO의 계산은 Reconsturtion Term과 Prior Fitting Term으로 나뉜다.
    - Reconstruction Term: auto-encoder의 reconstruction loss를 최소화
        - reconstruction loss ; x라는 입력을 latent space로 보냈다가 다시 decoder로 돌아오는것
    - Prior Fitting Term: latent distribution과 prior distribution을 근사하게 만든다.
- Variational Auto-encoder의 어려움
    - liklihood를 계산하기 어려운 intractable model이다.
    - ELBO의 2번째 term인 Prior Fitting Term이 미분이 가능해야 한다.
        - 미분을 가능하게 하기 위해, isotropic Gaussian을 활용한다.
    
    ![image](https://user-images.githubusercontent.com/91870042/153741654-2fa1ef14-18f0-4ff5-bb4a-5c52bbc5558c.png){: .align-center width="70%"}
    

<br>

# Adversarial Auto-encoder

![image](https://user-images.githubusercontent.com/91870042/153741656-fc6340d6-12fc-4230-b139-44b24bbf805b.png){: .align-center width="70%"}

- 위에서 Variational Auto-Encoder에서는 단점으로 prior fitting term이 Gaussian 을 따라야 연산이 가능했던 것이 있었다. → 하지만, 많은 경우에서 Gaussian을 활용하고 싶지 않을 때도 있다.
- 이것을 해결해주는 것이 Adaversarial Auto-encoder이다.
- GAN을 활용해서 latent distribution 사이의 분포를 맞춰준다. 
→ Prior Fitting Term을 GAN Objective로 바꿔버린다.
- latent distribution을 sampling 하는 분포만 있어도 근사시킬 수 있다.
    - uniform distribution

<br>

# Generative Adversarial Network

- Two player game.

![image](https://user-images.githubusercontent.com/91870042/153741662-eaebde1b-1e0c-4ac1-b39d-ed6ff07a9f79.png){: .align-center width="70%"}

- 한명이 위조지폐를 만들고, 그 위조지폐를 진짜인지 가짜인지 판별한다.
- 다시 이 판별한 정보를 가지고 더 잘 위조지폐를 만들수 있도록 하며 반복적으로 연산하는 구조

![image](https://user-images.githubusercontent.com/91870042/153741665-0ec70781-6a91-4b86-b3b2-66ba51731aa8.png){: .align-center width="70%"}

- generator와 discriminator 2명의 플레이어가 하는 minmax게임
    - minmax게임 : 한명은 높이려고 하고, 한명은 낮추려는 게임
- Discriminator의 입장
    
    ![image](https://user-images.githubusercontent.com/91870042/153741668-9dfc855d-7e5f-4fab-a4c0-8f71e7e6dc32.png){: .align-center width="70%"}
    
    - 위를 최적화 시키는 D는 다음과 같이 나타난다.
    
    ![image](https://user-images.githubusercontent.com/91870042/153741670-c782a63d-b199-4460-842f-b9f2ea2b5737.png){: .align-center width="70%"}
    
    - Generator가 고정되어 있을 때, 최적으로 구분을 해내는 optimal distribution.
    - 이 값을 다시 generator에 전달을 해주게 된다.
- Generator의 입장
    
    ![image](https://user-images.githubusercontent.com/91870042/153741673-36e750fa-2a0f-4bb9-9c5e-81059f095db0.png){: .align-center width="70%"}
    
    - 위에서 전달받은 값을 대입하면 나오는 결과
    
    ![image](https://user-images.githubusercontent.com/91870042/153741679-bca64a86-edcb-4a65-8fb2-1267ddcf40b3.png){: .align-center width="70%"}
    
    - GAN의 Objective가 많은 경우, 데이터를 실제로 만들어냈다고 생각하는 distribution과 학습시킨 generator사이의 Jenson-Shannon Divergence를 최소화 하는 것이라고 말한다.
    

## DCGAN

![image](https://user-images.githubusercontent.com/91870042/153741687-3963521e-a2c5-4d69-8d0b-1c4bde33fbf7.png){: .align-center width="70%"}

- 이미지 도메인에서 GAN을 사용한 구조
- leaked ReLU의 사용

## Info-GAN

![image](https://user-images.githubusercontent.com/91870042/153741691-3465b607-71e6-4c6b-861e-329959091d46.png){: .align-center width="70%"}

- 이미지만 만드는 것이 아니라, class도 같이 만들어낸다.
- GAN이 특정 모드에 집중할 수 있게 해준다. → multi modal distribution

## Text2Image

![image](https://user-images.githubusercontent.com/91870042/153741695-31c72b36-138e-4896-8ffa-88da1b9fe4e0.png){: .align-center width="70%"}

## Puzzle-GAN

![image](https://user-images.githubusercontent.com/91870042/153741700-5ede984d-fcc8-42ae-a550-f7feb06ddfbf.png){: .align-center width="70%"}

- 이미지의 부분만을 가지고 복원하는 것

## Cycle GAN

![image](https://user-images.githubusercontent.com/91870042/153741703-779f3955-0325-4891-8a87-95b7982096a4.png){: .align-center width="70%"}

- 이미지 사이의 도메인을 바꿔주는 것
- Cycle-consistency loss를 사용

![image](https://user-images.githubusercontent.com/91870042/153741707-fe869c70-6451-4e10-94a2-951e32ad9cd6.png){: .align-center width="70%"}

## Star-GAN

![image](https://user-images.githubusercontent.com/91870042/153741709-851757ff-06f3-4957-a085-9c5cf96d9291.png){: .align-center width="70%"}

## Progressive-GAN

- 저해상도에서 고해상도로 점차 늘려가면서 학습을 진행

![image](https://user-images.githubusercontent.com/91870042/153741715-72eb993a-8e06-4157-81c9-2140521b63b3.png){: .align-center width="70%"}