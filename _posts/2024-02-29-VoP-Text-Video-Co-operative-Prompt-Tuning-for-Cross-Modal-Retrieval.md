---
layout: post
title:  "[PAPER] VoP: Text-Video Co-operative Prompt Tuning for Cross-Modal Retrieval"
author: jaealways
categories: [ paper, NLP]
tags: [PAPER, CVPR'23, multi-modal, prompt]
---

# [PAPER] VoP: Text-Video Co-operative Prompt Tuning for Cross-Modal Retrieval

[reference: Adithya Bhaskar, "VoP: Text-Video Co-operative Prompt Tuning for Cross-Modal Retrieval", 2023](https://arxiv.org/pdf/2211.12764.pdf)


# Abstract
- 최근 많은 연구들이 multi-modal을 위해 CLIP에 무거운 모듈을 더해 backbone으로 사용
- 효율적인 튜닝을 위해 text-video에서 0.1%의 파라미터만을 baseline으로 사용한 프레임워크
- 또 비디오의 특성을 고려해 새로운 비디오 프롬프트 메커니즘 개발
- VoP enhancement: frame position, frame context, layer function을 각각 학습가능한 프롬프트로 모델링


# 1 Introduction
- 최근 video-text retrieval 학습은 image-text knowledge를 video로 transfer 시키려 했음
- 하지만 fine-tuning은 과적합과 모델 용량 문제로 사용하기 어려움

- 본 논문에선 transferability와 generalizability 해결을 위해 몇 가지 프롬프트 튜닝을 제시
- uni-modal에 비해 multi-modal은 더 많은 파라미터가 필요하기 때문에, 파라미터 효율화를 위해 dual-branch를 사용하는게 좋음
- VoP: Text-Video Co-operative Prompt Tuning, textual과 visual encoder 양쪽 모두에 tunable한 프롬프트 제시
- Vop는 0.1%의 파라미터만으로 우수한 성능 보임

![Figure1](https://github.com/jaealways/must-read-paper-CV-daily/assets/71856506/d2c4d838-2d43-404f-b0a0-c0f298e583cb)

- video에 필요한 정보를 잘 뽑기 위해서, 프롬프트를 position-specific, context-specific, function-specific 세 종류로 디자인함
    - position-specific: 같은 상대적 위치의 프레임과 정보를 공유함
    - context-specific: 프레임 간의 시퀀스 모델링에 필요한 문맥 메세지를 주입
    - function-specific: 프레임 내의 혹은 프레임 간의 변환을 도와주는 프롬프트
- MSR-VTT, DiDeMo, ActivityNet, LSMDC 등의 tuning strategy와 비교했을 때, 더 적은 파라미터로도 우수한 성능 보임

- 결국, 논문의 main-contribution은 다음과 같음
    - VoP를 통해 파라미터를 훨씬 감소시켜도 retrieval 성능 유지
    - video-specific 정보를 반영해주기 위해, 세 가지 형태의 프롬프트 개발
    - 다섯 개의 text-video retrieval 벤치마크를 통해 대규모 실험 진행

# 2 Related Work

### Contrastive Vision-Language Pre-Training
- CLIP, ALIGN, ALBEF

### Text-Video Retrieval
- 서로 다른 text-video 간의 유사한 샘플 매칭하는 것이 목표
- ClipBERT, CLIP4Clip, X-Pool
- 기존 모델들과 다르게 효율성과 효과성 사이의 균형을 이루고자 함

### Prompt Learning
- CoOp: few-shot image와 프롬프트 같이 훈련시킴
- visual prompt tuning이 효과적이지만 multi-modal에서 많이 시도 안됨

# 3 Methodology
- 아래 세 가지 시도를 함
    - text-video retrieval에서 pre-trained CLIP 사용하면 어떨까?
    - VoP를 통해 cross-modal alignemnt 시도
    - 비디오 특성을 고려한 프롬프트 적용

## 3.1. Preliminary
### Problem Formulation
- text-video retrieval의 핵심은 text set과 video set 사이를 유사도 점수를 높이는 것
- 본 논문에선 text t를 N개의 토큰화된 단어와 비디오 v를 F개의 샘플 이미지 프레임으로 정의

### Revisiting CLIP-based Solution
- CLIP은 textual과 visual 사이에 joint latent space를 공유함
- text를 토큰화해서 word 임베딩을 통해 K-layer 트랜스포머에 넣음
    - [EOS] 토큰 사용?
- visual encoder의 경우, 인풋 이미지 I를 M개의 패치로 나누고 패치토큰 시퀀스로 변환 후, K-layer 트랜스포머에 넣음
    - [CLS] 토큰과 임베딩 concat
- textual encoder와 visual encoder에서 나온 값들의 코사인 유사도 계산

- 위의 CLIP-based 방법을 비디오에 적용할 때, 샘플 프레임의 평균 임베딩 값을 평균내서 text와 비교


## 3.2. Text-Video Co-operative Prompt Tuning (VoP)
- backbone을 고정하고 fine-tuning 동안 최적화를 진행하기 위해 프롬프트 튜닝 진행
- 비디오 도메인에서 continuous prompt를 textual embedding sequence로 넣곤 했는데, 두 가지 limit에 직면함
    - text branch 기준으로만 프롬프트 학습하면 visual encoder의 정보는 소실됨
    - 인풋 레이어에 대한 prompt만 사용할 경우, 아웃풋 임베딩에 상대적으로 영향 덜 미침
- 때문에 VoP에선 각 레이어에 visual, text 인코더 모두 넣음

![Figure2](https://github.com/jaealways/must-read-paper-CV-daily/assets/71856506/0308a013-aca8-4014-8fdc-9ac374be5a35)

- text는 프롬프트와 이전 단계의 EOS 토큰 값을 다음 트랜스포머에 같이 넣어줌
- visual은 CLS 토큰과 visual prompt, 임베딩 값 같이 넣음
- text-to-video, video-to-text cross-entropy losses를 최소화하도록 학습


## 3.3. Equipping with Video Prompts
- 프레임을 video-text retrieval에서 독립된 이미지로 처리하기 때문에 정보를 충분히 반영하지 못함
- VoP with Position-Specific Video Prompts (VoPP): 상대위치가 같은 프레임들간 visual prompt 공유
- VoP with Context-Specific Video Prompts (VoPC): 현재 프레임에서 중요한 정보 강조하려면 나머지 비디오도 봐야 함
    - CLS 토큰 시퀀스를 넣어서 contextual information 출력 후 FC 거침
- VoP with Function-Specific Video Prompts (VoPF): Visual Encoder에 작은 transformer라도 붙이면 파라미터가 증가함. 현재의 visual encoder를 두 파트로 나눔
    - Ks(Intra): 각 프레임 별 self-attention 진행
    - K-Ks(Inter): 프레임끼리 묶어서 같이 학습


# 4. Experiments
## 4.1. Experimental Setup
- Evaluation Metrics: R@K (recall at rank K, higher is better), MnR (mean rank, lower is better), MdR (median rank, lower is better) 
- Implementation Details: 12-layer encoder

![Figure3](https://github.com/jaealways/must-read-paper-CV-daily/assets/71856506/c8b5b904-623d-4b6f-8fcf-376cc21edbbe)

## 4.2. Main Results
- VoP는 0.1%의 파라미터 storage로도 대등한 성능 기록
- 하나의 video prompt만 붙여도 성능 향상
- VoP 섞으면 더 높은 성능

