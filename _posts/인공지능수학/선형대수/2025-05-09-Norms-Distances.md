---
layout: single
title: "[선형대수] Norms & Distances"
last_modified_at: 2025-05-06
categories: ["인공지능 수학"]
tags: ["선형대수"]
excerpt: "벡터 노름"
use_math: true
toc: true
toc_sticky: true
---

## Norm

$$
\lVert\cdot\rVert:V→\Bbb R,~\mathbf{x}\mapsto \lVert \mathbf{x}\rVert
$$

벡터의 길이를 norm이라고 하며, 벡터를 스칼라로 mapping하는 일종의 함수로 볼 수 있다.

여러 종류의 norm이 있으며, 주로 사용하는 norm은 Manhattan Norm($l_1)$과 Euclidean Norm($l_2$)이다.

> ![Figure 1](/assets/images/인공지능수학/2-2. Figure1.png){: style="display:block; margin:0 auto; width: 90%; height: 90%;"}
>
> 좌측이 Manhattan Norm: $\lVert\mathbf{x}\rVert_1:=\sum_{i=1}^{n}{|x_i|}$
>
> 우측이 Euclidean Norm: $\lVert\mathbf{x}\rVert_2:=\sqrt{\sum_{i=1}^{n}{x_i^2}}$

Inner Product가 정의되어 있다면, norm은 다음과 같이 정의된다.

$$
\lVert\mathbf{x}\rVert=\sqrt{\mathbf{x}\mathbf{x}}
$$

## Distance

