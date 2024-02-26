---
layout: post
title:  "[PAPER] Prompted Opinion Summarization with GPT-3.5"
author: jaealways
categories: [ paper, NLP]
tags: [PAPER, ACL'23, summarization, prompt]
---

# [PAPER] Prompted Opinion Summarization with GPT-3.5

[reference: Adithya Bhaskar, "Prompted Opinion Summarization with GPT-3.5", 2023](https://aclanthology.org/2023.findings-acl.591.pdf)


# Abstract
- LLM의 강력한 요약 성능을 Opinion Summarization에도 적용하고 싶음
- GPT-3.5를 기반으로 아래와 같은 시도
    - 대규모의 유저 리뷰를 다루기 위해 recursive summarization 사용
- Human Evaluation 결과, GPT-3.5가 높은 성능 보임
    - 기존 evaluation metric이 요약 성능 충분히 발휘하지 못하기 때문에 새로운 metric 제안


# 1 Introduction
- GPT-4까지 LLM 요약이 발전했지만, opinion summarization은 좀 더 높은 추상화가 필요했기 때문에 다소 어려운 부분이었음
- 본 논문에선 GPT-3.5 기반으로 제품, 호텔, 비즈니스 리뷰 요약을 진행
    - 리뷰와 포스트의 길이가 max input length를 초과하기도 함
    - 또 인풋 데이터의 스타일에 따라 단순히 인풋을 메아리처럼 반복하기도 함
- 이를 해결하기 위해 아래와 같은 파이프라인 제안
    - 추출 요약 모델을 통해 문장의 부분집합을 필터링함
    - 반복된 요약으로 chunking
    - 리뷰-점수 기반으로 층화
- 문장별 주제 예측 및 클러스터링 단계를 포함하는 방법도 살펴보고자 함
- 우리 요약 방법은 사람 평가에서 높은 점수 받음. 
    - Rouge, BERTScore 등 기존 지표로는 찾아낼 수 없는 미묘한 포인트가 있음
    - 생성된 요약문의 factuality, faithfulness, genericity를 측정하기 위해 entailment를 사용하는 metric 제시
- 또한 인풋이 길어질수록 GPT-3.5의 요약 성능이 떨어짐
    - 추출 요약 모델 QFSumm 사용해서, 이전 문장을 필터링해서 결과 향상
- 본 논문의 주된 기여는 다음과 같음
    - GPT-3.5를 사용해서 chunking을 사용한 계층 요약 및 추출 요약을 사용해서 사전 extract
    - 인간 평가를 바탕으로 보다 객관적인 평가가 필요함을 입증함
    - 요약을 평가하기에 더 적합한 지표 개발

![Figure1](https://github.com/jaealways/must-read-paper-NLP-daily/assets/71856506/5ba24ac0-b3c5-417c-b7b1-d4393d57a154)

# 2 Motivation and Problem Setting
- 리뷰 요약은 여러 리뷰 텍스트를 일관된 시놉시스로 요약하는 것을 말함

## 2.1 Desiderata
- Opinion Summary는 세 가지 특성을 가져야 함
- 요약은 faithful 해야 함
    - 먼지 많은 카펫에 대한 불평이 더 많았다면 이를 선택해야 함
    - 즉 faithful은 한정된 output을 잘 관리하는 것을 말함
- 요약은 factual해야 함
    - 소수라도 잘못된 정보가 있으면, 사실로 간주될 수 있음
- 요약은 relevant해야 함
    - 호텔 방의 청결에 대한 요약에서 음식에 대한 리뷰는 제외되어야 함

## 2.2 Framework
- 요약이 모든 리뷰를 대표해야 하지만, 그 수가 너무 많음
- 때문에 여러 단계별로 시스템 S를 구축하고, 이전 시스템의 아웃풋을 인풋으로 취함 -> 조금씩 단계적으로 요약 진행

# 3 GPT-3.5 Summarization Pipelines
- 요약 파이프라인을 extractor와 summarizer로 나눌 수 있음
    - Extractor: 리뷰 셋에서 관련된 파트 선택
- GPT-3.5 Topic Clustering (T): 각 문장의 단일 단어 주제를 생성해달라고 함
- QFSumm-long (Q): QFSumm 사용해서 입력 텍스트에서 관련성이 가장 높은 문장을 최대 35개까지 추출
- Review Stratification (R): 리뷰 점수 별 클러스터링 및 각 클러스토 요약
- GPT3.5-chunking (C): 일부 파이프라인에선 추출기 대신 chunking 사용. 나중에 결과 concat함

![Table1](https://github.com/jaealways/must-read-paper-NLP-daily/assets/71856506/2e3e486a-67e0-4df6-a568-a1a3ebaf1c7f)


# 4 Evaluation
## 4.1 Datasets

![Figure3](https://github.com/jaealways/must-read-paper-NLP-daily/assets/71856506/18223ad2-a007-4355-8b54-787abd2a192e)

- SPACE: 호텔의 {general, rooms, building, cleanliness, location, service, food} aspect 별 리뷰 요약 데이터셋
    - 일부 데이터셋이 Figure3처럼 너무 길어서 파이프라인 사용
- FewSum: Amazon, Yelp의 제품 리뷰. aspect 별 분류 없고 리뷰 길이도 더 짧음

![Table2](https://github.com/jaealways/must-read-paper-NLP-daily/assets/71856506/ce987e46-c478-4b22-b7ea-fe138f6ddd99)

## 4.2 Automatic Eval: ROUGE and BERTScore
- 데이터셋 별로 Rouge, BERTScore 계산
- 어떤 파이프라인을 사용해도 점수에 큰 차이가 없는데, 이에 대해 의문을 가짐
- 가령 Figure2처럼 “The rooms were clean.” 대신 "The reviewers found the rooms to be clean”와 같이 출력하면 n-gram 기반 점수는 떨어짐

![Figure2](https://github.com/jaealways/must-read-paper-NLP-daily/assets/71856506/cd8dcd1d-f55b-480c-ace2-76feb060ff85)

- 요약 성능을 테스트하는 다른 지표가 필요하다고 결론 내림

![Table4](https://github.com/jaealways/must-read-paper-NLP-daily/assets/71856506/d9b7cbe8-4ce5-4c34-93c3-29fcff855c09)

## 4.3 Human Evaluation
- Human Evaluation을 할 때, 성능에 대한 신뢰도를 높이기 위헤 데이터를 무작위로 파이프라인 별로 추출
- 평가지표는 Factuality, Faithfulness, Relevance 세 가지임
- Representativeness도 지표로 추가함
    - "While most reviewers thought" 등에서 정말 most가 맞는지 등 평가
- Krippendorff’s alpha, Fleiss-Kappa: 평가자들의 의견이 일치할수록 높아짐
- TCG는 TQG와 QG에서 상당히 개선됨을 보임
- 전반적으로 최대 점수 달성했다고 주장

# 5 New Tools for Evaluation and Analysis
- 자동평가를 통해 개선될 수 있는 것 중 하나가 faithfulness임
    - faithfulness: 시스템이 얼마나 일관성과 진실성을 유지하는지임
- entailment를 support의 대용치로 사용하면 어떨까?
- 가령, 문장 간 entailment 계산해서 요약 점수로 사용
- 또한 genericity도 evaluate하고 싶음

## 5.1 Terminology
- 두 문장 s1,s2의 점수가 1.0이면 완벽한 entailment

## 5.2 Evaluation of Entailment
- entailment가 과연 human evaluation의 mentioned viewpoint의 support를 식별하는데 효과적인지 판단하고자 함
- entailment 주장이 받아들여짐

## 5.3 Faithfulness: Support Set Sizes
- 