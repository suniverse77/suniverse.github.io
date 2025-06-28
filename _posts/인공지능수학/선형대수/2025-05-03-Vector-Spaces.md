---
layout: single
title: "[선형대수] Vector Spaces"
last_modified_at: 2025-05-03
categories: ["인공지능 수학"]
tags: ["선형대수"]
excerpt: "벡터 공간 & span & 영공간"
use_math: true
toc: true
toc_sticky: true
---

## Vector Space

공집합이 아닌 집합 $V=(\mathcal V,+,\cdot)$에 대해 아래의 조건을 만족하는 집합 $V$를 vector space라고 하며, $V$에 속하는 원소를 벡라고 정의한다.

1. $V=(\mathcal V,+)$은 Abelian group (group에 대한 설명은 맨 아래에)
2. Distributivity가 성립

	a. 벡터 분배법칙: $\forall \ \lambda,\psi\in \Bbb R,~\forall \ \mathbf{x},\mathbf{y}\in \mathcal{V}\implies\lambda \cdot(\mathbf{x}+\mathbf{y})=\lambda \cdot\mathbf{x}+\lambda\cdot \mathbf{y}$

    b. 스칼라 분배법칙: $\forall \ \lambda,\psi\in \Bbb R,~\forall \ \mathbf{x},\mathbf{y}\in \mathcal{V}\implies(\lambda+\psi)\cdot\mathbf{x}=\lambda\cdot \mathbf{x}+\psi\cdot \mathbf{y}$
    
3. Associativity가 성립

	$\forall\lambda,\psi\in\mathbb{R},~\mathbf{x}\in\mathcal{V}\implies\lambda\cdot(\psi\cdot\mathbf{x})=(\lambda\psi)\cdot\mathbf{x}$
   

즉, vector space란 여러 개의 벡터들이 모여 형성하는 공간으로, 같은 공간 내의 벡터들끼리의 선형 연산이 가능해야한다.

## Vector Subspace

벡터공간 $V$의 부분집합 $U$가 아래의 조건을 만족하면, $U$를 $V$의 subspace라고 한다.

1. $V$에 존재하는 $\mathbf{0}$ (zero vector)를 포함해야 한다.
2. $U$는 덧셈과 스칼라배에 대해 닫혀있어야 한다.

좌표 공간에서의 subspace는 원점을 포함해야 한다.

- $\mathbb{R}^2$ 공간에서 vector subspace는 원점을 포함하는 직선
- $\mathbb{R}^3$ 공간에서 vector subspace는 원점을 포함하는 직선 또는 평면

> ex) $\mathbb{R}^2$ vector space
>
> ![Figure 1](/assets/images/인공지능수학/1-3. Figure1.png){: style="display:block; margin:0 auto; width: 100%; height: 100%;"}
>
> 첫 번째 그림은 스칼라배에 대해 닫혀있지 않으므로, $\mathbb{R}^2$의 subspace가 아니다.
>
> 두 번째 그림은 원점을 포함하지 않으므로, $\mathbb{R}^2$의 subspace가 아니다.
>
> 세 번째 그림은 $\mathbb{R}^2$의 subspace가 아니다.
>
> 네 번째 그림은 subspace의 조건을 다 만족하므로, $\mathbb{R}^2$의 subspace이다.

> ![Figure 2](/assets/images/인공지능수학/1-3. Figure2.png){: style="display:block; margin:0 auto; width: 50%; height: 50%;"}
> 해당 직선에 있는 벡터들끼리 덧셈을 하거나 벡터에 스칼라배를 할 경우, 직선 밖으로 나가기 때문에 subspace가 아니다.
> ![Figure 3](/assets/images/인공지능수학/1-3. Figure3.png){: style="display:block; margin:0 auto; width: 50%; height: 50%;"}
> 해당 직선에 있는 벡터들끼리 덧셈을 하거나 벡터에 스칼라배를 해도 직선 내에 존재하기 때문에 subspace이다.

## Span

벡터 집합 $S$에 있는 벡터들의 가능한 모든 linear combination으로 만들어지는 집합을 $\text{span}(S)$라고 한다.

> ex1)
> $$
> \text{span}(\begin{bmatrix}1\\0\end{bmatrix},\begin{bmatrix}0\\1\end{bmatrix})=\mathbb{R}^2
> $$
>
> ex2)
> $$
> \text{span}(\begin{bmatrix}2\\1\end{bmatrix},\begin{bmatrix}1\\3\end{bmatrix})=\begin{bmatrix}2a+b\\a+3b\end{bmatrix}
> $$

## Null Space

Homogeneous Equation의 solution을 모두 모아놓은 집합을 Null Space라고 부른다.
    
![Figure 4](/assets/images/인공지능수학/1-3. Figure4.png){: style="display:block; margin:0 auto; width: 20%; height: 20%;"}
    
- 어떠한 null space든지 항상 $\mathbf{0}$를 포함하므로, vector space이다.
- Null space의 차원을 **nullity**라고 하며, free variable의 개수와 동일하다.

## Group

임의의 집합 $S$에 operator 1개가 포함되어 있고, 아래의 조건을 만족할 때 해당 집합을 group이라고 부른다.

1. Closure (연산 후에도 그 집합에 속해있어야 한다.)

	$x\otimes y\in S$

2. Associativity

	$(x\otimes y)\otimes z=x\otimes(y\otimes z)$

3. Neural element (항등원이 존재해야한다.)

	$x\otimes e=x,~e\otimes x=x$

4. Inverse element (역원이 존재해야한다.)

	$x\otimes y=e,~y\otimes x=e$

이때, communicativity (교환법칙)까지 적용된다면, 해당 group을 **Abelian Group**이라고 부른다.
