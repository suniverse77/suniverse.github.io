---
layout: single
title: "[선형대수] Determinant & Trace"
last_modified_at: 2025-05-13
categories: ["인공지능 수학"]
tags: ["선형대수"]
excerpt: "행렬식과 trace"
use_math: true
toc: true
toc_sticky: true
---

## Determinant

![Figure 1](/assets/images/인공지능수학/3-1. Figure1.png){: style="display:block; margin:0 auto; width: 50%; height: 50%;"}

Square matrix에 대해 정의되며, 행렬이 나타내는 선형 변환의 부피 변화율 또는 가역성을 나타내는 스칼라 값이다.

$\text{det}(A)=0$이면 선형 변환에 의해 공간의 부피가 0이 된다는 뜻이므로 정보를 완전히 복원할 수 없기 때문에 역행렬이 존재하지 않는다.

$$
A=\begin{bmatrix}a&b\\c&d\end{bmatrix}~\to~\vert A\vert=ad-bc
$$

$2\times2$ 행렬에 대해 determinant는 위와 같이 정의된다.

### Properties of Determinant

1. $\text{det}(AB)=\text{det}(A)\text{det}(B)$
2. $\text{det}(A)=\text{det}(A^\top)$
3. $A$ is invertible $\implies\text{det}(A^{-1})=\frac{1}{\text{det}(A)}$
4. $\text{det}(A)=\text{det}(S^{-1}AS)$
5. $T$ is triangular matrix $\implies\text{det}(T)=\prod_i T_{ii}$
6. 

## Trace

### Properties of Trace

1. $\text{tr}(A+B)=\text{tr}(A)+\text{tr}(B)$
