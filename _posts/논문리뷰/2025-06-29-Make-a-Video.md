---
layout: single
title: "[논문리뷰] Make-A-Video: Text-to-Video Generation without Text-Video Data"
last_modified_at: 2025-06-29
categories: ["논문리뷰"]
tags: ["Video Generation"]
excerpt: ""
use_math: true
toc: true
toc_sticky: true
---

> paper: 

## Introduction

## Methods

### SpatioTemporal Layers

비디오를 생성하기 위해서는 spatial한 차원뿐만 아니라 temporal한 차원까지 고려해야하기 때문에 대부분의 U-Net 기반 디퓨전 네트워크들을 수정하였다.

- spatiotemporal decoder $D^t$는 64x64 크기의 RGB frame 16장을 생성
- frame interpolation network $f$는 디코더가 생성한 16장의 frame 사이를 보간해 frame rate를 증가시킴
- super-resolution networks $\text{SR}^t_l$

FC layer와 같은 layer들은 상관없기 때문에 추가적인 수정은 안했다고 한다.

#### Pseudo-3D Convolutional Layers

#### Pseudo-3D Attention Layers
