---
layout: single
title: "[논문리뷰] MagicAnimate: Temporally Consistent Human Image Animation using Diffusion Model"
last_modified_at: 2025-06-14
categories: ["논문리뷰"]
tags: ["Diffusion", "Video Generation"]
excerpt: "CVPR 2024"
use_math: true
toc: true
toc_sticky: true
---

**CVPR 2024** 
[[Paper]](https://arxiv.org/abs/2311.16498)
[[GitHub]](https://github.com/magic-research/magic-animate)

<details>
<summary><font color='#FF8C00'>📝 Summary</font></summary>
<div markdown="1">
<br>
새로운 appearance 인코더를 설계하여 reference 이미지의 정체성, 배경 등의 정보를 보존하였다.
- Appearance 인코더는 base UNet 구조를 복제하여 학습
- UNet의 중간 feature들을 base UNet에 단순히 더하지 않고, attention block의 key와 value에 합쳐 attention 연산을 수행

Video frame fusion 기법을 통합하여 애니메이션 영상 전반에 걸친 자연스러운 전환을 가능하게 하였다.
- 연산량의 문제 때문에 전체 프레임을 여러 개의 세그먼트로 나누어 학습하였으나, 각 세그먼트끼리 조금씩 겹치게 함

Image-video joint 학습 전략을 사용해 프레임별 품질을 향상시켰다.
- 랜덤 변수 $r$과 임계값을 설정하여, $r$의 값에 따라 비디오 데이터셋을 사용할건지 이미지 데이터셋을 사용할건지 설정

</div>
</details>
<br>
<center><img src='{{"/assets/images/논문리뷰/MagicAnimate-0.png" | relative_url}}' width="100%"></center>

## Introduction

기존 사람 이미지 애니메이션을 위한 기법들은 크게 GAN 기반 프레임워크와 Diffusion 기반 프레임워크 2가지로 나눌 수 있다.

**기존 연구들의 한계점**

- GAN 기반 기법

    은 제한된 모션 전이 능력을 가지며, 이로 인해 가려진 영역에서 비현실적인 디테일을 생성하고, 서로 다른 정체성 간(cross-identity)의 일반화 능력이 부족하다.
- Diffusion 기반 기법

    시간적 일관성을 고려하지 않아 생성된 비디오에 flickering 현상이 나타날 수 있다. 또한, appearance를 인코딩하기 위해 일반적으로 CLIP을 사용하는 데, 이 방법은 디테일 보존에 취약하다.

**제안하는 방법**

MagicAnimate라는 사람 이미지 애니메이션 프레임워크 제안한다.

- Temporal attention block을 도입해 시간 정보를 인코딩하였다.
- 기존의 CLIP 대신, 정체성, 배경, 의상 등의 요소를 더 잘 유지할 수 있는 새로운 appearance encoder를 도입하였다.
- 단일 프레임 이미지 데이터를 활용한 image-video joint 학습 전략을 설계해 프레임별 화질을 향상시켰다.
- 매우 단순한 비디오 융합 기법을 활용해 장시간의 애니메이션에서도 부드러운 전환을 가능하게 하였다.

## Methods

<center><img src='{{"/assets/images/논문리뷰/MagicAnimate-1.png" | relative_url}}' width="100%"></center>

- Reference 이미지 $I_{\text{ref}}$를 appearance encoder $\mathcal{F}_a$에 통과시켜 appearance 임베딩 $\mathbf{y}_a$로 변환한다.
- 만들고자 하는 pose sequence $\mathbf{p}^{1:N}$를 pose ControlNet $\mathcal{F}_p$에 통과시켜 motion condition $\mathbf{y}_p^{1:K}$을 추출한다.
- Video Diffusion Model은 이 두 신호를 조건으로 받아, reference 이미지가 주어진 motion을 따라하는 비디오를 생성한다.

---

### 1. Temporal Consistency Modeling

2D UNet 구조를 3D temporal UNet 구조로 변형하는 과정으로, [기존 방식](https://suniverse77.github.io/논문리뷰/MAV/)과 동일하다.

- 원본 shape: $\mathbb{R}^{N\times C\times K\times H\times W}$
- 모델 입력을 위한 shape: $\mathbb{R}^{(NK)\times C\times H\times W}$
- temporal 모듈 입력을 위한 shape: $\mathbb{R}^{(NHW)\times K\times C}$

### 2. Appearance Encoder

Reference 이미지의 얼굴, 신체 특징, 배경 요소 같은 세밀한 정보를 보존하기 위해 설계하였다.

구체적으로, Appearance Encoder는 base UNet $\mathcal{F}_a(\theta^a)$의 구조를 복제하여 사용하였다. (trainable copy)

$$
\mathbf{y}_a=\mathcal{F}_a(\mathbf{z}_t\mid I_{\text{ref}},\mathbf{t},\theta^a)
$$

인코딩 과정은 위와 같이 표현되며, $\mathbf{y}_a$는 UNet의 middle block과 upsampling block에서의 attention hidden state를 의미한다.

단순히 조건을 residual 방식으로 더하는 ControlNet과 달리, 조건 feature를 UNet의 spatial self-attention layer에 주입한다.

$$
\text{Attention}(Q,K,V,\mathbf{y}_a)=\text{Softmax}(\frac{QK'^\top}{\sqrt{d}})V'
$$

$$
Q=W^Q\mathbf{z}_t,~K'=W^K[\mathbf{z}_t,\mathbf{y}_a],~V'=W^V[\mathbf{z}_t,\mathbf{y}_a]
$$

Key와 Value를 생성할 때, $\mathbf{y}_a$와 원래 UNet의 self-attention hidden state를 concatenation한다.

### 3. Animation Pipeline

#### Motion transfer

ControlNet은 OpenPose keypoints를 이용해 reference 사람 이미지를 애니메이션화하는 데 일반적으로 사용된다.

이 방식은 그럴듯한 결과를 생성하지만, 저자들은 신체의 주요 keypoint들이 드물고 회전과 같은 특정 움직임에 대해 견고하지 않다고 주장한.

따라서 보다 밀도 높고 견고한 포즈 조건을 위해 DensePose를 모션 신호 $\mathbf{p}_i$로 선택하였다.

포즈 ControlNet $\mathbf{y}_{\mathbf{p},i}=\mathcal{F}_(\cdot,\theta^{\mathbf{p}})$를 사용하며, 프레임 $i$에 대한 pose condition은 아래와 같이 계산된다.

$$
\mathcal{F}_{\mathbf{p}}(\mathbf{z}_t\mid\mathbf{p},\mathbf{t},\theta^{\mathbf{p}})
$$

#### Denoising process

노이즈 예측 함수 $\epsilon_\theta^{1:K}(\cdot)$은 아래와 같이 정의된다.

$$
\epsilon_\theta^{1:K}(\mathbf{z}_t^{1:K},\mathbf{t},I_{\text{ref}},\mathbf{p}^{1:K})
=
\mathcal{F}^T(\mathbf{z}_t^{1:K}\mid \mathbf{t},\mathbf{y}_a,\mathbf{y}_p^{1:K})
$$

#### Long video animation

장시간 비디오를 생성하려면 연산량이 많이 필요하기 때문에, segment-by-segment 방식으로 생성하였다. (여러 개의 프레임으로 분할해서 생성)

하지만 긴 동영상을 프레임 단위로 나눠서 생성하면 세그먼트 사이의 전환이 부자연스럽고 디테일이 일관되지 않을 수 있기 때문에 inference 단계에서 sliding window 기법을 적용하였다.

- 긴 모션 시퀀스(총 $N$ 프레임)를 $K$의 길이를 갖는 여러 개의 세그먼트로 나눈다.
- 세그먼트들은 서로 $s$ 프레임만큼 겹치도록(오버랩) 설정

$$
\lbrace
\mathbf{z}^{1:K},\mathbf{z}^{K-s+1:2K-s},\dots,\mathbf{z}^{n(K-s)+1:n(K-s)+K}
\rbrace
~~,~~
n=\left\lceil\frac{N-K}{K-s}\right\rceil
$$

마지막 segment의 길이는 $K$보다 작을 수 있으며, 이때는 단순하게 처음 몇 장의 프레임으로 길이가 $K$가 되도록 패딩하였다.

경험적으로, 각 segment 내에서의 초기 노이즈는 동일하게 초기화하는 것이 성능이 좋았다고 한다.

각 세그먼트마다 노이즈 예측 모델 $\epsilon_\theta^{1:K}$을 얻고, 겹치는 부분은 평균을 내서 $\epsilon_\theta^{1:N}$으로 병합하였다.

### 4. Training

#### Learning objectives

첫 번째 stage에서는 temporal attention layer를 제외하고, appearance encoder와 pose ControlNet을 함께 학습한다.

$$
\mathcal{L}_1 =
\mathbb{E}_{\mathbf{z}_0,\, t,\, I_{\mathrm{ref}},\, \mathbf{p}_i,\, \boldsymbol{\epsilon} \sim \mathcal{N}(0,1)}
\left[
\left\| \boldsymbol{\epsilon} - \boldsymbol{\epsilon}_\theta \right\|_2^2
\right]
$$

두 번째 stage에서는 temporal attention layer만 학습한다.

$$
\mathcal{L}_2 =
\mathbb{E}_{\mathbf{z}_0^{1:K},\, t,\, I_{\mathrm{ref}},\, \mathbf{p}^{1:K},\, \boldsymbol{\epsilon}^{1:K} \sim \mathcal{N}(0,1)}
\left[
\left\| \boldsymbol{\epsilon}^{1:K} - \boldsymbol{\epsilon}_\theta^{1:K} \right\|_2^2
\right]
$$

#### Image-video joint training

**첫 번째 stage**

- 임계값 $\tau_0$를 설정하고 랜덤한 숫자 $r\sim U(0,1)$을 뽑는다.
- $r\leq\tau_0$이면, 이미지와 해당 이미지에서 추정된 포즈를 조건으로 사용하고, 모델은 해당 이미지를 재구성하도록 학습한다.

**두 번째 stage**

시간적 일관성도 향상시키는 동시에 개별 프레임별 품질도 향상시키기 위해 임계값에 따라 다른 조건에서 학습된다.

- $r\leq\tau_1$이면, 이미지 데이터셋을 이용해 학습한다.
- 그 외는 비디오 데이터셋을 이용해 학습한다.

$$
\boldsymbol{\epsilon}_\theta^{1:K} =
\begin{cases}
\boldsymbol{\epsilon}_\theta^{1:K}\left( \mathbf{z}_t,\, t,\, I_{\mathrm{ref}},\, \mathbf{p}_i \right), & \text{with } i = \mathrm{ref}, \quad \text{if } r \leq \tau_1, \\
\boldsymbol{\epsilon}_\theta^{1:K}\left( \mathbf{z}_t,\, t,\, I_{\mathrm{ref}},\, \mathbf{p}_i \right), & \text{with } i \neq \mathrm{ref}, \quad \text{if } \tau_1 \leq r \leq \tau_2, \\
\boldsymbol{\epsilon}_\theta^{1:K}\left( \mathbf{z}_t^{1:K},\, t,\, I_{\mathrm{ref}},\, \mathbf{p}^{1:K} \right), & \text{if } r \geq \tau_2.
\end{cases}
$$

## Experiments

### Quantitative Comparisons

<center><img src='{{"/assets/images/논문리뷰/MagicAnimate-2.png" | relative_url}}' width="80%"></center>

### Qualitative Comparisons

<center><img src='{{"/assets/images/논문리뷰/MagicAnimate-3.png" | relative_url}}' width="100%"></center>
