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


</div>
</details>

<center><img src='{{"/assets/images/논문리뷰/StableVideo-0.png" | relative_url}}' width="50%"></center>

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

<center><img src='{{"/assets/images/논문리뷰/StableVideo-1.png" | relative_url}}' width="40%"></center>
<br>
이후 각 clip에 3가지 synthetic captioning 기법을 사용하였다.

1. CoCa와 같은 image captioner를 사용해 clip의 중간 프레임에 대한 caption을 얻는다.
2. V-BLIP을 사용해 비디오 기반의 caption을 얻는다.
3. 생성된 두 caption을 LLM 기반으로 요약해서 clip에 대한 세 번째 설명을 얻는다.

위의 방법으로 생성된 비디오를 Large Video Dataset (LVD)라고 명명하였다.

하지만 추가적인 분석을 통해 이 데이터셋에서는 움직임이 거의 없는 clip, 텍스트가 과도하게 포함된 clip, 전반적으로 품질이 낮은 clip 등 비디오 모델의 성능을 저하시킬 수 있는 샘플들이 있다는 것을 확인하였다.

<center><img src='{{"/assets/images/논문리뷰/StableVideo-2.png" | relative_url}}' width="50%"></center>
<br>
위의 그림은 실제 비디오에 정적인 장면이 많다(optical flow score가 낮음)는 것을 보여준다.

따라서 아래와 같은 추가 필터링 과정을 거친다.

- Dense Optical Flow를 2FPS로 계산해서 평균 optical flow 크기가 임계값 이하인 정적인 clip은 제거한다.
- OCR을 사용해 텍스트가 과도하게 포함된 clip을 필터링한다.
- 각 클립의 첫 프레임, 중간 프레임, 마지막 프레임에 대해 CLIP 임베딩을 계산하고, 이를 기반으로 aesthetic score와 텍스트-이미지 유사도를 산출한다.

생성된 데이터셋의 전체 크기와 clip의 평균 길이 등의 통계는 아래와 같다.
<center><img src='{{"/assets/images/논문리뷰/StableVideo-3.png" | relative_url}}' width="50%"></center>
<br>

#### Stage I: Image Pretraining



#### Stage II: Curating a Video Pretraining Dataset



#### Stage III: High-Quality Finetuning



### 3. Training Video Models at Scale

#### Pretrained Base Model



#### High-Resolution Text-to-Video Model



#### High Resolution Image-to-Video Model



#### Frame Interpolation

#### Multi-View Generation

## Experiments

### Qualitative Results

**Arbitrary combinations of subjects and motions**

<center><img src='{{"/assets/images/논문리뷰/DreamVideo-5.png" | relative_url}}' width="80%"></center>
<br>
Subject와 motion이 주어졌을 때의 비디오 생성 결과이다.

**Subject customization**

<center><img src='{{"/assets/images/논문리뷰/DreamVideo-6.png" | relative_url}}' width="100%"></center>
<br>
Subject의 외형을 보존하면서도 주어진 프롬프트에 적합한 비디오를 생성할 수 있다.

**Motion customization**

<center><img src='{{"/assets/images/논문리뷰/DreamVideo-7.png" | relative_url}}' width="100%"></center>
<br>
Target motion을 잘 포착하면서도 주어진 프롬프트에 적합한 비디오를 생성할 수 있다.

**Ablation studies**

<center><img src='{{"/assets/images/논문리뷰/DreamVideo-8.png" | relative_url}}' width="90%"></center>

### Quantitative Results

**Arbitrary combinations of subjects and motions**

<center><img src='{{"/assets/images/논문리뷰/DreamVideo-9.png" | relative_url}}' width="60%"></center>

**Subject customization**

<center><img src='{{"/assets/images/논문리뷰/DreamVideo-10.png" | relative_url}}' width="60%"></center>

**Motion customization**

<center><img src='{{"/assets/images/논문리뷰/DreamVideo-11.png" | relative_url}}' width="60%"></center>

**Ablation studies**

<center><img src='{{"/assets/images/논문리뷰/DreamVideo-12.png" | relative_url}}' width="60%"></center>
