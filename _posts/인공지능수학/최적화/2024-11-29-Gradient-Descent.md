---
layout: single
title: "[최적화] Gradient Descent"
last_modified_at: 2024-11-29
categories: ["인공지능 수학"]
tags: ["최적화"]
excerpt: "경사 하강법"
use_math: true
toc: true
toc_sticky: true
---

## Gradient Descent

<figure style="text-align: center;">
  <img src='{{ "/assets/images/인공지능수학/5-1. Figure1.png" | relative_url }}' width="60%">
  <figcaption>출처: https://medium.com/@anmoltalwar/gradient-descent-72b8d138cabe</figcaption>
</figure>

함수의 최소값을 찾기 위한 최적화 알고리즘으로, 함수의 기울기를 계산해서 기울기가 낮아지는 방향으로 조금씩 이동하는 방법을 사용한다.

$$
\theta_t=\theta_{t-1}-\alpha\nabla_\theta\mathcal{L}(\theta)
$$

여기서 $\alpha$는 learning rate라고 부르며, 한 번에 이동하는 보폭을 의미한다.

<figure style="text-align: center;">
  <img src='{{ "/assets/images/인공지능수학/5-1. Figure2.png" | relative_url }}' style="width:60%;">
  <figcaption>출처: https://fabiorosato.com/blog/navigating-success-as-gradient-descent</figcaption>
</figure>

함수의 최소값을 찾을 때 단순히 "미분 = 0"을 계산하지 않는 이유는 아래와 같다.

- 실제 모델이 사용하는 함수는 매우 복잡하기 때문에 "미분 = 0"인 지점을 수식으로 직접 계산하기가 불가능한 경우가 많다.
- "미분 = 0"인 지점이 global minimum이 아니라 local minimum일 수도 있다.

<figure style="text-align: center;">
  <img src='{{ "/assets/images/인공지능수학/5-1. Figure3.png" | relative_url }}' width="60%">
  <figcaption>출처: https://variety82p.tistory.com/category/%E2%9A%A1AI/AI</figcaption>
</figure>

### Full-batch Gradient Descent

$$
\theta_t=\theta_{t-1}-\alpha\sum_{n=1}^N\nabla_\theta
\mathcal{L}_n(\theta)
$$

전체 $N$개의 샘플에 대해 손실을 계산한 후, 파라미터를 한 번만 업데이트한다.

### Mini-batch Gradient Descent

$$
\theta_t=\theta_{t-1}-\alpha\sum_{n\in B}\nabla_\theta\mathcal{L}_n(\theta)~,~|B|<N
$$

일부 샘플 묶음 (미니 배치)에 대해 손실을 계산한 후, 파라미터를 업데이트하는 과정을 배치 개수만큼 반복한다.

1. 첫 번째 샘플 묶음에 대해 손실을 계산한 후, 파라미터를 업데이트
2. 두 번째 샘플 묶음에 대해 손실을 계산한 후, 파라미터를 업데이트
3. ...

### Stochastic Gradient Descent

$$
\theta_t=\theta_{t-1}-\alpha\nabla_\theta\mathcal{L}_i(\theta)
$$

매 step마다 무작위로 선택된 한 개의 샘플에 대해 손실을 계산한 후, 파라미터를 업데이트한다.

## Optimizer

### Momentum

$$
\theta_t=\theta_{t-1}-\eta m_t
~,~
m_t=\gamma m_{t-1}+(1-\gamma)\nabla_\theta
\mathcal{L}(\theta)
$$

이전 방향을 반영해 진동을 줄이고 빠르게 수렴하도록 유도

- $m_t$는 모멘텀 term으로, 파라미터가 어떤 방향으로 얼마나 빠르게 바뀌는지를 나타내는 값임
    
    $m_t$는 이전 step의 gradient와 현재 step의 gradient의 EMA로 이루어져 있음
    
- $\gamma$는 모멘텀 계수로, 이전 방향에 가중치를 얼마나 부여할지를 나타냄
    
    일반적으로 $\gamma=0.9$로 설정

### Adam (Adaptive Moment Estimation)

$$
\theta_t=\theta_{t-1}-\eta\frac{m_{t-1}}{\sqrt{v_{t-1}}+\epsilon}
\\
m_t=\gamma_1 m_{t-1}+(1-\gamma_1)\nabla_\theta
\mathcal{L}(\theta)
~,~
v_t=\gamma_2 v_{t-1}+(1-\gamma_2)\lVert\nabla_\theta
\mathcal{L}(\theta)\rVert^2
$$

1차, 2차 모멘텀을 활용해 학습률을 적응적으로 조정

- Momentum과 AdaGrad를 융합한 기법
- 스케일이 다른 파라미터들에 대해 모든 파라미터가 균형있게 학습되도록 유도
- $m_t$는 1차 모멘텀으로, gradient에 대한 EMA임
- $v_t$는 2차 모멘텀으로, graident의 크기에 대한 EMA임