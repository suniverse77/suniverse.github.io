---
layout: single
title: "[논문리뷰] Adding Conditional Control to Text-to-Image Diffusion Models"
last_modified_at: 2025-03-21
categories: ["논문리뷰"]
tags: ["Image Generation"]
excerpt: "CVPR 2023"
use_math: true
toc: true
toc_sticky: true
---

**CVPR 2023** 
[[Paper]](https://arxiv.org/abs/2302.05543)
[[GitHub]](https://github.com/lllyasviel/ControlNet)

<details>
<summary><font color='#FF8C00'>📝 Summary</font></summary>
<div markdown="1">
<br>


</div>
</details>
<br>
<center><img src='{{"/assets/images/논문리뷰/ControlNet-0.png" | relative_url}}' width="100%"></center>

## Introduction

**기존 연구들의 한계점**

- 텍스트만으로 프롬프트만으로는 자세, 레이아웃 등 복잡한 이미지 구성을 정밀하게 제어하기 어렵다.

**제안하는 방법**

Stable Diffusion과 같은 대형 T2I 모델을 기반으로, 외부 이미지 조건을 사용하여 더 정밀한 제어를 가능하게 한다.

- 기존 파라미터를 고정하고, 학습 가능한 복사본인 인코딩 계층을 원본과 zero convolution layer로 연결한다.
- Zero convolution layer는 처음에 가중치가 0으로 초기화된 convolution을 의미하며, 

## Methods

### 1. ControlNet

ControlNet은 아래 그림과 같이 추가적인 조건을 신경망 내부에 주입한다.

<center><img src='{{"/assets/images/논문리뷰/ControlNet-1.png" | relative_url}}' width="60%"></center>
<br>
$\Theta$로 파라미터화되어 있는 신경망 블록을 아래와 같이 표현한다.

$$
\mathbf{y}=\mathcal{F}(\mathbf{x};\Theta)
$$

위의 수식에서 $\mathbf{x}$와 $\mathbf{y}$는 일반적으로 2D feature이며, $\mathbf{x}\in\mathbb{R}^{h\times w\times c}$ 형태를 가진다.

사전학습된 블록에 ControlNet을 추가하기 위해 $\Theta_c$로 파라미터화되어 있는 trainable copy 블록을 생성한다. 이 블록은 외부 조건 $\mathbf{c}$를 입력으로 받는다.

Trainable copy 블록은 원본과 zero convolution $\mathcal{Z}(\cdot;\cdot)$으로 연결되며, zero convolution은 초기에 가중치와 바이어스가 모두 0으로 초기화된 1x1 convolution을 의미한다.

ControlNet 블록의 출력은 아래와 같이 표현될 수 있다.

$$
\mathbf{y}_c=\mathcal{F}(\mathbf{x};\Theta)+\mathcal{Z}\left(
\mathcal{F}\left(\mathbf{x}+\mathcal{Z}(\mathbf{c;\Theta_{z1}});\Theta_C\right);\Theta_{z2}
\right)
$$

Zero convolution에 의해 초기에는 $\mathbf{y}_c=\mathbf{y}$가 된다. 이 덕분에 무작위로 초기화된 파라미터가 학습 초기에 trainable copy 블록의 hidden state에 영향을 주는 문제를 방지한다. 즉, backbone을 보호하며, $\mathbf{c}$의 영향을 점진적으로 반영하게 한다.

### 2. ControlNet for Text-to-Image Diffusion

ControlNet에도 Stable Diffusion에서와 동일하게 Prompt $\mathbf{c}_t$와 timestep $\mathbf{t}$가 입력으로 들어간다. 이때, Text prompt는 CLIP으로 인코딩되고, timestep은 positional encoding으로 인코딩된다.

<center><img src='{{"/assets/images/논문리뷰/ControlNet-2.png" | relative_url}}' width="70%"></center>
<br>
ControlNet 구조는 UNet의 인코더에만 적용된다. 구체적으로는, Stable Diffusion의 12개의 Encoder Block과 1개의 Middle Block의 파라미터를 ControlNet으로 복사한다.

ControlNet을 Stable Diffusion에 추가하기 위해, 먼저 512x512 크기의 조건 입력 이미지를 64x64의 latent 이미지로 변환해야 한다. 이를 위해 4개의 convolution layer로 구성된 인코더 $\mathcal{E}(\cdot)$을 사용하며, 전체 모델과 함께 공동으로 학습된다.

$$
\mathbf{c}_f=\mathcal{E}(\mathbf{c}_i)
$$

위의 수식에서 $\mathbf{c}_f$는 ControlNet에 전달되는 conditioning 벡터를 의미한다.

### 3. Training

아래의 수식은 ControlNet을 활용해 디퓨전 모델을 파인튜닝할 때 사용되는 목적함수이다.

$$
\mathcal{L}=\mathbb{E}_{\mathbf{z}_0,\mathbf{t},\mathbf{c}_t,\mathbf{c}_f,\epsilon\sim\mathcal{N}(0,1)}
\left[\lVert
\epsilon-\epsilon_\theta(\mathbf z_t,t,\mathbf c_t,\mathbf c_f)
\rVert^2_2\right]
$$

실제 학습 때는 50%의 확률로 $\mathbf{c}_t$를 빈 문자열로 대체하여, 모델이 텍스트 프롬프트 없이 이미지 $\mathbf{c}_f$만으로도 의미를 파악하도록 유도해, ControlNet의 능력을 극대화한다.

실험 결과, 모델이 조건을 점진적으로 학습하는 것이 아니라 갑자기 조건을 따르는 현상을 발견하였다.

<center><img src='{{"/assets/images/논문리뷰/ControlNet-3.png" | relative_url}}' width="100%"></center>
<br>
저자들은 위와 같은 현상을 급격한 수렴 현상(sudden convergence phenomenon)이라고 명명하였다.

### 4. Inference

#### Classifier-free guidance resolution weighting



<center><img src='{{"/assets/images/논문리뷰/ControlNet-4.png" | relative_url}}' width="100%"></center>

#### Composing multiple ControlNets

여러 개의 조건 이미지를 동시에 적용하고자 할 경우, 각 조건에 해당하는 ControlNet의 출력들을 단순히 Stable Diffusion에 더해주기만 하면 된다.

<center><img src='{{"/assets/images/논문리뷰/ControlNet-5.png" | relative_url}}' width="100%"></center>

## Experiments

### Qualitative Results

<center><img src='{{"/assets/images/논문리뷰/ControlNet-6.png" | relative_url}}' width="100%"></center>
<br>
위의 그림은 프롬프트 없이 다양한 condition에 따른 모델의 출력 결과를 보여준다.

**Ablation Study**

<center><img src='{{"/assets/images/논문리뷰/ControlNet-7.png" | relative_url}}' width="100%"></center>

### Quantitative Evaluation

<center><img src='{{"/assets/images/논문리뷰/ControlNet-8.png" | relative_url}}' width="100%"></center>
