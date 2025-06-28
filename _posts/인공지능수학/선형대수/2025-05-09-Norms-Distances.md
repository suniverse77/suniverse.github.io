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
\lVert\cdot\rVert:V→\Bbb R
$$

벡터의 길이를 norm이라고 하며, 벡터를 스칼라로 mapping하는 일종의 함수로 볼 수 있다.

여러 종류의 norm이 있으며, 주로 사용하는 norm은 Manhattan Norm($l_1)$과 Euclidean Norm($l_2$)이다.

> ![Figure 1](/assets/images/인공지능수학/2-2. Figure1.png){: style="display:block; margin:0 auto; width: 90%; height: 90%;"}
>
> 좌측이 Manhattan Norm $\lVert\mathbf{x}\rVert_1:=\sum_{i=1}^{n}{x_i}$
>
> 우측이 Euclidean Norm $\lVert\mathbf{x}\rVert_2:=\sqrt{\sum_{i=1}^{n}{x_i^2}}$

Inner product space에서 norm은 다음과 같이 정의된다.

$$
\lVert\mathbf{x}\rVert=\sqrt{\langle\mathbf{x},\mathbf{x}\rangle}
$$

### Norm의 조건

1. Absolutely homogeneous

   $\lVert\lambda\mathbf{x}\rVert=\lambda\lVert\mathbf{x}\rVert$

2. Triangle inequality

   $\lVert\mathbf{x}+\mathbf{y}\rVert\leq\lVert\mathbf{x}\rVert+\lVert\mathbf{y}\rVert$

3. Positive definite

   $\lVert\mathbf{x}\rVert\geq0$

   $\lVert\mathbf{x}\rVert=0\iff\mathbf{x}=\mathbf{0}$

## Distance

$$
d(\mathbf{x},\mathbf{y}):=\lVert\mathbf{x}-\mathbf{y}\rVert=\sqrt{\langle\mathbf{x}-\mathbf{y},\mathbf{x}-\mathbf{y}\rangle}
$$

Inner product space에서 두 벡터 사이의 distance는 위와 같이 정의된다.

Inner product로 dot product를 사용할 때 정의된 distance $d(\mathbf{x},\mathbf{y}):=\sqrt{(\mathbf{x}-\mathbf{y})^\top(\mathbf{x}-\mathbf{y})}$를 Euclidian distance라고 부른다.

### Distance의 조건

1. Symmetric

   $d(\mathbf{x},\mathbf{y})=d(\mathbf{y},\mathbf{x})$

2. Triangle inequality

   $d(\mathbf{x},\mathbf{z})\leq d(\mathbf{x},\mathbf{y})+d(\mathbf{y},\mathbf{z})$

3. Positive definite

   $d(\mathbf{x},\mathbf{y})\geq0$

   $d(\mathbf{x},\mathbf{y})=0\iff \mathbf{x}=\mathbf{y}$
