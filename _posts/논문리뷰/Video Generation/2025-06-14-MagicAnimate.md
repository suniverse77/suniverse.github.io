---
layout: single
title: "[논문리뷰] MagicAnimate: Temporally Consistent Human Image Animation using Diffusion Model"
last_modified_at: 2025-06-14
categories: ["논문리뷰"]
tags: ["Video Generation"]
excerpt: "CVPR 2024"
use_math: true
toc: true
toc_sticky: true
---

**CVPR 2024** 
[[Paper]](https://arxiv.org/abs/2311.16498)
[[Github]](https://github.com/magic-research/magic-animate)

<details>
<summary><font color='#FF8C00'>📝핵심 아이디어</font></summary>
<div markdown="1">

Reference 이미지의 정체성, 배경 등의 정보를 보존하기 위해 Appearance Encoder 설계

프레임별 화질을 향상시키기 위해, image-video joint 학습 전략 사용

</div>
</details>

## Introduction

기존 사람 이미지 애니메이션을 위한 기법들은 크게 2가지로 나눌 수 있다.
- GAN 기반 프레임워크: warping 함수를 사용해 참조 이미지를 목표 포즈로 변형한 뒤, GAN 모델을 사용해 누락되거나 가려진 신체 부위를 보완
- Diffusion 기반 프레임워크: 사전학습된 디퓨전 모델을 기반으로 외형과 포즈 조건을 활용해 이미지 생성

**기존 연구들의 한계점**
- GAN 기반 프레임워크는 제한된 모션 전이 능력으로 인해, 가려진 영역에서 비현실적인 디테일 발생 및 identity가 다른 사람에게 일반화하는 데 한계 존재
- Diffusion 기반 프레임워크: 시간적 일관성을 고려하지 않아 결과에 flickering 현상이 나타날 수 있음, 주로 CLIP을 이용해 인코딩하는 데 이 방법은 디테일 보존에 취약함

**제안하는 방법**
- MagicAnimate라는 사람 이미지 애니메이션 프레임워크 제안
    1. temporal attention block을 도입해 시간 정보를 인코딩
    2. 기존의 CLIP 대신, 혁신적인 appearance encoder를 도입해 정체성, 배경, 의상 등의 요소를 더 잘 유지할 수 있음
    3. 프레임별 화질을 향상시키기 위해, 단일 프레임 이미지 데이터를 활용한 image-video joint 학습 전략을 설계해 더 풍부한 시각적 단서를 통해 세부 묘사 능력 강화
    4. 매우 단순한 비디오 융합 기법을 활용해 장시간의 애니메이션에서도 부드러운 전환을 가능하게 함

## Methods

<center><img src='{{"/assets/images/논문리뷰/AnimateDiff-1.png" | relative_url}}' width="70%"></center>

추론 시에는 motion module(파란색)과 선택적으로 사용할 수 있는 MotionLoRA(초록색)를 personalized T2I 모델에 직접 삽입하여 애니메이션 생성기를 구성할 수 있다.

이를 가능하게 하기 위해 AnimateDiff는 세 가지 구성요소를 학습한다.

- Domain Adapter: 학습 단계에서만 사용되며, 기존 T2I 모델의 사전학습 데이터와 비디오 학습 데이터 간의 시각적 분포 차이로 인한 부정적 영향을 완화하는 것이 목적이다.
- Motion module: motion prior를 학습하는 AnimateDiff의 핵심 구성요소이다.
- MotionLoRA: 사전학습된 motion module을 새로운 모션 패턴에 적응시키는 데 사용한다.

---

<center><img src='{{"/assets/images/논문리뷰/AnimateDiff-2.png" | relative_url}}' width="100%"></center>

### 1. Temporal Consistency Modeling



### 2. Appearance Encoder

참조 이미지 $I_{\text{ref}}$의 얼굴, 신체 특징, 배경 요소 같은 세밀한 정보를 보존하기 위해 설계하였다.

구체적으로, Appearance Encoder는 base UNet $\mathcal{F}_a(\theta^a)$의 trainable copy이며, 참조 이미지에 대한 condition feature를 계산한다.

trainable copy란 base UNet의 구조를 그대로 복제

$$
\mathbf{y}_a=\mathcal{F}_a(\mathbf{z}_t\mid I_{\text{ref}},\mathbf{t},\theta^a)
$$

위의 식에서 $\mathbf{y}_a$는 UNet의 중간에서의 attention hidden state이다.

ControlNet에서 조건을 주는 방식과는 달리, 

$$
\text{Attention}(Q,K,V,\mathbf{y}_a)=\text{Softmax}(\frac{QK'^\top}{\sqrt{d}})V'
$$

$$
Q=W^Q\mathbf{z}_t,~K'=W^K[\mathbf{z}_t,\mathbf{y}_a],~V'=W^V[\mathbf{z}_t,\mathbf{y}_a]
$$

### 3. Animation Pipeline

#### Motion transfer

DensPose를 사용하였다.

#### Denoising process

#### Long video animation

### 4. Training

#### Training

An

## Experiments

### Qualitative Results

<center><img src='{{"/assets/images/논문리뷰/AnimateDiff-5.png" | relative_url}}' width="100%"></center>

### Quantitative Comparisons

<center><img src='{{"/assets/images/논문리뷰/AnimateDiff-6.png" | relative_url}}' width="70%"></center>

### Ablation Study

**Domain Adapter**

<center><img src='{{"/assets/images/논문리뷰/AnimateDiff-7.png" | relative_url}}' width="100%"></center>
<br>
추론 때 $\alpha$ 값이 감소할수록 품질이 올라가는 것을 확인할 수 있다.

**Motion LoRA**

<center><img src='{{"/assets/images/논문리뷰/AnimateDiff-8.png" | relative_url}}' width="100%"></center>
<br>
왼쪽의 첫 번째와 두 번째 샘플은 MotionLoRA가 소규모 파라미터 크기로도 zoom-in과 같은 새로운 카메라 모션을 성공적으로 학습할 수 있으며, 모션 품질도 유사한 수준으로 유지된다는 것을 보여준다.

오른쪽은 참조 비디오 수가 50개 정도(N=50)만 되어도 원하는 모션 패턴을 성공적으로 학습할 수 있음을 보여준다. 그러나 참조 비디오 수가 지나치게 적을 경우(N = 5)에는 품질이 눈에 띄게 저하되며, 참조 비디오의 텍스처 정보에 의존하는 경향을 보인다.
