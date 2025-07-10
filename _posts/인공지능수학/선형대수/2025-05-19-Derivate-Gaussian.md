---
layout: single
title: "[선형대수] Derivate of Gaussian"
last_modified_at: 2025-05-19
categories: ["인공지능 수학"]
tags: ["선형대수"]
excerpt: "가우시안 미분"
use_math: true
toc: true
toc_sticky: true
---

데이터 $X=\lbrace\mathbf{x}_1,\cdots\mathbf{x}_n\rbrace$ 가 있을 때, \mathbf{x}\in\mathbb{R}^d에 대한 가우시안 분포는 아래와 같은 식으로 표현된다.

$$
p(\mathbf{x};\boldsymbol\mu,\boldsymbol\Sigma)=\frac{1}{\sqrt{(2\pi)^d|\boldsymbol\Sigma|}}\exp(-\frac{(\mathbf{x}-\boldsymbol\mu)^\top\boldsymbol\Sigma^{-1}(\mathbf{x}-\boldsymbol\mu)}{2})
$$

### 1. 목적 함수 설계 (MLE)

$$
\log p(\mathbf{x};\boldsymbol\mu,\boldsymbol\Sigma)=-\frac{d}{2}\log(2\pi)-\frac{1}{2}\log|\Sigma|-\frac{1}{2}(\mathbf{x}-\boldsymbol\mu)^\top\boldsymbol\Sigma^{-1}(\mathbf{x}-\boldsymbol\mu)
$$

$$
\underset{\boldsymbol\mu,\boldsymbol\Sigma}{\text{argmax}}~\log p(\mathbf{x};\boldsymbol\mu,\boldsymbol\Sigma)
$$

### 2. $\boldsymbol\mu$에 대한 미분

$$
\nabla_{\boldsymbol\mu}\log p
=\nabla_{\boldsymbol\mu}\bigg(-\frac{1}{2}(\mathbf{x}-\boldsymbol\mu)^\top\boldsymbol\Sigma^{-1}(\mathbf{x}-\boldsymbol\mu)\bigg)
=(\mathbf{x}-\boldsymbol\mu)^\top\boldsymbol\Sigma^{-1}
$$

### 3. $\boldsymbol\Sigma$에 대한 미분

$$
\nabla_{\boldsymbol\Sigma}\log p=-\frac{1}{2}(\boldsymbol\Sigma^{-1}-\boldsymbol\Sigma^{-1}(\mathbf{x}-\boldsymbol\mu)(\mathbf{x}-\boldsymbol\mu)^\top\boldsymbol\Sigma^{-1})
$$

- $\nabla_{\boldsymbol\Sigma}\bigg(-\frac{1}{2}\log|\Sigma|\bigg)=-\frac{1}{2}\Sigma^{-1}$
- $\nabla_{\boldsymbol\Sigma}\bigg(-\frac{1}{2}(\mathbf{x}-\boldsymbol\mu)^\top\boldsymbol\Sigma^{-1}(\mathbf{x}-\boldsymbol\mu)\bigg)=-\frac{1}{2}\boldsymbol\Sigma^{-1}(\mathbf{x}-\boldsymbol\mu)(\mathbf{x}-\boldsymbol\mu)^\top\boldsymbol\Sigma^{-1}$

### 4. 


