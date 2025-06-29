![image](https://github.com/user-attachments/assets/8a147950-61b1-4f7f-b2d6-d7a520cfad46)---
layout: single
title: "[최적화] Linear Programming"
last_modified_at: 2025-05-30
categories: ["인공지능 수학"]
tags: ["최적화"]
excerpt: "라그랑주"
use_math: true
toc: true
toc_sticky: true
---

## Max-min inequality

$$
\underset{\lambda}{\max}~\underset{x}{\min} ~f(x,\lambda)\leq\underset{x}{\min}~\underset{\lambda}{\max}~f(x,\lambda)
$$

임의의 함수 $f(x,\lambda)$에 대해 최대화하고 최소화하는 것이 최소화하고 최대화하는 것보다 항상 크다.

> **유도**
>
> 1. $g(x,\lambda):=\underset{x}{\min} ~f(x,\lambda)$
> 2. $g(x,\lambda)\leq f(x,\lambda)$
> 3. $\underset{\lambda}{\max} ~g(x,\lambda)\leq\underset{\lambda}{\max} ~f(x,\lambda)$
> 4. $\underset{\lambda}{\max} ~g(x,\lambda)\leq\underset{x}{\min} ~\underset{\lambda}{\max} ~f(x,\lambda)$

## Lagrangian

![Figure 1](/assets/images/인공지능수학/6-2. Figure1.png){: style="display:block; margin:0 auto; width: 50%; height: 50%;"}

Primal problem이 위와 같이 주어졌을 때, 아래와 같이 원래의 목적 함수와 제약 조건을 결합한 함수를 Lagrangian이라고 한다.

![Figure 2](/assets/images/인공지능수학/6-2. Figure2.png){: style="display:block; margin:0 auto; width: 50%; height: 50%;"}

제약 조건이 있는 최적화 문제를 다루기 쉽게 만들고, Dual 문제를 정의하기 위해 사용한다.

이때 $\lambda$와 $\nu$를 lagrange multiplier라고 부르며, 부등식 조건에 대한 승수에는 항상 $\lambda_i\geq0$의 조건이 붙음

### Dual problem

원래의 최적화 문제 (primal problem)를 직접 푸는 대신, 다른 문제 (dual problem)를 풀어서 해를 유도하거나 경계를 줄 수 있음

Primal은 원래의 최적화 문제, Dual은 라그랑지안을 통해 정의한 새로운 최적화 문제임

## Duality gap

## KKT conditions
