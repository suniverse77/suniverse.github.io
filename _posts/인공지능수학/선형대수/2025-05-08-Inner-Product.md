---
layout: single
title: "[선형대수] Inner Product"
last_modified_at: 2025-05-08
categories: ["인공지능 수학"]
tags: ["선형대수"]
excerpt: "벡터의 내적과 외적"
use_math: true
toc: true
toc_sticky: true
---

## Inner Product

$$
\langle\cdot,\cdot\rangle:V\times V\to\mathbb{R}
$$

벡터 공간 내의 임의의 두 벡터를 스칼라로 매핑시키는 함수를 inner product라고 한다.

Inner product가 정의된 vector space $(V,\langle\cdot,\cdot\rangle)$를 **inner product space**라고 한다.

### Inner Product의 조건

1. 분배 법칙이 성립

   $\langle\mathbf u+\mathbf w,\mathbf v\rangle=\langle\mathbf u,\mathbf v\rangle+\langle\mathbf w,\mathbf v\rangle$

   $\langle\lambda\mathbf v,\mathbf w\rangle=\lambda\langle\mathbf v,\mathbf w\rangle$
   
3. 교환 법칙이 성립

   $\langle\mathbf v,\mathbf w\rangle=\langle\mathbf w,\mathbf v\rangle$
   
4. 자기 자신과의 내적은 항상 0 이상

   $\langle\mathbf v,\mathbf v\rangle\geq0$

   $\langle\mathbf v,\mathbf v\rangle=0\iff\mathbf v=\mathbf0$

### Inner Product의 종류

Inner product는 다양한 형태로 정의된다.

- $\langle\mathbf{x},\mathbf y\rangle:=\mathbf x^\top \mathbf y$ → 이런 형태로 정의되는 내적을 **Dot Product**라고 부른다.
- $\langle\mathbf x,\mathbf y\rangle:=x_1y_1-(x_1y_2+x_2y_1)+2x_2y_2$
- $\langle f,g\rangle:=\int_a^b f(x)g(x)\,dx$ → 함수의 내적

### Symmetric, Positive Definite

대칭 행렬 $A$에 대해 $\mathbf{x}^\top A\mathbf{x}$가 $\mathbf{0}$이 아닌 모든 $\mathbf{x}$에 대해 양수일 때, $A$를 symmetric, positive definite matrix라고 부른다.

$$
\langle\mathbf{x},\mathbf y\rangle:=\mathbf{x}^\top A\mathbf{y}
$$

행렬 $A$가 symmetric, positive definite matrix라면, 내적을 위와 같이 정의할 수 있다.

<details>
<summary><font color='red'>Example</font></summary>
<div markdown="1">
  
<center><img src='{{"/assets/images/인공지능수학/2-1. Figure1.png" | relative_url}}' width="25%"></center>

---

<center><img src='{{"/assets/images/인공지능수학/2-1. Figure2.png" | relative_url}}' width="100%"></center>

$\mathbf{x}\not=\mathbf{0}$일 때 항상 양수이므로, $A$는 symmetric, positive definite matrix이다.

</div>
</details>

## Outer Product

$$
\mathbf{x}\otimes\mathbf{y}:=\mathbf{x}\mathbf{y}^\top
$$

두 벡터의 곱으로 행렬을 생성하는 연산을 outper product라고 한다.

## Cross Product

$$
\mathbf{x}\times\mathbf{y}:=\begin{vmatrix}\mathbf{i}&\mathbf{j}&\mathbf{k}\\x_1&x_2&x_3\\y_1&y_2&y_3\end{vmatrix}=(x_2y_3-x_3y_2)\mathbf{i}-(x_1y_3-x_3y_1)\mathbf{j}+(x_1y_2-x_2y_1)\mathbf{k}
$$

두 3차원 벡터에 수직인 벡터를 생성하는 연산을 cross product라고 한다.
