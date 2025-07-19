---
layout: single
title: "[논문리뷰] An Image is Worth One Word: Personalizing Text-to-Image Generation using Textual Inversion"
last_modified_at: 2025-02-02
categories: ["논문리뷰"]
tags: ["Computer Vision"]
excerpt: "ICLR 2023"
use_math: true
toc: true
toc_sticky: true
---

**ICLR 2023** 
[[Paper]](https://arxiv.org/abs/2208.01618)

<details>
<summary><font color='#FF8C00'>📝 Summary</font></summary>
<div markdown="1">
<br>
파인튜닝은 전체 가중치를 업데이트해야 하지만, 실제로 업데이트할 때 필요한 가중치는 low-rank로 표현이 가능하다.
- 가중치 변화량을 2개의 작은 행렬로 분해 → $\Delta W=BA$
- 원래 가중치 $W_0$를 고정하고, $B$와 $A$만 학습

추론 시 가중치를 $W = W_0 + BA$ 하나로 합쳐서 사용하면 되기 때문에 추론 지연이 없다.

</div>
</details>

## Introduction

**기존 연구들의 한계점**

- T2I 모델은 사용자가 원하는 대상을 텍스트로 얼마나 잘 설명하느냐에 의해 그 활용이 제한된다.
- 대규모 모델에 새로운 개념을 도입하는 일은 어렵다.
    - 매번 새로운 개념마다 모델을 다시 학습시키는 것은 비용이 많이 든다.
    - 소수의 예시로 파인튜닝을 하면 기존 지식을 망각하는 catastrophic forgetting 문제가 발생한다.

**제안하는 방법**

- 사용자가 제공한 개념을 통해 새로운 이미지를 만드는 personalized text-to-image generation이라는 새로운 task를 제안한다.
- 사전학습된 T2I 모델의 텍스트 임베딩 공간 안에서 새로운 단어(pseudo-word)를 찾는 방법을 제안한다. 즉, 새로운 개념을 나타낼 수 있는 새로운 임베딩 벡터를 찾는 것이 목표이다.
- 생성 모델 자체는 건드리지 않기 때문에 기존 모델의 텍스트 이해력과 일반화 능력을 보존할 수 있다.

## Methods

<center><img src='{{"/assets/images/논문리뷰/Textual-Inversion-1.png" | relative_url}}' width="100%"></center>

### 1. Latent Diffusion Models

본 연구에서는 LDM을 사용하였다.

$$
L_{LDM} := \mathbb{E}_{z \sim \mathcal{E}(x),\, y,\, \epsilon \sim \mathcal{N}(0,1),\, t} \left[ \left\| \epsilon - \epsilon_\theta(z_t, t, c_\theta(y)) \right\|_2^2 \right]

$$

텍스트 인코더 $c_\theta$로는 BERT를 사용하였고, $y$는 텍스트 임베딩을 의미한다.

학습 중에 $c_\theta$와 $\epsilon_\theta$는 함께 최적화된다.

### 2. Text Embeddings

입력 문자열의 각 단어나 sub-word(단어의 일부)는 토큰으로 변환되며, 이 토큰들은 미리 정의된 dictionary 내의 인덱스이다.

각 토큰은 고유한 임베딩 벡터와 연결되어 있으며, 이 벡터는 인덱스를 기반으로 조회하여 가져올 수 있다.

학습하고자 하는 새로운 개념을 나타내기 위해 대체 문자열(placeholder string) $S_*$를 지정하고, 이에 대응되는 임베딩 벡터 $v_*$를 출력하도록 하여 개념을 어휘에 주입한다.

### 3. Textual Inversion

새로운 임베딩 $v_*$를 찾기 위해 3~5장의 소량의 이미지를 사용한다.

$$
v_* =\underset{v}{ \arg\min}~\mathbb{E}_{z \sim \mathcal{E}(x),\, y,\, \epsilon \sim \mathcal{N}(0,1),\, t} \left[ \left\| \epsilon - \epsilon_\theta(z_t, t, c_\theta(y)) \right\|_2^2 \right]
$$

생성 모델과 텍스트 인코더의 가중치는 고정하고, 위의 LDM loss를 최소화하도록 $v_*$만 최적화한다.

즉, 주어진 입력 이미지를 재구성하도록 $S_*$에 대응되는 최적의 텍스트 임베딩 $v_*$를 찾는 것이 목표이다.

일반적인 T2I 모델은 텍스트를 입력으로 주면 그와 대응되는 이미지를 만들지만, textual inversion에서는 이미지를 주고, 그 이미지를 가장 잘 설명할 수 있는 텍스트 임베딩을 역으로 찾아내기 때문에 inversion이라고 부른다.

## Experiments

### Qualitative Comparisons and Applications

### Quantitative Analysis
