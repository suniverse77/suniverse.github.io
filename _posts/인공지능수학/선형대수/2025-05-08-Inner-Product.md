---
layout: single
title: "[선형대수] Inner Product"
last_modified_at: 2025-05-06
categories: ["인공지능 수학"]
tags: ["선형대수"]
excerpt: "벡터의 내적과 외적"
use_math: true
toc: true
toc_sticky: true
---

## Inner Product

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

Inner Product는 다양한 형태로 정의된다.

- $\langle\mathbf{x},\mathbf y\rangle:=\mathbf x^\top \mathbf y$ → 이런 형태로 정의되는 내적을 **Dot Product**라고 부른다.
- $\langle\mathbf x,\mathbf y\rangle:=x_1y_1-(x_1y_2+x_2y_1)+2x_2y_2$
- $\langle f,g\rangle:=\int_a^b f(x)g(x)\,dx$ → 함수의 내적

## Outer Product
