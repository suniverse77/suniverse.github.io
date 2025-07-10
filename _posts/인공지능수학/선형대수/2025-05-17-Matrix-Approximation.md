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

행렬 $A$의 SVD 분해는 아래와 같이 표현할 수 있다.

$$
A=U\Sigma V^\top=\sum_{i=1}^r\sigma_i\mathbf{u}_i\mathbf{v}_i^\top
$$

여기서 $A_i=\mathbf{u}_i\mathbf{v}_i^\top$는 rank-1 행렬로, $A$의 전체를 구성하는 rank-1 성분 중 i번째 방향의 기여를 나타낸다.

$A_i$는 단순히 두 벡터의 외적이기 때문에 rank-1 행렬이다. (하나의 방향성만 있음)

<details>
<summary><font color='red'>Example</font></summary>
<div markdown="1">

$$
\begin{bmatrix}1\\3\\5\end{bmatirx}\begin{bmatrix}2&4&6\end{bmatirx}
=\begin{bmatrix}\end{bmatirx}
$$

</div>
</details>

큰 특이값부터 $k$개만 남기면 데이터의 주요 구조를 유지하면서 저장량을 줄일 수 있다.

$$
\hat{A}(k)=\sum_{i=1}^k\sigma_i\mathbf{u}_i\mathbf{v}_i^\top
$$

$\hat{A}(k)$는 $A$의 SVD에서 가장 큰 k개의 특이값과 대응되는 rank-1 성분만 더한 것으로, 전체 정보를 k개의 중요한 방향만 남겨서 압축한 근사 행렬을 의미한다.

### Eckart-Young Theorem

$$
\hat{A}(k)=\underset{\text{rank}(B)=k}{\text{argmin}}~\lVert A-B\rVert_2
~~~~,~~~~
\lVert A-\hat{A}(k)\rVert_2=\sigma_{k+1}
$$

모든 rank-k 행렬 $B$ 중에서 $A$와의 차이를 최소로 만드는 행렬은 SVD 기반의 rank-k 행렬 $\hat{A}(k)$이며, 그 오차는 $k+1$번째 특이값과 같다.

## Spectral Norm

$$
\displaystyle\lVert A\rVert_2:=\underset{\mathbf{x}}{\max}\frac{\lVert A\mathbf x\rVert_2}{\lVert \mathbf x\rVert_2}=\underset{\mathbf{x}}{\max}\lVert A\mathbf x\rVert_2
$$

Matrix norm에는 여러 종류가 있으며, 그 중 spectral norm은 위와 같이 정의된다.

Spectral norm은 변환 전후의 크기 변화의 최대값으로 정의되며, 이는 $A$의 가장 큰 singular value 값과 동일하다.

> **Why?**
>
> $\lVert A\mathbf x\rVert_2=\mathbf x^\top A^\top A\mathbf x=\mathbf x^\top V(\Sigma^\top\Sigma)V^\top\mathbf x$
>
> Let, $ V^\top \mathbf x=\mathbf y$ → $\lVert A\mathbf x\rVert_2=\mathbf y^\top(\Sigma^\top\Sigma)\mathbf y=\sigma_1^2\mathbf y_1^2+\sigma_2^2\mathbf y_2^2+\cdots$
