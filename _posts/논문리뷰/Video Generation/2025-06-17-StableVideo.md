---
layout: single
title: "[논문리뷰] Stable Video Diffusion: Scaling Latent Video Diffusion Models to Large Datasets"
last_modified_at: 2025-06-17
categories: ["논문리뷰"]
tags: ["Video Generation"]
excerpt: "CoRR 2023"
use_math: true
toc: true
toc_sticky: true
---

**CoRR 2023** 
[[Paper]](https://arxiv.org/abs/2311.15127)
[[GitHub]](https://github.com/Stability-AI/generative-models)

<details>
<summary><font color='#FF8C00'>📝 Summary</font></summary>
<div markdown="1">
<br>
비디오 모델링을 개선하기 위해 대규모의 잡음이 섞인 비디오 데이터셋을을 생성 비디오 모델에 적합한 데이터셋으로 변환하는 방법을 제안하였다.

Curation된 대규모 비디오 데이터셋에 대해 사전학습된 비디오 모델은 후에 다양한 task에 대해 파인튜닝이 가능하다.

</div>
</details>
<br>
<center><img src='{{"/assets/images/논문리뷰/StableVideo-0.png" | relative_url}}' width="100%"></center>

## Introduction

**기존 연구들의 한계점**

- 비디오 모델링 개선에 관한 연구들은 주로 공간 및 시간 레이어의 정확한 배열에 집중해왔지만, 데이터 선택의 영향에 대한 탐구는 거의 없었다.
- 많은 기존 비디오 모델링 연구들이 이미지 도메인의 기법을 차용해왔음에도 불구하고, 비디오에 대한 이러한 사전 훈련 및 고품질 파인튜닝 전략은 아직 연구되지 않았다.

**제안하는 방법**

- 단순한 latent video diffusion baseline의 아키텍처 및 훈련 방식을 고정시켜 놓은 채, data curation의 영향을 분석한다.
- 우수한 성능을 위해 아래의 세 가지의 중요한 비디오 학습 단계를 제시한다.
    1. 텍스트-이미지 사전학습
    2. 저해상도 대규모 비디오 사전학습
    3. 고품질 소규모 데이터셋을 활용한 고해상도 비디오 파인튜닝
- 원본 비디오 데이터셋을 체계적으로 필터링하고 정제하는 data curation workflow를 제시한다.
- 학습된 모델은 T2V, I2V, NVS, Motion control 등 다양한 downstream task에 활용될 수 있다.

## Methods

### 1. Data Curation

공개적으로 접근 가능한 비디오 데이터셋 중에서는 WebVid-10M이 널리 사용되고 있다.

Joint image-video 학습 전략에서는 WebVid-10M은 종종 이미지 데이터와 결합되어 사용되지만, 이로 인해 이미지 데이터와 비디오 데이터가 최종 모델에 미치는 영향을 구분하기가 어려워진다.

### 2. Curating Data for HQ Video Synthesis

대규모 비디오 데이터셋에서 SOTA video diffusion 모델을 학습시키기 위해 데이터 가공 및 curation 기법과 3단계의 학습 전략을 도입한다.

3단계 학습 전략은 아래와 같다.
- Stage I: image pretraining
- Stage II: video pretraining (대규모 비디오 데이터셋에 대해 학습)
- Stage III: video finetuning (고품질의 소규모 비디오 데이터셋에 대해 파인튜닝)

#### Data Processing and Annotation

하나의 비디오 내에서 장면 전환(cut)과 fade가 있는 것을 방지하기 위해 [cut-detection pipeline](https://github.com/Breakthrough/PySceneDetect)을 사용해서 장면 전환이 일어나는 부분을 잘랐다.

파이프라인 적용 결과, 아래와 같이 비디오별 clip(하나의 비디오를 분할한 세그먼트) 개수가 약 4배 증가했으며, 이는 원본 데이터에는 장면 전환 정보가 부분적으로 있다는 것을 보여준다.

<center><img src='{{"/assets/images/논문리뷰/StableVideo-1.png" | relative_url}}' width="20%"></center>
<br>
이후 각 clip에 3가지 synthetic captioning 기법을 사용하였다.

1. CoCa와 같은 image captioner를 사용해 clip의 중간 프레임에 대한 caption을 얻는다.
2. V-BLIP을 사용해 비디오 기반의 caption을 얻는다.
3. 생성된 두 caption을 LLM 기반으로 요약해서 clip에 대한 세 번째 설명을 얻는다.

위의 방법으로 생성된 비디오를 Large Video Dataset (LVD)라고 명명하였으며, 580M개의 annotated video clip pair로 구성되어 있다.

하지만 추가적인 분석을 통해 이 데이터셋에서는 움직임이 거의 없는 clip, 텍스트가 과도하게 포함된 clip, 전반적으로 품질이 낮은 clip 등 비디오 모델의 성능을 저하시킬 수 있는 샘플들이 있다는 것을 확인하였다.

<center><img src='{{"/assets/images/논문리뷰/StableVideo-2.png" | relative_url}}' width="50%"></center>
<br>
위의 그림은 실제 비디오에 정적인 장면이 많다(optical flow score가 낮음)는 것을 보여준다.

따라서 아래와 같은 추가 필터링 과정을 거친다.

- Dense Optical Flow를 2FPS로 계산해서 평균 optical flow 크기가 임계값 이하인 정적인 clip은 제거한다.
- OCR을 사용해 텍스트가 과도하게 포함된 clip을 필터링한다.
- 각 클립의 첫 프레임, 중간 프레임, 마지막 프레임에 대해 CLIP 임베딩을 계산하고, 이를 기반으로 aesthetic score와 텍스트-이미지 유사도를 산출한다.

생성된 데이터셋의 전체 크기와 clip의 평균 길이 등의 통계는 아래와 같다.
<center><img src='{{"/assets/images/논문리뷰/StableVideo-3.png" | relative_url}}' width="70%"></center>

#### Stage I: Image Pretraining

이미지로 사전학습된 모델의 강력한 visual representation을 활용하기 위해, 학습 파이프라인의 첫 번째 단계로 image pretraining을 고려하였다.

본 연구에서는 사전학습된 Stable Diffusion 2.1 모델을 초기 모델로 사용하였다.

아래 그림은 이미지로 사전학습된 모델이 더 우수하다는 것을 보여준다.

<center><img src='{{"/assets/images/논문리뷰/StableVideo-4.png" | relative_url}}' width="50%"></center>

#### Stage II: Curating a Video Pretraining Dataset

비디오 도메인에는 이미지처럼 원치 않는 샘플을 자동으로 걸러낼 수 있는 off-the-shelf representation이 없기 때문에 아래의 방법을 사용해 LVD의 subset을 만든 후 사람의 선호도를 기반으로 평가하였다.

- CLIP scores
- aesthetic scores
- OCR detection rates
- synthetic captions
- optical flow scores

<center><img src='{{"/assets/images/논문리뷰/StableVideo-5.png" | relative_url}}' width="50%"></center>
<br>
위의 그림에서 LDV-10M-F는 LVD-10M을 앞서 언급한 방법들을 사용해 필터링한 데이터셋으로, LVD-10M보다 데이터 양이 4배 더 적지만 user preference가 더 높았다.

<center><img src='{{"/assets/images/논문리뷰/StableVideo-6.png" | relative_url}}' width="90%"></center>

#### Stage III: High-Quality Finetuning

앞 절에서는 체계적인 data curaction이 성능 향상에 기여한다는 점을 입증하였다.

그러나 최종 목표는 파인튜닝 이후의 성능을 향상시키는 것이기 때문에 Stage II에서의 차이가 Stage III의 최종 성능에 어떤 영향을 미치는지 분석한다.

Video pretraining의 효과를 분석하기 위해, 동일한 구조를 갖지만 초기 가중치가 다른 3개의 모델을 250k개의 고품질 비디오 데이터셋을 이용해 파인튜닝하였다.

- 모델1: 이미지에 대해서만 사전학습된 모델
- 모델2: 50M개의 curation 비디오 데이터로 사전학습된 모델
- 모델3: 50M개의 non-curation 비디오 데이터로 사전학습된 모델

<center><img src='{{"/assets/images/논문리뷰/StableVideo-7.png" | relative_url}}' width="60%"></center>
<br>
- 모델1: 시간적 개념에 대해 학습하지 않았기 때문에 파인튜닝만으로 비디오의 특성을 학습해야 하므로 성능이 가장 낮다.
- 모델2: 파인튜닝하기 전에 비디오의 특성에 대해 학습했기 때문에 파인튜닝을 통해 더 효율적으로 고해상도 비디오 생성 능력을 얻을 수 있다.
- 모델3: curation된 데이터셋을 사용해 사전학습된 모델2보다 성능이 낮다.

Video pretraining과 video finetuning을 분리하여 진행하는 것이 성능 향상에 도움이 되며, video pretraining은 대규모의 curation 데이터셋으로 수행하는 것이 이상적이다.

## Experiments

### Qualitative Results

**High-Resolution Text-to-Video Model**

<center><img src='{{"/assets/images/논문리뷰/StableVideo-8.png" | relative_url}}' width="100%"></center>
<br>
위 그림은 576x1024 해상도를 가진 비디오 데이터셋에 대해 파인튜닝을 진행한 결과이다.

**High Resolution Image-to-Video Model**

<center><img src='{{"/assets/images/논문리뷰/StableVideo-9.png" | relative_url}}' width="100%"></center>
<br>
위 그림은 이미지를 conditioning으로 하여 비디오를 생성한 결과이다.

이를 위해 base 모델에 사용되던 텍스트 임베딩을 CLIP 이미지 임베딩으로 교체하였다.
