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

> [[Paper]](https://arxiv.org/abs/2208.11970)
>
> **2022**

논문을 읽고 제가 이해한 방식으로 정리하였습니다.

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

$$
q_\phi(\mathbf z\mid\mathbf x)=\mathcal{N}(\mathbf z;\boldsymbol\mu_\phi(\mathbf x),\boldsymbol\sigma^2_\phi(\mathbf x)\mathbf I)
$$

- Encoder는 Multivariate Gaussian으로 설정

$$
p(\mathbf z)=\mathcal{N}(\mathbf z;\mathbf0,\mathbf I)
$$

- Prior는 Standard Multivariate Gaussian으로 설정

### Encoder

- 각 feature에 대한 평균과 분산을 출력함: $\mathbf z\in\mathbb{R}^D\to\boldsymbol \mu\in\mathbb{R}^D$
- $\mathbf z$의 각 $z_i$는 Gaussian을 따름

### Sampling

- Decoder의 입력을 위해서 하나의 $\mathbf z$값이 필요하기 때문에, 샘플링이 필요함
    
    샘플링하는 $\mathbf z$값마다 다른 이미지가 생성됨
    
- $z$를 단순히 샘플링하면 backpropagation이 불가능하므로, reparameterization trick 사용
    - $x=\mu+\sigma\epsilon~,~\epsilon\sim\mathcal{N}(\epsilon;0,I)$
    - $\mathbf z=\boldsymbol\mu_\phi(\mathbf x)+\boldsymbol\sigma_\phi(\mathbf x)\boldsymbol\epsilon~,~\boldsymbol\epsilon\sim\mathcal{N}(\boldsymbol\epsilon;\mathbf 0,\mathbf I)$

### Decoder

- 샘플링된 $\mathbf z$를 이용해 이미지 생성

## Hierarchical VAE

<center><img src='{{"/assets/images/논문리뷰/Diffusion-2.png" | relative_url}}' width="70%"></center>

VAE의 latent variable을 여러 층으로 쌓은 모델로, $t$시점의 latent는 $t+1$시점의 latent의 영향만 받는다는 Markov 가정 하에 진행한다.
    
$p(z_t\mid z_{t+1},z_{t+2},\dots)$는 매우 복잡하기 때문에 풀어쓸 수 없다.

### Encoder

$$
q_\phi(z_{1:T}\mid x)=q_\phi(z_{1}\mid x)\prod_{t=2}^Tq_\phi(z_t\mid z_{t-1})
$$

- 처음 step에는 $x$가 포함되어 있으므로, $\prod$ 안에 넣을 수 없음

### Decoder

$$
p(x,z_{1:T})=p(z_T)p_\theta(x\mid z_1)\prod_{t=2}^Tp_\theta(z_{t-1}\mid z_t)
$$

- Latent가 여러 층이므로 VAE와 달리 $z_{1:T}$로 표현
- 우변은 Markov 정의에 의해 나온 식임

## Diffusion Model

<center><img src='{{"/assets/images/논문리뷰/Diffusion-3.png" | relative_url}}' width="70%"></center>
