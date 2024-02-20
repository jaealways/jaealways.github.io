---
layout: post
title:  "[PAPER] Cap4Video: What Can Auxiliary Captions Do for Text-Video Retrieval?"
author: jaealways
categories: [ paper, CV]
tags: [PAPER, CVPR'23, multi-modal]
---

# [PAPER] Cap4Video: What Can Auxiliary Captions Do for Text-Video Retrieval?
[reference: Wenhao Wu et al, "Cap4Video: What Can Auxiliary Captions Do for Text-Video Retrieval?", 2023](https://arxiv.org/pdf/2301.00184.pdf)


# TLDR
- CVPR 2023 highlight paper
- Cap4Video를 통해 캡션을 증강해서 text-video 모델 성능 높임
- 생성된 캡션을 학습 데이터 증강, video-caption feature interaction, Output score fusion에 사용

# Abstract

- title, tags, subtitles과 같은 관련 텍스트 정보를 활용하여 text-video retrieval 성능을 높이고자 함
- web-scale pre-trained model(CLIP, GPT-2)를 통해 제로 샷 비디오 캡션을 사용,  비디오에서 캡션을 생성하는 새로운 접근 방식인 Cap4Video를 제안
- Cap4Video는 아래 세 가지 방법으로 text-video retrieval 성능 향상
  - Input data: video-caption pair 사용해서 학습 데이터 보강
  - Intermediate feature interaction: video representation 성능 높이기 위해 비디오와 캡션 간의 cross-modal feautre interaction 수행
  - Output Score: Query-Caption matching을 통해 Query-Video matching을 보완
- 텍스트 비디오 검색 벤치마크들에서 SOTA 달성


# 1. Introduction

- global matching(video-sentence alignment)에서 fine-grained matching(frame-word alignment, videoword alignment,  multi-hierarchical alignment)으로 연구 발전하면서 성능 향상됨
- 아래 두 가지 factor가 성능 향상에 영향
    - CLIP을 통해 cross-modal learning에서의 문제를 줄일 수 있음
    - sparsely sampled 프레임으로 비전 및 텍스트 인코더를 fine-tune할 수 있음
- 위 두 가지 방법은 video와 text 간의 cross-modal aligenment를 목표로 함

- 현실에선 비디오 속 제목이나 사이트의 태그 등을 활용할 수 있고, 심지어 text-to-text retrieval의 성능을 향상시킬 수 있음. 
- How can we generate associated text descriptions for videos? 이 때 두 가지 방법이 사용 가능함
    - video website에서 video 제목 크롤링하기 -> 주석에 심하게 의존적, URL이 없다면?
    - zero-shot video caption model 사용해서 만들기
- ZeroCap: 최근 연구에서 제로샷 이미지 캡션에 CLIP 및 GPT-2를 사용해서 추가학습 없이 비디오 캡션 자동으로 생성함

![Figure1](https://github.com/jaealways/must-read-paper-CV-daily/assets/71856506/318a34cb-de64-4692-aac6-153bcb22d856)

- 보조 캡션을 생성하면, How can we leverage these captions to enhance the text-video retrieval task? 라는 질문 떠오름
- 본 논문에선 Cap4Video를 제시. 크게 세 가지 방법으로 caption 활용
  - Input data: video-caption pair로 인풋 넣음
  - Intermediate Feature Interaction: video와 caption 사이 cross-modal interaction을 통해 중복 피쳐 제거하고, video representation 성능 올림
  - Output score: query-video matching 뿐만 아니라 query-caption matching을 사용하여 text-video retrieval 향상시킴.
- 본 논문의 contribution은 다음과 같음
    - LLM에서 자동으로 생성된 캡션을 사용하는 등 새로운 방식으로 문제 접근
    - Cap4Video를 제안해서 기존 query-video matching 개선
    - 대규모 실험을 통해 Cap4Video가 SOTA 달성했음을 확인


# 2. Methodology

## 2.1. Background: Text-Video Matching

- 유사도 함수(sentence Qi, video Vj)를 사용하여 쿼리 문장과의 유사도로 비디오의 순위를 매김

### Global Matching
- 각 modality를 독립적으로 인코딩해서 global features 구함
- F 샘플링된 프레임에서 F frame embeddings을 출력하도록 visual encoder 훈련
- query 인코더는 W 워드 임베딩 및 W 워드들을 포함하는 query sentence에 대한 [CLS] 임베딩을 생성
- 유사도 계산을 위한 전역 쿼리 임베딩과 비교하여, 프레임 임베딩에 대한 average pooling을 사용하여 global video embedding을 획득

### Fine-grained Matching
- frame-word alignment과 같은 토큰 수준 정렬에 초점
- Max-Mean 파이프라인을 활용하여 patch and word tokens 간의 토큰별 최대 유사성을 찾은 다음 평균화

## 2.2. Preprocessing: Caption Generation

- 보조 캡션을 얻기 위한 두 가지 접근 방식

### Manual Crawling of Video Titles
- 원래 링크를 캡션으로 사용하여 사이트에서 제목 추출, 링크가 만료된 비디오는 패스
### Automatic Video Captioning
- LLM(CLIP + GPT-2) 활용하여 추가 훈련 없이 가능한 zero-shot video captioning 과정 거침


![Figure2](https://github.com/jaealways/must-read-paper-CV-daily/assets/71856506/d0749675-8f19-4a55-b372-2e288d74771b)

## 2.3. Data Augmentation with Auxiliary Captions
- 비디오에서 생성된 캡션에서 추가적인 positive sample 생성함
- automatic video captioner로 각 비디오에 대해 최대 20개의 캡션을 생성
- 훈련에 negative effect 주지 않기 위해서, caption과 ground-truth query의 semantic similarity 계산


## 2.4. Video-Caption Cross-Modal Interaction

- video과 caption의 complementarity을 활용하여 redundant features 줄이고 discriminative video representations 학습하도록 함
  
- frame embeddings $$ e_v = {v_1, v_2, · · · , v_F } $$
- caption embeddings $$ e_c = {c_1, c_2, · · · , c_C } $$
- F: number of frames, C: number of captions
  
![image](https://github.com/jaealways/must-read-paper-CV-daily/assets/71856506/7e357e20-2443-4ca0-a13e-806adc076854)


## 2.5. Complementary Query-Caption Matching

- 캡션은 data aug 효과 뿐만 아니라, video content를 directly represent하는 효과도 있음
- [CLS] 토큰을 통해 caption을 represent함
  
- global caption embedding과 global query embedding 간의 코사인 유사도를 계산하여 query video matching 보완

- B triples: $$ \{e_{vi}, e_{ti}, e_{ci}\}_{i=1}^B $$
$$ e_{vi}, e_{ti}, e_{ci} $$ 
- 각각 video, query, caption embedding 뜻함

- query-video global matching: $$ e_{vi}, e_{ti} $$는 averaged video feature and global [CLS] text feature 나타냄
- query-video fine-grained matching: $$ e_{vi}, e_{ti} $$ 는 sequence of frame embeddings, sequence of word embeddings 나타냄
- query-caption matching: $$ e_{ci}, e_{ti} $$ 는 sequence of caption embeddings, global [CLS] text feature 나타냄
  
- Query-Caption 간에 cross-entropy loss를 사용해서 contrasive learning 하고자 함
- sqc(·, ·)는 query-caption matching similarity function
$$
\begin{align*}
L_{Q2C} &= \frac{1}{B} \sum_i^B \log \frac{\exp(s_{qc}(e_{ti}, e_{ci}) / T)}{\sum_j^B \exp(s_{qc}(e_{ti}, e_{cj}) / T)}, \\
L_{C2Q} &= \frac{1}{B} \sum_i^B \log \frac{\exp(s_{qc}(e_{ti}, e_{ci}) / T)}{\sum_j^B \exp(s_{qc}(e_{tj}, e_{ci}) / T)}, \\
L_{QC} &= \frac{1}{2} (L_{Q2C} + L_{C2Q}),
\end{align*}
$$

- Query-Video 간에도 contrasive learning 하고자 함

$$
\begin{align*}
L_{Q2V} &= \frac{1}{B} \sum_{i}^B \log \frac{\exp(s_{qv}(e_{ti}, e_{vi}) / T)}{\sum_{j}^B \exp(s_{qv}(e_{ti}, e_{vj}) / T)}, \\
L_{V2Q} &= \frac{1}{B} \sum_{i}^B \log \frac{\exp(s_{qv}(e_{ti}, e_{vi}) / T)}{\sum_{j}^B \exp(s_{qv}(e_{tj}, e_{vi}) / T)}, \\
L_{QV} &= \frac{1}{2} (L_{Q2V} + L_{V2Q}),
\end{align*}
$$

- total loss를 아래와 같이 구함
$$
L = L_{QV} + L_{QC}
$$


# 3. Experiments: Text-Video Retrieval

## 3.1. Setups

### Datasets
- MSR-VTT: 각 20개의 캡션이 포함된 10K 개의 비디오 클립
- DiDeMo: 10K 비디오, 40K description 쌍. 
- VATEX: 각각 여러 개의 주석이 달린 약 35,000개의 비디오
- MSVD: 80K 캡션이 있는 1970개의 비디오, 비디오당 평균 ~40개의 캡션이 있음
  
### Evaluation Metrics
- R@K (Recall at K): top-k retrieve된 비디오(또는 텍스트)에서 정답 있는지 확인
- MdR(Median Rank): retrieval ranking list에서 ground-truth의 median rank 계산
- MnR(평균 순위): retrieval ranking list에서 ground-truth의 median rank 계산
- MdR, MnR은 낮을수록 성능 높은 것임

### Implemenation detail
- CLIP의 Visual Encoder를 Video encoder로, Textual Encoder를 caption, query encoder로 사용
- 영상당 30개의 캡션을 생성하도록 함
- zero-shot 캡션 생성을 위해 CLIP 및 GPT-2 모델 사용


## 3.2. Comparison with State-of-the-Arts

![Table2](https://github.com/jaealways/must-read-paper-CV-daily/assets/71856506/e7582559-bb00-48a6-b7de-f1fbda14ff2d)

- 네 가지 벤치마크 모두에서 대부분의 지표가 SOTA 기록

## 3.3. Ablation Study

![Table5](https://github.com/jaealways/must-read-paper-CV-daily/assets/71856506/569ad9a6-5202-48f3-bf38-96f4b68bc061)

### Auxiliary Caption as Data Augmentation
- 현실에선 video title이 additional auxiliary caption으로 사용될 수 있음
- 때문에 video title과 GPT-2에 의해 생성된 캡션을 비교함
- GPT-2에 의해 생성된 캡션을 train에 사용하면 matching 성능 약간 향상
- Top-1만 사용해도 효과적임을 입증

![Table6](https://github.com/jaealways/must-read-paper-CV-daily/assets/71856506/14a0582d-94fc-443f-8956-07821911f7d1)

### Benefit on Both Online and Offline Videos Scenarios
- 오프라인 동영상의 경우 CLIP+GPT-2 사용해서 캡션 생성
- 온라인 동영상의 경우 원래 웹사이트 제목을 캡션으로 직접 사용
- 온라인 및 오프라인 모두 baseline에 비해 개선됨

### Video-Caption Feature Interaction
- Sum, MLP, Cross Transformer, Co-attention Transformer approach 시도
- Co-attention Transformer가 가장 높은 성능 향상 기록

### Query-Caption matching
- Query-Video matching을 보완하여 QC, QV 조합을 통해 추가 개선

- Cap4Video는 input data augmentation, intermediate feature interaction, output score fusion 세 가지 방법을 통해 생성된 캡션 활용


![Figure4](https://github.com/jaealways/must-read-paper-CV-daily/assets/71856506/cb2c9df0-4098-41e7-9288-609f05cb0dd3)

## 3.4. Visualization
- 보조 캡션을 사용하여 이전 모델보다 높은 정확성 보임


# 5. Conclusion

- Cap4Video를 통해 캡션을 증강해서 text-video 모델 성능 높임
- 생성된 캡션을 학습 데이터 증강, video-caption feature interaction, Output score fusion에 사용

