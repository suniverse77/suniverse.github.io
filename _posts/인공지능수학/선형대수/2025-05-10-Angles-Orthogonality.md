---
layout: single
title: "[선형대수] Angles & Orthogonality"
last_modified_at: 2025-05-06
categories: ["인공지능 수학"]
tags: ["선형대수"]
excerpt: "행렬 기초 & 곱셈"
use_math: true
toc: true
toc_sticky: true
---

## Angle

$$
\theta=\cos^{-1}\big(\frac{\langle\mathbf{x},\mathbf{y}\rangle}{\lVert\mathbf{x}\rVert\cdot\lVert\mathbf{y}\rVert}\big)
$$

Inner product space에서 두 벡터의 각도는 위와 같이 정의된다.

## Orthogonality

두 벡터 $\mathbf{x}$, $\mathbf{y}$의 내적이 0이라면, 두 벡터는 **orthogonal**하다고 한다.

각 벡터의 크기가 1이라면, **orthonormal**하다고 한다.

Square matrix $A$의 column vector들이 orthonormal하면, $A$를 **orthogonal matrix**라고 한다.

- $A$, $B$가 orthogonal matrix라면, $AB$도 orthogonal matrix다.
- $A$가 orthogonal matrix라면, $\text{det}(A)=\pm1$이다.

## Orthonormal Basis

orthonormal basis로 변환하는 방법에는 크게 2가지가 있다.

### 1. $B$에 대해 Gauss Elimination 수행

### 2. Gram method 적용
