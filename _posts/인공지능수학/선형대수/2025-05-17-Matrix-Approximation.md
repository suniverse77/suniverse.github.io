---
layout: single
title: "[선형대수] Matrix Approximation"
last_modified_at: 2025-05-17
categories: ["인공지능 수학"]
tags: ["선형대수"]
excerpt: "행렬 근사"
use_math: true
toc: true
toc_sticky: true
---

## Rank-k Approximation

## Spectral Norm

$$
\displaystyle\lVert A\rVert_2:=\underset{\mathbf{x}}{\max}\frac{\lVert A\bold x\rVert_2}{\lVert \bold x\rVert_2}=\underset{\mathbf{x}}{\max}\lVert A\bold x\rVert_2
$$

Matrix norm에는 여러 종류가 있으며, 그 중 spectral norm은 위와 같이 정의된다.

Spectral norm은 변환 전후의 크기 변화의 최대값으로 정의되며, 이는 $A$의 가장 큰 singular value 값과 동일하다.

> **Why?**
>
> $\lVert A\mathbf x\rVert_2=\mathbf x^\top A^\top A\mathbf x=\mathbf x^\top V(\Sigma^\top\Sigma)V^\top\mathbf x$
>
> Let, $ V^\top \mathbf x=\mathbf y$ → $\lVert A\mathbf x\rVert_2=\mathbf y^\top(\Sigma^\top\Sigma)\mathbf y=\sigma_1^2\mathbf y_1^2+\sigma_2^2\mathbf y_2^2+\cdots$
