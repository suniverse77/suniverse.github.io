---
layout: single
title: "[최적화] Linear Programming"
last_modified_at: 2024-12-01
categories: ["인공지능 수학"]
tags: ["최적화"]
excerpt: "선형 계획법"
use_math: true
toc: true
toc_sticky: true
---

## Linear Programming

목적함수와 제약조건이 모두 선형식으로 표현된 최적화 문제를 의미한다.

$$
\underset{\mathbf x\in\mathbb{R}^d}\min~\mathbf c^\top\mathbf x
\\\text{subject to}~~A\mathbf x\leq\mathbf b
$$

## Qudratic Programming

목적함수가 2차식이고 제약조건이 선형식으로 표현된 최적화 문제를 의미한다.

$$
\underset{\mathbf x\in\mathbb{R}^d}\min~\bigg(
\frac{1}{2}\mathbf x^\top Q\mathbf x+\mathbf c^\top\mathbf x\bigg)
\\
\text{subject to}~~A\mathbf x\leq\mathbf b
$$
