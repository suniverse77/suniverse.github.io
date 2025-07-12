---
layout: single
title: "[논문리뷰] Understanding Diffusion Models: A Unified Perspective"
last_modified_at: 2025-03-10
categories: ["논문리뷰"]
tags: ["Image Generation"]
excerpt: ""
use_math: true
toc: true
toc_sticky: true
---

## Bayes' Rule

$$
p(z|x)=\frac{p(x|z)p(z)}{p(x)}=\frac{p(x,z)}{p(x)}
$$

- Prior $p(z)$는 우리가 latent의 분포 Gaussian Distribution를 사전에 임의로 정의할 수 있기 때문에 알고 있는 값이다. *ex) Normal Distribution, Gaussian Distribution*
- Conditional Likelihood $p(x\mid z)$는 latent

- Posterior Distribution $p(z\mid x)$는 주어진 데이터 $x$를 가장 완벽하게 표현하는 latent variable
- Evidence $p(x)$는 latent의 차원이 매우 크다면 계산이 불가능해진다.
    - $p(\mathbf x)=\int p(\mathbf x,\mathbf z)\mathbf z$으로, 모든 가능한 latent variable에 대해서 $\mathbf x$의 적분이다.
    - 만약 $\mathbf z\in\mathbb{R}^{100}$라면, $\mathbf z\in\mathbb{R}^{100}\to p(\mathbf x)=\int_{z_{100}}\cdots\int_{z_{1}}p(\mathbf x,\mathbf z)d\mathbf z$이기 때문에 적분이 어려워진다.

$p(z\mid x)$를 직접 찾을 수 없으니 모델을 이용해 근사한다.

$$
q_\phi(z\mid x)\sim p(z\mid x)
$$

## ELBO (Evidence Lower BOund)

ELBO는 log evidence를 직접 계산할 수 없을 때, 이를 하한선으로 근사하는 데 사용된다.

$$
\mathbb{E}_{q_\phi(x\mid z)}\bigg[\log\frac{p(x,z)}{q_\phi(z\mid x)}\bigg]=
\mathbb{E}_{q_\phi(x\mid z)}\bigg[\log p_\theta(x\mid z)\bigg]
-D_{KL}(q_\phi(z\mid x)\mid\mid p(z))
$$

ELBO는 재구성 term과 정규화 term으로 구성된다.

- 재구성 term: 우리가 전달한 latent variable로부터 나온 출력이 입력 데이터와 얼마나 유사한지를 의미한다. (복원력)
- 정규화 term: 우리가 설계한 $q_\phi(z\mid x)$가 적어도 사전 설정한 분포를 따르도록 강제한다.

> ex) $p(z)$를 사전에 가우시안으로 정의했으면 $q_\phi(z\mid x)$도 가우시안이 되도록 설정

학습에는 경사 하강법을 사용하므로, 실제 손실 함수로는 $\min(-\text{ELBO})$를 사용한다.

재구성 term과 정규화 term 모두 음수의 값을 가지기 때문에 ELBO도 항상 음수이다.

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
    \log p(x)=\int q_\phi(z\mid x)(\log p(x))dz=\mathbb{E}_{q_\phi(z\mid x)}\big[\log p(x)\big]
    $$
4. $\log p(x)$를 product rule을 이용해 변환한다.

    $$
    \log p(x)=\mathbb{E}_{q_\phi(z\mid x)}\bigg[\log\frac{p(x,z)}{q_\phi(z\mid x)}\bigg]
    +D_{KL}(q_\phi(z\mid x)\mid\mid p(z\mid x))
    $$

- 위의 식은 **Evidence = ELBO + KL**으로 표현된다.
- 우리의 목적은 $q_\phi(z\mid x)$를 $p(z\mid x)$에 근사시키는 것임
    
Evidence는 상수이므로, KL을 최소화하는 것은 Evidence를 최대화하는 것과 같다.
    
KL은 직접 계산할 수 없으므로, Evidence를 최대화하는 방식으로 진행
    
KL term은 항상 0 이상이기 때문에, $\rm Evidence\geq ELBO$가 됨

</div>
</details>

## VAE (Variational Auto Encoder)

$$
q_\phi(\mathbf z\mid\mathbf x)=\mathcal{N}(\mathbf z;\boldsymbol\mu_\phi(\mathbf x),\boldsymbol\sigma^2_\phi(\mathbf x)\mathbf I)
$$

- Encoder는 Multivariate Gaussian으로 설정

$$
p(\bold z)=\mathcal{N}(\bold z;\bold0,\bold I)
$$

- Prior는 Standard Multivariate Gaussian으로 설정

### Encoder

- 각 feature에 대한 평균과 분산을 출력함: $\bold z\in\mathbb{R}^D\to\boldsymbol \mu\in\mathbb{R}^D$
- $\bold z$의 각 $z_i$는 Gaussian을 따름

### Sampling

- Decoder의 입력을 위해서 하나의 $\bold z$값이 필요하기 때문에, 샘플링이 필요함
    
    샘플링하는 $\bold z$값마다 다른 이미지가 생성됨
    
- $z$를 단순히 샘플링하면 backpropagation이 불가능하므로, reparameterization trick 사용
    - $x=\mu+\sigma\epsilon~,~\epsilon\sim\mathcal{N}(\epsilon;0,I)$
    - $\bold z=\boldsymbol\mu_\phi(\bold x)+\boldsymbol\sigma_\phi(\bold x)\boldsymbol\epsilon~,~\boldsymbol\epsilon\sim\mathcal{N}(\boldsymbol\epsilon;\bold 0,\bold I)$

### Decoder

- 샘플링된 $\bold z$를 이용해 이미지 생성

## Hierarchical VAE



## Diffusion Model


