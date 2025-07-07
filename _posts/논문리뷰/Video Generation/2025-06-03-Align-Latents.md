---
layout: single
title: "[논문리뷰] Align your Latents: High-Resolution Video Synthesis with Latent Diffusion Models"
last_modified_at: 2025-06-03
categories: ["논문리뷰"]
tags: ["Video Generation"]
excerpt: "CVPR 2023"
use_math: true
toc: true
toc_sticky: true
---

> [[Paper]](https://arxiv.org/abs/2304.08818)
> 
> **CVPR 2023**

## Introduction

**기존 연구들의 한계점**
- 비디오 데이터 학습에는 막대한 계산 자원이 필요하고, 공개된 대규모 비디오 데이터셋의 부족 때문에 비디오 생성 모델링의 발전이 더디다.
- 여러 연구들이 진행되었지만, 대부분 저해상도이거나 짧은 길이의 비디오만 생성 가능하다.

**제안하는 방법**
- Latent Diffusion Model (LDM)을 사용함으로써 고해상도 및 장시간 비디오 생성을 가능하게 한다.
- 사전 학습된 이미지 생성 LDM을 비디오 생성 LDM으로 확장하기 위해 시간 계층을 추가하였다.
- 공간 계층은 고정하고 시간 계층만 학습하므로 효율적이다.
- 기존의 이미지 super-resolution에 널리 사용되던 pixel-space upsampler와 latente DM upsampler를 시간적으로 정렬시켜 시간적으로 일관된 비디오 super-resolution 모델로 전환한다.

## Methods

## Experiments
