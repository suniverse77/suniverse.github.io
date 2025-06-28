---
layout: single
title: "[선형대수] Basis & Rank"
last_modified_at: 2025-05-05
categories: ["인공지능 수학"]
tags: ["선형대수"]
excerpt: "기저 & 차원 & rank"
use_math: true
toc: true
toc_sticky: true
---

## Basis

벡터 공간 $V$를 span하는 가장 작은 집합 $B$를 $V$의 basis라고 부르며, 특정 공간에서 좌표축 역할을 해주는 벡터들의 집합이다.


basis는 다음과 같이 표현 가능하다.
- $B$ is a minimal generating set of $V$ (더이상 뺄 수 있는 벡터가 없음)
- $B$ is a maximally linearly independent set of $V$ (벡터를 하나라도 추가하면 선형 독립이 깨짐)
    
basis는 아래의 조건을 만족해야 한다.
- Basis에 속하는 벡터들은 linearly independent해야 한다.
- Basis에 속하는 벡터들을 span 했을 때, 공간 전체를 다 표현할 수 있어야 한다.
        
$$
\text{span}(\begin{bmatrix}1\\0\end{bmatrix},\begin{bmatrix}0\\1\end{bmatrix})=\mathbb{R}^2
$$

---

## Dimension

벡터 공간 $V$의 basis vector의 개수를 $V$의 dimension이라고 부른다.

**벡터의 차원과 벡터 공간의 차원은 다르다.**

> ex1) 3차원 벡터
> 
> $$
> \mathbf{v}=\begin{bmatrix}2\\4\\6\end{bmatrix}~\to~\text{dim}(\mathbf{v})=3
> $$
>
> ex2) 3차원 벡터들이 만드는 2차원 공간
> 
> $$
> V=\text{span}(\begin{bmatrix}1\\3\\5\end{bmatrix}~,~\begin{bmatrix}2\\4\\6\end{bmatrix})~\to~\text{dim}(V)=2
> $$

Subspace의 차원은 원래 vector space의 차원보다 작거나 같아야 한다.

$$
U\subseteq V \implies \text{dim}(U)\leq \text{dim}(V)
$$

## Rank

행렬 $A$에서 선형 독립인 row 또는 column의 개수를 rank라고 부르며, 해당 벡터들이 만드는 공간의 최대 차원을 나타낸다.

> ex)
> $$
> A=\begin{bmatrix}1&2\\3&4\\5&6\end{bmatrix}
> $$
>
> Gauss Elimination을 하면
> $\text{rank}(A)=2$
> 3개의 column vector들이 span하는 공간 $V$의 차원은 2차원 평면임

### Properties of Rank

1. $\text{rank}(A)=\text{rank}(A^T)$
2. $A\in\mathbb{R}^{m\times n}$에 대해 $\text{rank}(A)=\min(m,n)$의 경우, 해당 행렬을 full rank라고 한다.
3. Square matrix $A\in\mathbb{R}^{n\times n}$에 대해 $\text{rank}(A)=n$일 경우, 해당 행렬은 invertible함
4. $\text{rank}(A)=\text{rank}(A\mid \mathbf{b})$인 경우에만, $A\mathbf{x}=\mathbf{b}$의 해를 구할 수 있다.

## Rank-Nullity Theorem

$\dim(V)=\text{rank}(A)+\text{nullity}(A)$
