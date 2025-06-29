---
layout: single
title: "[선형대수] Eigenvalues & Eigenvectors"
last_modified_at: 2025-05-14
categories: ["인공지능 수학"]
tags: ["선형대수"]
excerpt: "고유값과 고유벡터"
use_math: true
toc: true
toc_sticky: true
---

$$
A\mathbf{x}=\lambda\mathbf{x}
$$

Square matrix $A$에 대해 $\mathbf{0}$이 아닌 $\mathbf{x}$가 위의 식을 만족할 때, $\lambda$를 eigenvalue, $\mathbf{x}$를 eigenvector라고 한다.

즉, $\mathbf{x}$는 행렬 $A$에 의한 선형 변환 전후의 방향이 변하지 않고, 길이만 $\lambda$배되는 벡터이다.

행렬의 고유값과 고유벡터를 찾기 위해서는 $\text{det}(A-\lambda I)=0$을 만족하는 해를 찾으면 된다.

> **Why?**
>
> $A\mathbf{x}=\lambda\mathbf{x}$ → $(A-\lambda I)\mathbf{x}=0$
>
> $\mathbf{x}\not=\mathbf{0}$이므로, $\text{det}(A-\lambda I)=0$이어야 한다.

> ex) 
>
> $$
> A=\begin{batmrix}4&2\\1&3\end{bmatrix}
> $$
> 
> ---
> 1. $\text{det}(A-\lambda I)=0$의 해를 구함
>
>    $$
>    \text{det}(A-\lambda I)=\begin{vmatrix}4-\lambda&2\\1&3-\lambda\end{vmatrix}=(4-\lambda)(3-\lambda)-2=0
>    $$
>
>    $\lambda_1=5,~\lambda_2=2$
> 2. 고유값에 대응하는 고유벡터를 구함
>    
>    $$
>    \lambda_1=5:~(A-5I)\mathbf{x}=0~\to~\begin{bmatrix}\begin{array}{cc|c}2&2&0\\1&1&0\end{array}\end{bmatrix}
>    $$
>
>    $$
>    x_1=-x_2~\to~\begin{bmatrix}x_1\\x_2\end{bmatrix}=c\begin{bmatrix}1\\-1\end{bmatrix}
>    $$
