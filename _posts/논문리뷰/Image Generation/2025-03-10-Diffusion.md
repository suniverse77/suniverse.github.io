---
layout: single
title: "[논문리뷰] Understanding Diffusion Models: A Unified Perspective"
last_modified_at: 2025-03-10
categories: ["논문리뷰"]
tags: ["Diffusion", "Image Generation"]
excerpt: "2022"
use_math: true
toc: true
toc_sticky: true
---

**2022** 
[[Paper]](https://arxiv.org/abs/2208.11970)

## Bayes' Rule

$$
p(z\mid x)=\frac{p(x\mid z)p(z)}{p(x)}=\frac{p(x,z)}{p(x)}
$$

- Prior $p(z)$는 latent의 사전 분포로, Gaussian 분포처럼 사전에 임의로 정의할 수 있기 때문에 알고 있는 값이다.
- Likelihood $p(x\mid z)$는 latent

- Posterior Distribution $p(z\mid x)$는 주어진 데이터 $x$를 가장 완벽하게 표현하는 latent의 분포로, 직접 계산이 어렵기 때문에 일반적으로 근사하여 구한다.
- Evidence $p(x)$는 모든 가능한 $z$에 대해 joint를 적분한 marginal likelihood이다.
    
    $$
    p(\mathbf x)=\int p(\mathbf x,\mathbf z)\mathbf z
    $$
    
    latent의 차원이 $\mathbf z\in\mathbb{R}^{100}$처럼 매우 크면 $\mathbf z\in\mathbb{R}^{100}\to p(\mathbf x)=\int_{z_{100}}\cdots\int_{z_{1}}p(\mathbf x,\mathbf z)d\mathbf z$처럼 다중 적분을 해야하기 때문에 사실상 계산이 불가능해진다.

따라서, 실제 학습에서는 $p(z\mid x)$를 직접 계산할 수 없기 때문에 모델을 이용해 근사한다.

$$
q_\phi(z\mid x)\sim p(z\mid x)
$$

## ELBO (Evidence Lower BOund)

log evidence는 직접 계산하기 어렵기 때문에, 그 하한인 ELBO를 최대화함으로써 근사한다.

$$
\mathbb{E}_{q_\phi(z\mid x)}\bigg[\log\frac{p(x,z)}{q_\phi(z\mid x)}\bigg]=
\mathbb{E}_{q_\phi(z\mid x)}\bigg[\log p_\theta(x\mid z)\bigg]
-D_{KL}(q_\phi(z\mid x)\mid\mid p(z))
$$

ELBO는 재구성 term $\mathbb{E}_{q\_\phi(z\mid x)}\big[\log p\_\theta(x\mid z)\big]$과 정규화 term $D\_{KL}(q\_\phi(z\mid x)\mid\mid p(z))$으로 구성된다.

- 재구성 term: latent variable $z$로부터 복원한 $x$가 실제 $x$와 얼마나 유사한지를 측정한다.
- 정규화 term: 설계한 $q_\phi(z\mid x)$가 적어도 사전 설정한 분포 $p(z)$를 따르도록 강제한다.

학습에는 경사 하강법을 사용하므로, 실제 손실 함수로는 $\min(-\text{ELBO})$를 사용한다.

<details>
<summary><font color='blue'>공식 유도</font></summary>
<div markdown="1">

1. 항등식

    $$
    \log p(x)=\log p(x)
    $$
2. $\int q_\phi(z\mid x)dz=1$이므로, 우변에 곱한다.

    $$
    \log p(x)=\log p(x)\int q_\phi(z\mid x)dz
    $$
3. $\log p(x)$를 적분 안으로 넣으면 기대값으로 표현할 수 있다.

    $$
    \begin{equation}
    \log p(x)=\int q_\phi(z\mid x)(\log p(x))dz=\mathbb{E}_{q_\phi(z\mid x)}\big[\log p(x)\big]
    \end{equation}
    $$

---

1. Bayes' Rule을 사용해 $\log p(z\mid x)$를 아래와 같이 표현할 수 있다.

    $$
    p(z\mid x)=\frac{p(x|z)p(z)}{p(x)}=\frac{p(x,z)}{p(x)}
    ~\rightarrow~\log p(z\mid x)=\log p(x,z)-\log p(x)
    $$
2. KL은 아래와 같이 정의된다.

    $$
    D_{KL}(q_\phi(z\mid x)\mid\mid \log p(z\mid x))=
    \mathbb{E}_{q_\phi(z\mid x)}\bigg[\log q_\phi(z\mid x)-\log p(z\mid x)\bigg]
    $$
3. KL 정의에 Bayes' Rule로 정리한 수식 대입

    $$
    D_{KL}(q_\phi(z\mid x)\mid\mid \log p(z\mid x))
    =\mathbb{E}_{q_\phi(z\mid x)}\bigg[\log q_\phi(z\mid x)-\big(\log p(x,z)-\log p(x)\big)\bigg]
    \\ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    =\mathbb{E}_{q_\phi(z\mid x)}\bigg[\log q_\phi(z\mid x)-\log p(x,z)\bigg]+\log p(x)
    $$

4. 이항

    $$
    \begin{equation}
    \log p(x)=\mathbb{E}_{q_\phi(x\mid z)}\bigg[\log p_\theta(x\mid z)\bigg]-D_{KL}(q_\phi(z\mid x)\mid\mid p(z))
    \end{equation}
    $$

---

식 (1)과 식 (2)를 통해 아래와 같은 결과를 얻을 수 있다.

$$
\log p(x)=\mathbb{E}_{q_\phi(z\mid x)}\bigg[\log\frac{p(x,z)}{q_\phi(z\mid x)}\bigg]
+D_{KL}(q_\phi(z\mid x)\mid\mid p(z\mid x))
$$

위의 식은 **Evidence = ELBO + KL**으로 표현되며, KL term은 항상 0 이상이기 때문에 <mark style='background-color: fff5b1'>$\rm Evidence\geq ELBO$</mark> 이다.
    
Evidence는 상수이므로, KL을 최소화하는 것은 Evidence를 최대화하는 것과 같다.
    
KL을 직접 계산할 수 없기 때문에 Evidence를 최대화하여 간접적으로 $q_\phi(z\mid x)$를 $p(z\mid x)$에 근사시킨다.

</div>
</details>

## VAE (Variational Auto Encoder)

<center><img src='{{"/assets/images/논문리뷰/Diffusion-1.png" | relative_url}}' width="70%"></center>

### Encoder

고차원 데이터 $x$를 저차원 잠재변수 $z$로 압축해 의미적인 특징만 보존하는 역할을 한다.

이때, 직접 계산이 불가능한 사후분포 $p(z\mid x)$를 인코더의 뉴럴 네트워크로 근사한다.

$$
q_\phi(\mathbf z\mid\mathbf x)=\mathcal{N}(\mathbf z;\boldsymbol\mu_\phi(\mathbf x),\boldsymbol\sigma^2_\phi(\mathbf x)\mathbf I)
$$

인코더의 출력은 각 feature에 대한 평균과 분산으로, $\mathbf z\in\mathbb{R}^D$이면, $\boldsymbol\mu\in\mathbb{R}^D$이다.

### Sampling

Decoder는 하나의 입력 $\mathbf{z}$가 필요하기 때문에, 확률 분포에서 하나의 값을 샘플링해야 된다. 이때, 샘플링하는 $\mathbf{z}$값마다 디코더의 결과가 달라진다.
    
$$
\mathbf z=\boldsymbol\mu_\phi(\mathbf x)+\boldsymbol\sigma_\phi^2(\mathbf x)\odot\boldsymbol\epsilon
$$

$\mathbf{z}$를 단순히 샘플링하면 미분이 안되서 역전파가 불가능하므로, 위와 같이 reparameterization trick을 사용한다.

### Decoder

주어진 $z$로부터 데이터 분포를 모델링한다.

$$
p_\theta(\mathbf{x}\mid\mathbf{z})
$$

## Hierarchical VAE

<center><img src='{{"/assets/images/논문리뷰/Diffusion-2.png" | relative_url}}' width="70%"></center>
<br>
VAE의 latent variable을 여러 층으로 쌓은 모델로, 상하위 잠재 변수 간의 조건부 독립성을 1차 마르코프 가정으로 둔다.

즉, $t$시점의 latent는 $t+1$시점 또는 $t-1$ 시점의 latent의 영향만 받는다.

### Encoder

$$
q_\phi(z_{1:T}\mid x)=q_\phi(z_{1}\mid x)\prod_{t=2}^Tq_\phi(z_t\mid z_{t-1})
$$

$p(z_{1:T})=p(z_1,z_2,\dots,z_T)$는 joint 분포를 의미한다.

$t=1$ step은 $x$에 의존하므로, 곱셈 기호 안으로 합치지 않는다.

$t\geq2$ step은 바로 이전 step $t-1$에만 의존한다.

### Decoder

$$
p(x,z_{1:T})=p(z_T)p_\theta(x\mid z_1)\prod_{t=2}^Tp_\theta(z_{t-1}\mid z_t)
$$

$z_T$는 사전 분포 $p(z_T)$에서 샘플링한다.

$t\geq2$ step은 바로 이전 step $t+1$에만 의존한다.

마지막에는 $z_1$으로부터 $x$를 복원한다.

## Diffusion Model

<center><img src='{{"/assets/images/논문리뷰/Diffusion-3.png" | relative_url}}' width="70%"></center>
<br>
Hierarchical VAE에 3가지 제약 조건을 추가한 것으로 이해할 수 있다.

1. latent 차원과 data 차원이 동일하다. ($\mathbf z$를 $\mathbf x$로 표기)
2. Encoder는 학습하지 않는다. (forward process에 학습 가능한 파라미터가 없음)
3. $T$ step에서는 standard gaussian이 된다.

<mark style='background-color: fff5b1'>Diffusion process의 각 step이 가우시안 분포로 구성될 경우, time step 폭이 충분히 작으면 reverse process도 가우시안 분포로 근사할 수 있다.</mark>

### Encoder (Forward process)

$$
q(\mathbf x_{1:T}\mid\mathbf x_0):=\prod_{t=1}^Tq(\mathbf x_t\mid\mathbf x_{t-1})
$$

Markov로 이루어져 있으며, $\mathbf x_t$는 $\mathbf x_{t-1}$에만 의존한다.

HVAE 수식에서 학습 파라미터가 사라졌으며, $z$ 기호를 사용하지 않기 때문에 초기 step도 곱셈 기호 안에 넣을 수 있다.

### Decoder (Backward process)

$$
p_\theta(\mathbf x_{0:T}):=p(\mathbf x_T)\prod_{t=1}^Tp_\theta(\mathbf x_{t-1}\mid\mathbf x_t)
~,~p(\mathbf x_T)\sim\mathcal{N}(\mathbf0,\mathbf I)
$$

Markov로 이루어져 있으며, $\mathbf x_{t-1}$에서 $\mathbf x_t$를 복원한다.

$$
p_\theta(\mathbf x_{t-1}\mid\mathbf x_t):=\mathcal{N}(\mathbf x_{t-1};\boldsymbol{\mu}_\theta(\mathbf x_t,\mathbf x_0),\boldsymbol\Sigma_\theta(\mathbf x_t,t))
$$

이전 시점 $\mathbf{x}_{t-1}$의 분포를 복원하기 위해 평균과 분산을 예측해야 한다.
