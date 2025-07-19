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

**첫 번째 stage**

[Textual Inversion](https://suniverse77.github.io/논문리뷰/Textual-Inversion/)을 사용해 textual identity를 학습한다.

Video diffusion model은 고정시켜 놓고, pseudo-word $S^*$의 텍스트 임베딩만 최적화한다.

하지만 textual identity만으로는 subject의 외형 디테일을 복원하기 어렵기 때문에 두 번째 stage가 필요하다.

**첫 번째 stage**

앞서 학습된 textual identity를 결합해 lightweight identity adapter를 학습한다.

텍스트 임베딩은 고정시켜 놓고, identity adapter만 최적화한다.

<center><img src='{{"/assets/images/논문리뷰/DreamVideo-2.png" | relative_url}}' width="20%"></center>

Identity adapter는 위와 같이 skip connection이 있는 병목 구조를 사용하며, down-projected linear layer $\mathbf{W}\_{\text{down}}$, up-projected linear layer $\mathbf{W}\_{\text{up}}$과 비선형 활성 함수 $\sigma$로 이루어져 있다.

Identity adapter의 학습 과정은 아래와 같이 표현된다.

$$
h'_t=h_t+\sigma\left(h_t*\mathbf{W}_{\text{down}}\right)*\mathbf{W}_{\text{up}}
$$

본 논문에서는 활성 함수로 GELU를 선택했다.

또한 학습 초기에 사전학습된 디퓨전 모델의 지식이 손상되지 않도록 $\mathbf{W}_{\text{up}}$을 0으로 초기화한다.

#### Motion Learning

효율적으로 motion을 모델링하기 위해 motion adapter의 구조를 identity adapter와 유사하도록 설계하였다.

Motion adapter가 motion의 패턴을 학습할 수 있지만, subject의 외형 또한 함께 학습할 수도 있다.

이를 방지하기 위해 외형 가이던스를 motion adapter에 주입시켜 motion adapter가 motion만 학습할 수 있도록 한다.

<center><img src='{{"/assets/images/논문리뷰/DreamVideo-3.png" | relative_url}}' width="30%"></center>

구체적으로, 위와 같이 condition linear layer $\mathbf{W}_{\text{cond}}$를 더했다.

외형 가이던스는 입력 비디오에서 무작위로 하나의 프레임을 선택한 후, 인코딩함으로써 만들어진다.

Motion adapter의 학습 과정은 아래와 같이 표현된다.

$$
\hat{h}_t^e=\hat{h}_t+\text{broadcast}(e*\mathbf{W}_{\text{cond}})
$$

$$
\hat{h}'_t=\hat{h}_t+\sigma\left(\hat{h}_t^e=\hat{h}_t*\mathbf{W}_{\text{down}}\right)*\mathbf{W}_{\text{up}}
$$

$e$는 이미지 임베딩을 의미하며, linear layer를 통과한 이미지 임베딩 $e*\mathbf{W}_{\text{cond}}$는 모든 프레임에 broadcast된다.

### 2. Model Analysis, Training and Inference

#### Where to put these two adapters

<center><img src='{{"/assets/images/논문리뷰/DreamVideo-4.png" | relative_url}}' width="100%"></center>

위의 그림은 파인튜닝동안 adapter의 삽입 위치에 따른 가중치 변화 $\Delta_l=\frac{\lVert\theta'_l\theta_l\rVert_2}{\lVert\theta_l\rVert_2}$를 비교한 결과이다.

최종적으로, identity adapter는 cross-attention layer에, motion adapter는 temporal 트랜스포머의 모든 layer에 삽입하였다.

#### Decoupled training strategy

Subject와 motion을 동시에 커스터마이징하려면 각 조합에 대해 따로따로 학습해야하므로 비용이 많이 든다.

제안하는 방법은 사전학습 모델을 고정시키고, 각 adapter만 독립적으로 학습시키면 되므로 효율적이다.

#### Inference

추론 시, 두 adapter를 결합하거나 하나의 adapter만 사용함으로써 개별적으로 커스터마이징을 할 수 있다.

## Experiments

### Qualitative Results

**Arbitrary combinations of subjects and motions**

<center><img src='{{"/assets/images/논문리뷰/DreamVideo-5.png" | relative_url}}' width="80%"></center>
<br>
Subject와 motion이 주어졌을 때의 비디오 생성 결과이다.

**Subject customization**

<center><img src='{{"/assets/images/논문리뷰/DreamVideo-6.png" | relative_url}}' width="100%"></center>
<br>
Subject의 외형을 보존하면서도 주어진 프롬프트에 적합한 비디오를 생성할 수 있다.

**Motion customization**

<center><img src='{{"/assets/images/논문리뷰/DreamVideo-7.png" | relative_url}}' width="100%"></center>
<br>
Target motion을 잘 포착하면서도 주어진 프롬프트에 적합한 비디오를 생성할 수 있다.

**Ablation studies**

<center><img src='{{"/assets/images/논문리뷰/DreamVideo-8.png" | relative_url}}' width="100%"></center>

### Quantitative Results

**Arbitrary combinations of subjects and motions**

<center><img src='{{"/assets/images/논문리뷰/DreamVideo-9.png" | relative_url}}' width="40%"></center>

**Subject customization**

<center><img src='{{"/assets/images/논문리뷰/DreamVideo-10.png" | relative_url}}' width="40%"></center>

**Motion customization**

<center><img src='{{"/assets/images/논문리뷰/DreamVideo-11.png" | relative_url}}' width="40%"></center>

**Ablation studies**

<center><img src='{{"/assets/images/논문리뷰/DreamVideo-12.png" | relative_url}}' width="40%"></center>
