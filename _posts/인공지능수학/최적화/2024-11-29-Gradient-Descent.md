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

$$
\theta_t=\theta_{t-1}-\alpha\nabla_\theta\mathcal{L}(\theta)
$$

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

한 개의 샘플에 대해 손실을 계산한 후, 파라미터를 업데이트한다.

이 때, 샘플 1개는 무작위로 선택한다.

## Optimizer

### Momentum

$$
\theta_t=\theta_{t-1}-\eta m_t
~,~
m_t=\gamma m_{t-1}+(1-\gamma)\nabla_\theta
\mathcal{L}(\theta)
$$

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

- Momentum과 AdaGrad를 융합한 기법
- 스케일이 다른 파라미터들에 대해 모든 파라미터가 균형있게 학습되도록 유도
- $m_t$는 1차 모멘텀으로, gradient에 대한 EMA임
- $v_t$는 2차 모멘텀으로, graident의 크기에 대한 EMA임