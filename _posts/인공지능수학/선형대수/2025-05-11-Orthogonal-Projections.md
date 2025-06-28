---
layout: single
title: "[선형대수] Orthogonal Projections"
last_modified_at: 2025-05-11
categories: ["인공지능 수학"]
tags: ["선형대수"]
excerpt: "정사영"
use_math: true
toc: true
toc_sticky: true
---

## Projection onto Lines

![Figure 1](/assets/images/인공지능수학/2-4. Figure1.png){: style="display:block; margin:0 auto; width: 40%; height: 40%;"}

$$
\pi_U(\mathbf{x})=\frac{\mathbf{b}\mathbf{b}^\top}{\lVert\mathbf{b}\rVert}\mathbf{x}
$$

벡터 $\mathbf{x}$의 직선인 벡터 공간 $U$ 위로의 정사영은 위와 같이 정의된다.

> **유도**
>
> $\mathbf{x}-\lambda\mathbf{b}$는 직선 벡터 공간의 basis와 직교해야한다.
> 
> $\langle\mathbf{x}-\lambda\mathbf{b},\mathbf{b}\rangle=0$

## Projection onto General Subspaces

![Figure 2](/assets/images/인공지능수학/2-4. Figure2.png){: style="display:block; margin:0 auto; width: 50%; height: 50%;"}

$$
\pi_U(\mathbf{x})=B(B^\top B)^{-1}B^\top\mathbf{x}
$$

벡터 $\mathbf{x}$의 벡터 공간 $U$ 위로의 정사영은 위와 같이 정의된다.

> **유도**
>
> $$
