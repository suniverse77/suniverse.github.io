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

ELBO는 

$$
\mathbb{E}_{q_\phi(x\midz)}\bigg[\log\frac{p(x,z)}{q_\phi(z\midx)}\bigg]=
\mathbb{E}_{q_\phi(x\mid z)}\bigg[\log p_\theta(x\midz)\bigg]
-D_{KL}(q_\phi(z\midx)\mid\mid p(z))
$$


<details>
<summary><font color='blue'>공식 유도</font></summary>
<div markdown="1">

1. 식

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

</div>
</details>

- Expectation: $\mathbb{E}_{f(z)\sim g(z)}[f(z)]=\int f(z)g(z)dz$


- 위의 식은 $\rm Evidence=ELBO+KL$으로 표현됨
- 우리의 목적은 $q_\phi(z|x)$를 $p(z|x)$에 근사시키는 것임
    
    $\rm Evidence$는 상수이므로, $\rm KL$ 최소화 = $\rm Evidence$ 최대화
    
    $\rm$$\rm KL$은 직접 계산할 수 없으므로, $\rm Evidence$를 최대화하는 방식으로 진행
    
- $\rm KL$은 항상 0 이상이기 때문에, $\rm Evidence\geq ELBO$가 됨

### ELBO

$$
\mathbb{E}_{q_\phi(x|z)}\bigg[\log\frac{p(x,z)}{q_\phi(z|x)}\bigg]
=
\mathbb{E}_{q_\phi(x|z)}\bigg[\log p_\theta(x|z)\bigg]
-
D_{KL}(q_\phi(z|x)||p(z))
$$

- $\rm ELBO$를 위처럼 풀어 쓸 수 있음
- 위의 식은 $\rm ELBO=Reconstruction-Prior~matching$으로 표현됨
- $\rm Reconstruction$은 우리가 전달한 latent variable로부터 나온 출력이 입력 데이터와 얼마나 유사한지를 의미함 (복원력)
- $\rm Prior~matching$은 우리가 설계한 $q_\phi(z|x)$가 적어도 사전 설정한 분포를 따르도록 강제함
    
    ex) $p(z)$를 사전에 가우시안으로 정의했으면 $q_\phi(z|x)$도 가우시안이 되도록 설정
    
- 학습에는 경사 하강법을 사용하므로, 실제 손실 함수로는 $\min(-\text{ELBO})$를 사용
    
    $\rm Reconstruction$과 $-\rm Prior~matching$이 모두 음수임 → $\rm ELBO$도 항상 음수

## VAE (Variational Auto Encoder)

## Hierarchical VAE

## Diffusion Model
