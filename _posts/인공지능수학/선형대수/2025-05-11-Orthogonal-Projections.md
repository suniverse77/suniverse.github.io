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

<center><img src='{{"/assets/images/인공지능수학/2-4. Figure1.png" | relative_url}}' width="40%"></center>

$$
\pi_U(\mathbf{x})=\frac{\mathbf{b}\mathbf{b}^\top}{\lVert\mathbf{b}\rVert}\mathbf{x}
$$

벡터 $\mathbf{x}$의 직선인 vector space $U$ 위로의 정사영은 위와 같이 정의된다.

<details>
<summary><font color='blue'>공식 유도</font></summary>
<div markdown="1">

1. 직선 vector space $U$에서 $\mathbf{x}$와 거리가 가장 가까운 벡터를 $\pi_U(\mathbf{x})$라고 정의

2. $\pi_U(\mathbf{x})$는 $U$의 basis의 상수배이다.

   $$
   \pi_U(\mathbf{x})=\lambda\mathbf{b}
   $$
4. $\mathbf{x}-\lambda\mathbf{b}$는 $U$의 basis와 직교해야한다.

   $$\langle\mathbf{x}-\lambda\mathbf{b},\mathbf{b}\rangle=0\to \mathbf{x}^\top\mathbf{b}=\lambda\mathbf{b}^\top\mathbf{b}
   $$
6. Find projection
   
   $$
   \lambda=\frac{\mathbf{b}^\top\mathbf{x}}{\mathbf{b}^\top\mathbf{b}}~\to~\pi_U(\mathbf{x})=\frac{\mathbf{b}\mathbf{b}^\top}{\lVert\mathbf{b}\rVert}\mathbf{x}
   $$

</div>
</details>

## Projection onto General Subspaces

<center><img src='{{"/assets/images/인공지능수학/2-4. Figure2.png" | relative_url}}' width="50%"></center>

$$
\pi_U(\mathbf{x})=B(B^\top B)^{-1}B^\top\mathbf{x}
$$

벡터 $\mathbf{x}$의 vector space $U$ 위로의 정사영은 위와 같이 정의된다.

<details>
<summary><font color='blue'>공식 유도</font></summary>
<div markdown="1">

1. $m$차원 vector space $U$에서 $\mathbf{x}$와 가장 가까운 벡터를 $\pi_U(\mathbf{x})$라고 정의

2. $\pi_U(\mathbf{x})$는 $U$의 basis들의 선형 결합으로 표현될 수 있다.

   $$
   \pi_U(\mathbf{x})=\lambda_1\mathbf{b}_1+\cdots+\lambda_m\mathbf{b}_m=B\boldsymbol\lambda
   $$
4. $\mathbf{x}-\pi_U(\mathbf{x})$는 $U$의 basis들과 직교해야한다.

   $$
   \langle\mathbf{x}-\pi_U(\mathbf{x}),\mathbf{b}_1\rangle=0,~\cdots,~\langle\mathbf{x}-\pi_U(\mathbf{x}),\mathbf{b}_m\rangle=0
   $$
6. 행렬로 표현

   <center><img src='{{"/assets/images/인공지능수학/2-4. Figure3.png" | relative_url}}' width="80%"></center>
   
   <center><img src='{{"/assets/images/인공지능수학/2-4. Figure4.png" | relative_url}}' width="80%"></center>
   
7. Find projection

   $$
   \boldsymbol\lambda=(B^\top B)^{-1}B^\top\mathbf{x}~\to~\pi_U(\mathbf{x})=B(B^\top B)^{-1}B^\top\mathbf{x}
   $$

</div>
</details>

