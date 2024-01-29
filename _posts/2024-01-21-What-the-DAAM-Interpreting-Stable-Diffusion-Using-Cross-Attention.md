---
layout: post
title:  "[PAPER] What the DAAM: Interpreting Stable Diffusion Using Cross Attention"
author: jaealways
categories: [ paper, NLP]
tags: [PAPER, ACL'22, multi-modal]
---


# [PAPER] What the DAAM: Interpreting Stable Diffusion Using Cross Attention

[reference: Raphael Tang et al, "What the DAAM: Interpreting Stable Diffusion Using Cross Attention", 2023](https://arxiv.org/pdf/2210.04885.pdf)

[![Video Label](http://img.youtube.com/vi/XiKyEKJrTLQ/0.jpg)](https://www.youtube.com/watch?v=XiKyEKJrTLQ)


# TLDR
- Stable diffusion 기반으로 각 단어가 이미지 생성에 미치는 영향 알아보기

# Abstract

- text-to-image 분야 모델의 해석가능성에 대한 분석이 부족함.
- Diffusion Attentic Attribution Maps, DAAM이라는 방법을 도입하여 오픈 소스 모델인 Stable Diffusion 모델에 대한 텍스트-이미지 속성 분석을 수행
- DAAM: cross-attention map을 결합해서 각 단어에 대한 2차원 속성 map 생성


# 1 Introduction

![Figure1](https://github.com/jaealways/TIL-daily/assets/71856506/843c4260-089d-4baa-9bf6-fc28767079e1)


- 이미지 segmentation 방법인 DAAM을 정량적으로 평가하고자 함. 
  - 기존 데이터셋과 방법을 활용하여 DAAM이 명사에 대한 semantic segmantation을 얼마나 잘 맞추는지 평가함.
- 이미지당 30개의 추론 단계를 가진 Stable Diffusion 2.0 기본 모델을 사용
  - COCO-Gen: 현실적인 프롬프트에 COCO 이미지 캡션 데이터셋 사용
  - Unreal-Gen: 무작위로 명사를 교체한 프롬프트 사용
  - 모델의 비정상적 프롬프트로 일반화 능력을 테스트하고자 함.
- DAAM의 성능을 Mask R-CNN, QueryInst, Mask2Former 등 supervised method 및 STEGO, PiCIE + H와 같은 Unsupervised Methods와 비교
- 사용된 평가 메트릭은 MIoU(mean intersection over union)
$$ \frac{|A∩B|}{|A∪B|} $$ 
  - the mean visual intersection area over the union
  - MIoU 클수록, 원래 박스와 겹치는 영역 많다는 뜻
  - MIoU80: 80개의 COCO 클래스로만 학습한 결과
  - MIoU∞: 클래스 제한 없이 모두 사용
- CLIPSeg는 mIoU80과 mIoU ∞ 모두에서 높은 성능
- DAAM은 비논리적인 텍스트에 대한 복원력을 입증하여 일반화 능력을 보여줌

- DAAM은 semantic segmentation을 명사 외에 형용사, 동사 등으로 확장
- DAAM 지도의 속성 품질을 5점 리커트 척도로 평가하는 인간 평가자 포함

- 2800개의 고유 단어-프롬프트 쌍이 생성되며, COCO에서 가장 일반적인 14개의 품사 태그를 포함
  - 각 쌍에 대해 이미지와 DAAM 맵이 생성. 크라우드 소싱을 통한 annotation

- 형용사, 동사, 명사, 고유명사의 DAAM 지도는 대부분 '좋음'에 가깝거나 그 이상의 평가를 받는 반면, 숫자와 부사의 지도는 'fair'에 가까움


# 2 Our approach

## 2.1 Preliminaries
- **Latent Diffusion Models**: 랜덤 노이즈로부터 high-fidelity 이미지를 생성하도록 훈련된 생성 모델, 크게 다음 세 개의 구성
  - **Deep Language Model**: 단어 임베딩을 생성, CLIP 같은 모델
  - **VAE(Variational Autoencoder)**: 영상의 latent 벡터를 인코딩하고 디코딩
  - **time-conditional U-Net**: latent 벡터에서 점진적으로 노이즈 제거

- **이미지 생성 프로세스**
  - latent 벡터를 랜덤 노이즈로 초기화하고 U-Net을 사용하여 텍스트 프롬프트에서 feed in
  - 반복적으로 노이즈 제거한 후 최종 벡터를 VAE로 이미지로 디코딩

- **트레이닝 및 최적화**: 네트워크는 캡션-이미지 쌍으로 훈련하고, 점수 일치를 위해 하한의 증거에 대해 가중치가 부여된 형태를 최적화 <br><br>
$$ \min_{\theta} \sum_{t=1}^{T} \sum_{i} p(\ell_{i} | \theta, t) \left[ \| c(\ell_{i}, t_i) \|_0 - \nabla_{\ell_t} \log p(\ell_t | \theta, t_i) \right]_2^2
$$<br><br>
- latent 벡터 만들기 위해 가우시안 노이즈로 $\hat{\ell}_{tT}$ 초기화하고 노이즈 제거 프로세스를 통해 반복되는 잠재 벡터를 생성 <br><br>
$$\hat{\ell}_{t-1} = \frac{1}{\sqrt{1-\hat{\alpha}_t}} \left( \hat{\ell}_t + \hat{\alpha}_t \hat{\epsilon}(\hat{\ell}_t, t_i) \right) + \sqrt{\hat{\alpha}_t} \hat{z}_t.
$$

## 2.2 Diffusion Attentive Attribution Maps
- 텍스트-이미지 합성을 위한 대규모 잠재 확산 모델에서 각 단어에 의해 이미지의 어떤 부분이 가장 큰 영향을 받을까?

- attribution approach를 통해 아웃풋의 1차미분을 인풋과 매핑하는 방법을 시도할 수 있음
  - 이 경우 T step동안 backpropagation을 해야하는데, 작은 혼란으로 큰 값의 오차 발생할 수 있음
- **Attention Mechanism**: NLP에서 자주 사용하는 Attention Mechanism 활용 
  - 이미지의 좌표 인식 잠재 표현과 텍스트 임베딩을 cross-contextualize함 
### Spatiotemporal aggregation
- 공간적 차원에 대해 정규화된 attention 점수를 집계하고 이미지 전체에 걸쳐 보간
- 인풋에서 k번째 단어를 다운샘플링 블록 등에 연결해서 [0,1]로 정규화

$$D^{(i)}_{k}[x, y] = \sum_{i,j,l} F^{(i)}_{j,k,l}[x, y] + \tilde{F}^{(i)}_{j,k,l}[x, y],$$

- 여러 다운샘플, 업샘플의 hitmap을 보간해서 이미지 원래 크기로 통일한 다음, head, layer, timestep을 더함
- 그 후에 임계값 넘는 픽셀만 표시하게 함

$$D^{+}_{k}[x, y] := \mathbb{1}\left( D^{R}_{k}[x, y] > \max_{i,j} D^{R}_{k}[i, j] \right),$$

![Figure2](https://github.com/jaealways/TIL-daily/assets/71856506/71c08a86-1a17-4dee-8658-fdb95e9f8bf2)



# 3 Attribution Analyses

## 3.1 Object Attribution
- DAAM 방법이 의미론적으로 이미지 분할 레이블과 일치하는지 명사를 중심으로 효과를 평가
### Setup
- **Experiment Setup**: Stable Diffusion 2.0 모델을 활용
  - COCO-Gen: COCO 이미지 캡션 데이터 세트의 real 프롬프트
  - Unreal-Gen: 무작위로 스왑된 명사가 있는 프롬프트로 이미지 생성 후 일반화 성능 측정
- **Ground Truth 비교**: 생성된 이미지를 Ground Truth 이미지에 존재하는 hand-segmented 명사와 비교
- **DAAM 분할 마스크**: 이진 DAAM 분할 마스크는 각 명사에 대해 다양한 임계값을 사용하여 계산
- semantic segmentation 모델과 감독되지 않은 기준선에 대해 평균 교차점(mIoU)을 메트릭으로 사용하여 평가
### Results
- DAAM은 unsupervised에서 뛰어난 성능을 보였으며, 비상식적인 텍스트에 대한 복원력과 구성 일반화의 효과를 보여줌

![Table1](https://github.com/jaealways/TIL-daily/assets/71856506/465ebb2e-4317-4f24-9076-795b47827a83)


## 3.2  Generalized Attribution
### Setup
- 명사를 넘어 형용사, 동사 등 모든 품사로 분석을 확장
- **human annotation**: 단어는 효과적인 분할 주석을 위해 분리할 수 없음
  - "people running" -> "running"만 별도로 annotate 불가능
  - 5점 리커트 척도를 사용하여 DAAM 맵의 속성 품질 인간이 평가
- **실험 절차**: COCO에서 14개의 공통 품사 태그를 포함하는 2800개의 고유 워드-프롬프트 쌍으로 단어-이미지 데이터 세트를 구성
  - 모든 쌍에 대한 DAAM 맵과 함께 이미지를 생성
### Results
- 조사 결과에 따르면 형용사, 동사, 명사 및 고유명사에 대한 DAAM 지도는 GOOD 이상, 숫자 및 부사는 Fair 
- 전반적으로 DAAM은 그럴듯한 mapping에 성공

![Figure3](https://github.com/jaealways/TIL-daily/assets/71856506/c9ec3904-f55d-4abc-98bc-144b06bc8ad0)
![Figure4](https://github.com/jaealways/TIL-daily/assets/71856506/103e52a6-f597-4f30-9f21-62a1632739ee)


# 4 Visuosyntactic Analysis

- DAAM을 사용하여 생성된 픽셀과 구문이 어떻게 관련되는지 파악하기

### Setup
- head-dependent DAAM 맵, COCO에서 1000개의 프롬프트를 분석하고 모든 단어에 대한 DAAM 맵을 생성
- mIoU, mIoD, mIoH과 같은 집합 기반 유사성 통계를 사용하여 상위 10개의 가장 일반적인 구문 관계에 초점

![image](https://github.com/jaealways/TIL-daily/assets/71856506/6bf8ddce-f02f-4d17-abfb-ea09dfde400f)
![image](https://github.com/jaealways/TIL-daily/assets/71856506/dcbf794d-2903-4f82-a707-6e03b7b73faf)

### Results
- **기준 비교**: 관련이 없는 단어 쌍과 head-dependent 모든 쌍에서 
- **구문 관계 분석**:
  - 명사 화합물(예: "아이스크림")에서는 지배력이 없다.
  - 구두점 및 기사(예: "punct" 및 "det")는 이미지 전반에 걸친 광범위한 주의로 인해 지배력을 나타내지 않는다.
  - 시각적 분리(예: "cat and dog")로 인해 "and"와 연결된 명사에서 중복이 적다.
- **지도의 지배**:
  - 핵심 논변(nsubj, obj)에서는 머리말이 우세하다.
  - 명목 종속어(nmod:of, amod, acl)에서, 집합 명사는 직관적인 우세를 보이는 반면, 형용사는 놀랍게도 그들이 수식하는 명사를 지배한다.
  - 핵심 서로 다른 단어 쌍이 가장 높은 중첩을 보여 동일한 참조에 대한 주의를 나타낸다.


# 5 Visuosemantic Analyses

## 5.1 Cohyponym Entanglement
- **목적**: 프롬프트에서 의미론적으로 유사한 단어(코히포니움)가 생성된 이미지의 품질에 미치는 영향을 조사
- **방법론**: WordNet을 활용하여 COCO의 시각적 객체 위에 의미론적 필드를 표현하는 온톨로지를 만듦
  - 두 개의 서로 다른 객체를 가진 프롬프트를 사용하여 이미지를 생성했으며 때로는 코히포니움으로, 때로는 그렇지 않았음
- **주석 프로세스**: 이미지는 존재하는 개체를 식별하는 주석자에 의해 평가되었음
  - 프롬프트에서 두 단어가 모두 존재하는 경우 이미지가 올바른 것으로 간주
- **Results**: 비협조어를 사용한 프롬프트는 코히포니즈(52%)에 비해 생성 정확도(61%)가 더 높은 것으로 나타남
  - 이는 코히포니즈가 DAAM 맵에서 중첩을 증가시켜 얽힌 주의력과 구성을 나타냄을 시사

## 5.2 Adjectival Entanglement
- **목적**: 명사의 수식 형용사가 전체 이미지에 어떤 영향을 미치는지 연구
- **방법론**: 다양한 형용사를 가진 프롬프트에서 생성된 이미지를 분석
  - 형용사가 변형되었을 때 특히 배경에서 이미지의 변화를 관찰하는 데 초점

![Figure8](https://github.com/jaealways/TIL-daily/assets/71856506/eda793bf-9521-453a-902e-12d2fe04ff38)

- **사례 연구**:
  - **a {rusty, metallic, wooden} shovel sitting in a clean shed**: 형용사(예: "녹슨", "금속", "나무")를 바꾸면 창가의 외관에 변화
  - **a {bumpy, smooth, spiky} ball rolling down a hill**: "bumpy", "smooth", "spicky"와 같은 형용사들이 땅의 모양에 영향
  - **a {blue, green, red} car driving down the streets**: 색 형용사("파란색", "녹색", "빨간색")가 배경의 색에 영향
- **관찰**: 실험을 통해 형용사가 수정하려는 대상뿐만 아니라 이미지 전체에 광범위하게 영향을 미침
- 이미지 생성 과정에서 형용사 얽힘의 한 형태

![Figure9](https://github.com/jaealways/TIL-daily/assets/71856506/8bfd44cb-7c1c-4735-828d-f73a9da4e293)


# 7 Conclusions

- 신경망이 언어적 정보를 처리하는 방법을 탐구함으로써 향후 연구에 대한 정보를 제공하는 것을 목표
- **확산모델에서의 시각언어적 현상에 관한 연구**: 확산모델에서의 단어-픽셀 교차주의 지도 해석에 초점을 맞춘 연구
  - 정량적 의미 세분화 작업과 정성적 속성 연구를 통해 DAAM 방법을 검증

- **구문 관계 및 특징 얽힘에 대한 연구 결과**: 구문 관계가 이미지 생성에서 시각적 상호 작용으로 어떻게 변환되는지에 대한 통찰력을 제공
  - 생성된 이미지에서 의미론적으로 관련된 단어(동음이)가 뒤섞이는 특징 얽힘 및 지나치게 광범위한 영향을 미치는 형용사와 같은 문제를 식별
- **미래 연구 및 언어 분석 기여**: 텍스트-이미지 합성 모델을 이해하고 개선하는 데 있어 향후 연구를 위한 토대를 마련
  - 확산 모델에서의 감독되지 않은 파싱 능력, 텍스트-이미지 생성과 자연어 처리의 교차와 같은 분야에서 추가적인 탐색의 필요성을 강조
