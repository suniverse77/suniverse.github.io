---
layout: single
title: "[논문리뷰] AnimateDiff: Animate Your Personalized Text-to-Image Diffusion Models without Specific Tuning"
last_modified_at: 2025-06-13
categories: ["논문리뷰"]
tags: ["Video Generation"]
excerpt: "ICLR 2024"
use_math: true
toc: true
toc_sticky: true
---

**ICLR 2024** 
[[Paper]](https://arxiv.org/abs/2304.08818)
[[GitHub]](https://github.com/guoyww/AnimateDiff)

## Introduction

**기존 연구들의 한계점**
- Personalized T2I 모델은 정적인 이미지 생성에만 국한되어 있고, 시간적 변화를 고려하지 못한다.
- 기존의 T2I 모델에 motion dynamic(움직임)을 부여하기 위해서는 파인튜닝, 대규모 비디오 데이터셋, 고성능 컴퓨팅 자원 등이 필요하다.

**제안하는 방법**
- 별도 파인튜닝 없이 personalized T2I 모델에 애니메이션 생성 기능을 부여하는 실용적인 파이프라인인 AnimateDiff를 제안한다.
- AnimateDiff의 핵심 구성 요소인 motion module은 plug-and-play 방식이다.
- Transformer 구조가 motion prior를 모델링하는 데 적합하다.
- 사전 학습된 motion module이 새로운 motion에 적응하기 위한 파인튜닝 방법으로 MotionLoRA를 제안한다.

## Methods

<center><img src='{{"/assets/images/논문리뷰/AnimateDiff-1.png" | relative_url}}' width="70%"></center>

추론 시에는 motion module(파란색)과 선택적으로 사용할 수 있는 MotionLoRA(초록색)를 personalized T2I 모델에 직접 삽입하여 애니메이션 생성기를 구성할 수 있다.

이를 가능하게 하기 위해 AnimateDiff는 세 가지 구성요소를 학습한다.

- Domain Adapter: 학습 단계에서만 사용되며, 기존 T2I 모델의 사전학습 데이터와 비디오 학습 데이터 간의 시각적 분포 차이로 인한 부정적 영향을 완화하는 것이 목적이다.
- Motion module: motion prior를 학습하는 AnimateDiff의 핵심 구성요소이다.
- MotionLoRA: 사전학습된 motion module을 새로운 모션 패턴에 적응시키는 데 사용한다.

---

<center><img src='{{"/assets/images/논문리뷰/AnimateDiff-2.png" | relative_url}}' width="100%"></center>

### 1. Alleviate Negative Effects from Training Data with Domain Adapter

T2I 모델을 학습할 때 사용한 이미지 데이터셋과 motion module을 학습할 때 사용할 비디오 데이터셋 사이에는 domain gap이 존재하며, domain adapter는 비디오 프레임의 저품질 특성을 중화시키는 역할을 한다.

- T2I 모델은 고품질의 이미지 데이터로 학습되어 있다. 만약 모델에 저품질의 비디오 데이터를 그대로 사용해 학습하면 애니메이션 생성 품질을 저해할 수 있다.
- 비디오를 프레임별로 보면, 움직임 때문에 생긴 blur나 압축 과정에서 발생한 artifact가 포함되어 있다. 이러한 프레임을 독립된 이미지로 간주해 학습에 사용하면, 생성된 이미지에도 블러나 아티팩트가 그대로 반영될 수 있다.

domain adapater layer를 LoRA를 이용해 구현하고, T2I 모델의 self-attention 및 cross-attention layer에 삽입한다.

Query를 생성하는 단계가 아래와 같아진다. (Key, Value도 동일)

$$
Q=W^Qz+\text{AdapterLayer}(z)=W^Qz+\alpha\cdot AB^\top z
$$

여기서 $\alpha=1$은 추론 때 다른 값으로 변경될 수 있다.

추론은 노이즈에서 이미지를 생성하는 과정으로, 학습 때와 달리 domain gap이라는 것이 존재하지 않기 때문에 domain adapter의 영향이 있으면 오히려 성능이 저하될 수 있다. 따라서, 추론 때는 $\alpha=0$으로 설정한다.

Domain adapater의 파라미터를 비디오 데이터셋에서 임의로 샘플링한 정적 프레임을 이용하여 아래의 목표 함수를 기반으로 최적화한다.

<center><img src='{{"/assets/images/논문리뷰/AnimateDiff-3.png" | relative_url}}' width="60%"></center>

### 2. Learn Motion Priors with Motion Module

motion dynamics을 모델링하기 위해, 사전학습된 T2I 위에 아래의 두 가지 절차를 수행한다.

**1. Network Inflation**

2D diffusion 모델을 3D 비디오 데이터에 대응할 수 있도록 네트워크 구조를 바꾼다.

5차원 텐서 $x\in\mathbb{R}^{b\times c\times f\times h\times w}$ 비디오 입력이 각 layer를 통과할 때 아래와 같이 형태를 변형한다.

- Image layer를 통과할 때는 시간 축이 무시되도록 한다. → $x\in\mathbb{R}^{(b\cdot f)\times c\times h\times w}$ 
- Motion module에서는 시간 축만 유지한 채 동작하도록 한다. → $x\in\mathbb{R}^{(b\cdot h\cdot w)\times c\times f}$ 

**2. Module Design**

Transformer 구조를 기반으로 하는 Temporal Transformer를 채택하여 시간 축 방향의 정보를 처리한다.

Temporal Transformer는 시간 축을 따라 여러 개의 self-attention 블록으로 구성되며, 각 프레임의 위치 정보를 인코딩하기 위해 사인파 위치 인코딩(sinusoidal positional encoding)을 사용한다.

앞서 언급한 것처럼, 모션 모듈의 입력은 공간 정보를 병합한 후 시간 순서대로 정렬된 피처 시퀀스이다.

$$
\lbrace z_1,\dots,z_f;~z_i\in\mathbb{R}^{(b\cdot h)\cdot w\times c}\rbrace
$$

이러한 시퀀스는 self-attention 블록에 의해 처리된다.

$$
z_{\text{out}}=\text{Attention}(Q,K,V)=\text{Softmax}(\frac{QK^\top}{\sqrt{c}})\cdot V
$$

이러한 어텐션 메커니즘은 현재 프레임이 다른 프레임으로부터 정보를 통합하여 표현할 수 있게 해주기 때문에, 각 프레임을 독립적으로 생성하는 것이 아니라, 전체 시간 축을 고려한 시각적 변화(motion dynamics)를 학습할 수 있게 된다.

추가된 모션 모듈이 학습 초기에 불안정한 영향을 주는 것을 방지하기 위해, 아래와 같은 안정화 기법을 적용하였다.

- Temporal Transformer의 출력 projection layer를 0으로 초기화
- Residual connection을 추가하여 학습 초기에 motion module이 항등 함수(identity mapping)처럼 작동하도록 설정

### 3. Adapt to New Motion Patterns with MotionLoRA

사전학습된 motion module은 일반적인 모션 패턴을 포착할 수는 있지만, camera zooming, panning 등과 같은 새로운 모션 패턴에 대해서도 적응시켜야 할 때가 발생한다. 이를 위해 소수의 참조 비디오와 적은 학습  횟수로도 높은 효율을 내는 방식이 필요하다.

제안하는 MotionLoRA는 motion personalization을 위한 효율적인 파인튜닝 기법으로, 2절에서 설명한 motion module의 self-attention layer에 LoRA layer를 추가하고, 새로운 모션 패턴에 대한 참조 비디오를 기반으로 이 LoRA layer만 학습시킨다.

다양한 촬영 유형(zooming, panning 등)에 대해 실험을 진행하였고, rule-based augmentation을 통해 참조 비디오를 생성했다. (예를 들어, zoom-in/out 효과가 있는 비디오를 얻기 위해 프레임의 크롭 영역을 시간축을 따라 점진적으로 축소 또는 확대하는 방식으로 비디오를 증강)

MotionLoRA는 low-rank 성질 때문에 모션의 합성 능력도 가진다. 즉, 서로 다른 모션에 대해 학습한 여러 개의 MotionLoRA를 조합해서 새로운 모션 효과를 만들 수 있다.

### 4. AnimateDiff in Practice

#### Training

AnimateDiff는 3가지의 학습 가능한 모듈로 구성되며, 이 모듈들은 학습 목적이 다르다.

- Domain Adapter는 기존 확산 모델의 목적 함수를 그대로 사용한다.

    <center><img src='{{"/assets/images/논문리뷰/AnimateDiff-3.png" | relative_url}}' width="60%"></center>
- Motion module과 MotionLoRA는 애니메이션 생성기의 일부로서, 고차원 비디오 데이터에 적합하도록 약간 수정된 목적 함수를 사용한다.

    <center><img src='{{"/assets/images/논문리뷰/AnimateDiff-4.png" | relative_url}}' width="60%"></center>

    여기서 $z_t^{1:f}=\sqrt{\bar{\alpha_t}}z_0^{1:f}+\sqrt{1-\bar{\alpha_t}}\epsilon^{1:f}$는 프레임별 latent code를 의미한다.

#### Inference

추론 시에는 아래와 같은 순서로 모델이 사용된다.

1. Methods 2절에서 설명한 방식대로 모델이 inflate된다.
2. Inflate된 모델에 motion module을 삽입하여 애니메이션 생성을 수행하고, 선택적으로 MotionLoRA도 함께 삽입할 수 있다.
3. Reverse diffusion 과정을 수행하고 latent code를 디코딩함으로써 애니메이션 프레임을 얻는다.

Domain Adapter는 추론 시 제거하는 방식이 기본이지만, 실제 실험에서는 domain Adapter를 삽입한 채로 $\alpha$ 값을 조절해 기여도를 조정하는 방법도 사용하였다.

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
