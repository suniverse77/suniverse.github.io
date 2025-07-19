---
layout: single
title: "[논문리뷰] DreamVideo: Composing Your Dream Videos with Customized Subject and Motion"
last_modified_at: 2025-06-15
categories: ["논문리뷰"]
tags: ["Video Generation"]
excerpt: "CVPR 2024"
use_math: true
toc: true
toc_sticky: true
---

**CVPR 2024** 
[[Paper]](https://arxiv.org/abs/2312.04433)
[[GitHub]](https://github.com/ali-vilab/VGen)

<details>
<summary><font color='#FF8C00'>📝 Summary</font></summary>
<div markdown="1">
<br>
모델 최적화의 복잡도를 줄이고 커스터마이징의 유연성을 높이기 위해 subject 학습과 motion 학습을 분리하였다.
- 

</div>
</details>

## Introduction

**기존 연구들의 한계점**

- 공간적 content와 시간적 움직임을 동시에 커스터마이징하는 것은 어렵다.
- 기존의 Dreamix, Tune-A-Video와 같은 연구에서는 subject의 정체성 또는 target motion만 최적화하도록 집중하였다.
- [AnimateDiff](https://suniverse77.github.io/논문리뷰/AnimateDiff/)에서는 personalized T2I 모델에 temporal 모듈을 덧붙여 학습시키지만, 생성된 비디오의 motion이 단조롭다.

**제안하는 방법**

소수의 이미지와 비디오만으로도 사용자가 지정한 subject와 원하는 motion을 생성할 수 있는 DreamVideo를 제안한다.

- 모델 최적화의 복잡도를 줄이고 커스터마이징의 유연성을 높이기 위해 subject 학습과 motion 학습을 분리하였다.

## Methods

<center><img src='{{"/assets/images/논문리뷰/DreamVideo-1.png" | relative_url}}' width="100%"></center>

### 1. DreamVideo

Subject 학습과 motion 학습을 분리하여, 두 개의 어댑터를 통해 맞춤형 비디오 생성 task를 수행한다.

사용자는 이 두 어댑터를 간단히 결합함으로써 원하는 비디오를 생성할 수 있다.

#### Subject Learning

#### Motion Learning

### 2. Model Analysis, Training and Inference

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



## Experiments

### Quantitative Comparisons

<center><img src='{{"/assets/images/논문리뷰/MagicAnimate-5.png" | relative_url}}' width="80%"></center>

### Qualitative Comparisons

<center><img src='{{"/assets/images/논문리뷰/MagicAnimate-6.png" | relative_url}}' width="100%"></center>
