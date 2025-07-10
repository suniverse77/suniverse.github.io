---
layout: single
title: "[선형대수] Gradients"
last_modified_at: 2025-05-18
categories: ["인공지능 수학"]
tags: ["선형대수"]
excerpt: "Gradient & Jacobian"
use_math: true
toc: true
toc_sticky: true
---

## Differentiation

$$
\Delta y=f(x+\Delta x)-f(x)=\alpha\Delta x
$$

입력을 흔들었을 때($\Delta x$) 출력이 흔들리는 정도($\Delta y$)의 관계를 나타낸다.

### Derivate

$$
f'(x)=\frac{df}{dx}
$$

미분 또는 도함수라고 부름

스칼라→스칼라 함수 $f:\mathbb{R}\mapsto\mathbb{R}$의 변화율

### Gradient

$$
\nabla_\bold x f(\bold x)=
\frac{df}{d\bold x}=
\begin{bmatrix}
\frac{\partial f(\bold x)}{\partial x_1}&\cdots&\frac{\partial f(\bold x)}{\partial x_n}
\end{bmatrix}
\in\mathbb{R}^n
$$

벡터 → 스칼라 함수 $f:\mathbb{R}^n\mapsto\mathbb{R}$의 각 변수에 대한 편미분을 벡터 형태로 모은 것

$$f(\bold x)=f(x_1,\dots,x_n)$$

### Jacobian

$$
J=\nabla_\bold x \bold f(\bold x)=
\frac{d\bold f}{d\bold x}=
\begin{bmatrix}
\frac{\partial \bold f(\bold x)}{\partial x_1}&\cdots&\frac{\partial \bold f(\bold x)}{\partial x_n}
\end{bmatrix}=
\begin{bmatrix}
\frac{\partial f_1(\bold x)}{\partial x_1}&\cdots&\frac{\partial f_1(\bold x)}{\partial x_n}\\
\vdots&&\vdots\\
\frac{\partial f_m(\bold x)}{\partial x_1}&\cdots&\frac{\partial f_m(\bold x)}{\partial x_n}
\end{bmatrix}
\in\mathbb{R}^{m\times n}
$$

벡터 → 벡터 함수 $\bold f:\mathbb{R}^n\to\mathbb{R}^m$의 각 변수에 대한 편미분을 행렬 형태로 모은 것

$$\bold f(\bold x)=
\begin{bmatrix}
f_1(\bold x)\\\vdots\\f_n(\bold x)
\end{bmatrix}$$

### Graident 계산법
