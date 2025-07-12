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
    - $p(\mathbf x)=\int p(\mathbf x,\mathbf z)\mathbf z$으로, 모든 가능한 latent variable에 대해서 $\mathbf x$의 적분임
    - 만약 $\mathbf z\in\mathbb{R}^{100}라면, $\mathbf z\in\mathbb{R}^{100}\to p(\mathbf x)=\int_{z_{100}}\cdots\int_{z_{1}}p(\mathbf x,\mathbf z)d\mathbf z$이기 때문에 적분이 어려워진다.

$p(z\mid x)$를 직접 찾을 수 없으니 모델을 이용해 근사한다.

$$
q_\phi(z\mid x)\sim p(z\mid x)
$$

## ELBO (Evidence Lower BOund)

## VAE (Variational Auto Encoder)

## Hierarchical VAE

## Diffusion Model
