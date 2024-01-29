---
layout: post
title:  "[PAPER] G-EVAL: NLG Evaluation using GPT-4 with Better Human Alignment"
author: jaealways
categories: [ paper, NLP]
tags: [PAPER, EMNLP'23, evaluation, LLM]
---


# [PAPER] G-EVAL: NLG Evaluation using GPT-4 with Better Human Alignment

[reference: Y Liu et al, "G-EVAL: NLG Evaluation using GPT-4 with Better Human Alignment", 2023](https://arxiv.org/pdf/2303.16634.pdf)

# TLDR
- GPT-4를 백본으로 사용한 G-EVAL은 NLG task에서 human evluation과 매우 높은 상관관계 보임

## NLU
- Natural Lauguage Generation: LLM이 자연어 문장을 얼마나 잘 생성하는가?
- 결국 sequence x를 얼마나 잘 표현할 수 있는가?
- summarization, text expansion, style transfer 등 있음...
## NLU Metrics
- BLUE: Generated Sentence의 단어가 Reference Sentence에 포함되는 정도
- ROUGE: Reference Setence의 단어가 Generated Sentence에 포함되는 정도

# Abstract

- BLEU와 ROUGE 같은 NLG metric은 인간의 직접적인 판단과 낮은 상관관계 가짐
- 인간이 annotation 하지 않아도 가능한 metric 만들어보자!
- CoT(Chain of Thought)와 폼 필링(Form-filling)을 활용한 LLM framework인 G-EvAL 제시
- G-EVAL은 GPT-4를 백본으로 사용하여 인간의 판단과 0.514의 Spearman 상관관계를 달성
- 이외에도 LLM 기반 평가자 분석으로 LLM 생성 텍스트에 대한 잠재적 편향성 밝힘


# 1 Introduction

- BLEU, ROUGE, MATEO 등의 NLG metric은 인간 점수와 낮은 상관관계 보임.
- 최근의 연구들은 LLM을 평가자로 활용하자고 하지만, 신뢰성에 의문 제기
- Form-filling(?): 양식을 채워서 넣는다는 뜻(?)
- G-EVAL은 NLG 평가에 Form-filling에서 CoT 쓰는 LLM을 사용
- G-EVAL은 텍스트 요약 및 대화 생성에 대한 인간 평가와 상관 관계에서 기존 평가자를 능가
- main contribution
    - LLM based metric이 얼마나 우수한지
    - Context나 Instruct에 대해 CoT가 얼마나 효과적인지
    - LLM 생성 텍스트가 얼마나 편향적인지 등 제시

![Figure1](https://github.com/jaealways/TIL-daily/assets/71856506/c9b603c5-856d-445e-9441-cff4700bea9a)



# 2 Method

- G-EVAL은 아래 세 가지 구성요소 가지는 프롬프트 기반 평가 프레임워크
    - Prompt for NLG Evaluation: 다양한 NLG 과제에 맞춘 일관성, 간결성, 문법 등의 평가 과제와 구체적인 기준을 명확하게 제시
    ```
    Evaluation Criteria:
    Coherence (1-5) - the collective quality of all sentences. We align this dimension with the DUC quality question of structure and coherence whereby ”the summary should be well-structured and
    well-organized. The summary should not just be a heap of related information, but should build from sentence to sentence to a coherent body of information about a topic.”
    ```
    - Auto Chain-of-Thoughts for NLG Evaluation: LLM에게 evaluation steps를 위한 context와 instruct 제공, 평가 과정을 보다 투명하게 만듦
    - Scoring Function: LLM을 호출하여 평가 기준에 따라 점수를 할당하는 프롬프트

- G-EVAL은 LLMs의 결과의 가중합 사용

## 3 Experiments

- SumEval, Topic-Chat, QAGS의 세 가지 벤치마크 사용
- 텍스트 요약과 대화 응답 생성이라는 두 가지 NLG 작업, meta-evaluation
- meta-evaluation: 평가에 대한 평가, 위의 벤치마크 사용해서 G-eval의 평가 성능 평가

## 3.1 Implementation Details

- LLM 중 GPT-3.5(text-davinci-003), GPT-4 사용
- text-davinci-003: 인간 피드백 모델을 기반의 강화학습으로 좋은 성능 보이는 모델
- GPT-3.5의 디코딩 temparture를 0
- GPT-4는 토큰 확률 output 없어서 추정하기 위해 'n = 20, temparture = 1, top p = 1'로 20번 샘플링
- G-EVAL에서 백본 모델로서 GPT-4와 GPT-3.5의 사용을 나타내기 위해 G-EVAL-4와 G-EVAL-3.5 도입
- 부록에 다양한 작업에 대한 예제 프롬프트 있음


## 3.2 Benchmarks  

- G-EVAL과 인간의 판단 간의 상관관계를 평가하기 위해 SumEval, Topic-Chat, QAGS의 세 가지 메타평가 벤치마크를 사용
- SumEval: CNN/DailyMail 데이터셋 사용하여 유창성, 일관성, 일관성 및 관련성에 대한 인간의 평가를 기반으로 평가
- Topic-Chat: 인간의 평가에 따라 자연스러움, 일관성, 참여성, 근거성을 평가하는 대화 응답 생성 시스템의 테스트베드 역할
- QAGS: 두 가지 다른 요약 데이터셋을 사용하여 환각과 관련하여 요약의 일관성 평가

## 3.3 Baselines  

- G-EVAL은 자연어 생성(NLG) 작업을 위한 다양한 최첨단 평가자와 비교
- **BERTScore**: BERT의 상황별 임베딩을 사용하여 텍스트 유사도를 측정
- **MoverScore**: 강력한 유사성 측정을 위해 부드러운 정렬과 새로운 집계 방법으로 BERTS 점수를 향상
- **BARTScore**: 인코더-디코더 모델을 기반으로 텍스트를 평가하기 위해 BART의 평균 가능성을 사용하는 통합 평가자로, 다양한 소스 및 대상 형식에 적응
- **FactCC와 QAGS**: 둘 다 생성된 요약의 사실적 일관성을 측정하는 데 초점
    - FactCC는 BERT 기반 분류기를 사용
    - QAGS는 질의응답 방식을 사용
- **USR**: 다양한 관점에서 대화 응답 생성을 평가, 응답을 채점하기 위한 여러 버전 제공
- **UniEval**: pre-trained T5 모델을 사용하여 텍스트 생성을 QA 작업으로 평가하는 통합 평가자로 질문 형식 변경을 통해 다양한 평가 작업에 적응
- **GPTScore**: GPT-3와 같은 생성 사전 훈련 모델을 사용하여 텍스트를 양식 채우기보다는 조건부 생성에 초점을 두고 고품질 텍스트 생성 확률을 기준으로 평가

![Table1](https://github.com/jaealways/TIL-daily/assets/71856506/0fa17636-402b-4754-8488-df7a03f30bd6)


## 3.4 Results for Summarization  

- Zhong et al. (2022) 접근법은 요약 수준의 Spearman과 Kendall-Tau 상관관계로 요약 평가

- 모델 출력과 참조 텍스트 간의 의미론적 유사성을 평가하는 메트릭은 대부분의 차원에서 성능이 좋지 않음
- 인간의 평가로 학습된 메트릭은 훨씬 더 높은 상관 관계를 보임
- GPTScore를 포함한 GPT 기반 평가자들은 평가를 위해 GPT 모형을 사용하지만 조건부 확률에 의존
- G-EVAL은 SumEval 벤치마크에서 이전의 모든 평가자들보다 유의하게 높은 성과
- G-EVAL-4가 G-EVAL-3.5보다 인간 지표와의 상관관계 높음
- G-EVAL은 여러 차원에서 GPTS 점수를 능가하여 형태채움 평가 패러다임의 효과를 강조

## 3.5 Results for Dialogue Generation  

- 주제-채팅 벤치마크를 사용하여 대화 응답 품질에 대한 다양한 평가자 간의 합의와 인적 평가를 평가
- 각 대화 순서에 대해 피어슨 및 스피어먼 상관 관계 계산
- 유사성 기반 측정지표는 응답의 참여성과 근거성에 대한 인간의 판단과 잘 일치, 다른 측면에서는 정확성이 떨어짐
- G-EVAL 이전에는 UniEval이 가장 좋은 성능
- G-EVAL은 Topic-Chat 벤치마크에서 다른 모든 최첨단 평가사들을 크게 앞질름
- G-EVAL-3.5와 G-EVAL-4 모두 유사한 결과를 얻었으며, 이는 G-EVAL 모형에 대한 벤치마크의 상대적 용이성

## 3.6 Results on Hallucinations  

- 최근 LLM들은 환각을 생성하는 경우가 많음.
- 요약의 일관성 측면을 측정하기 위한 평가자 작성에 초점, QAGS와 같은 벤치마크의 활용
- QAGS는 CNN/DailyMail(더 추출적)과 XSum(더 추상적)의 두 가지 요약 데이터 세트를 포함
- BARTScore는 CNN/DailyMail에서는 좋은 성과를 보이지만 Xsum에서는 낮은 상관관계
- UniEval은 두 QAGS 데이터셋 모두에서 좋은 상관관계
- G-EAL-4는 QAGS, 특히 더 어려운 XSum 데이터셋에서 좋은 성능
- 일관성 평가에 있어 LLM 역량의 중요성을 강조하면서 G-EVAL-3.5는 벤치마크에 어려움


# 4 Analysis

### Will G-EVAL prefer LLM-based outputs? 

- Zhang et al.(2023)의 데이터셋을 통해 LLM 생성 요약과 인간 요약을 비교
- 데이터 세트 범주
    - GPT-3.5보다 높은 평가를 받은 인간 요약
    - 낮은 평가를 받은 인간 요약
    - 인간 및 GPT-3.5 요약은 동일하게 평가
- G-EVAL-4는 인간 심사위원이 인간이 작성한 내용을 선호하는 경우에도 LLM 생성 요약에 더 높은 점수를 부여
- 고품질 NLG 산출물 평가의 어려움과 LLM 생성 요약에 대한 G-EVAL의 고유한 편향

- 주석 간 일치도(Krippendorff's alpha in 0.07)가 낮다는 것은 NLG 산출물을 평가하는 것이 어렵다
- LLM 기반 평가자가 자체 평가 기준에 대한 자기 강화 및 과적합으로 이어지는 위험성을 강조


### The Effect of Chain-of-Thoughts 

- G-EVAL-4의 성능은 CoT를 포함한 경우와 포함하지 않은 경우 SumEval 벤치마크에서 비교
- CoT를 사용한 버전은 평가된 모든 차원, 특히 영향력에서 더 높은 상관 점수를 달성
- CoT가 생성된 텍스트를 평가할 때 LLM에 대한 가치 있는 컨텍스트와 지침을 추가
- CoT는 또한 평가 과정과 결과를 명확히 하는 데 도움


### The Effect of Probability Normalization 

- SumEval 벤치마크에 대한 G-EVAL-4의 성과를 확률 정규화를 실시한 경우와 실시하지 않은 경우를 비교
- 확률 정규화가 없는 G-EVAL-4는 일치 또는 불일치 쌍으로 계산되지 않은 점수 동점으로 인해 더 높은 켄달-타우 상관 관계
- 텍스트 품질을 평가하는 모델의 능력을 정확하게 나타내지 못할 수 있음
- G-EVAL-4의 확률 정규화는 텍스트 간의 미묘한 차이를 포착하여 보다 상세하고 연속적인 점수를 제공
- 이 방법은 점수 순위에 초점을 맞추어 Spearman 상관 관계가 더 높아지므로 보다 정확한 평가 능력을 반영

### The Effect of Model Size

- G-EVAL의 성과는 SumEval과 QAGS 벤치마크에서 서로 다른 모형 크기(G-EVAL-4 대 G-EVAL-3.5)에 걸쳐 비교
- G-EVAL-4는 일반적으로 대부분의 차원과 데이터셋에서 G-EVAL-3.5보다 높은 상관 점수를 달성
- 예외는 Topic-Chat 벤치마크에서 G-EVAL-3.5의 성능이 유사하거나 우수한 결합도 및 접지도 차원에서 발견
- 이러한 비교는 특히 일관성 및 관련성 평가와 같은 복잡한 작업의 경우 LLM 모델의 크기가 클수록 G-EVAL의 평가 능력이 향상


# 5 Related Work

### Ngram-based Metrics 

- Ngram 기반 메트릭은 생성된 텍스트와 참조 텍스트 간의 어휘적 중첩을 측정하여 NLG 모델을 평가
- 기계번역에서 널리 사용되는 BLEU는 수정된 n-그램 정밀도의 기하평균을 계산하고 간결성 페널티를 적용
- 요약 평가를 목표로 하는 ROUGE는 생성된 요약과 참조 요약 사이의 n-그램 중첩을 측정
- 이런 측정방법은 콘텐츠 품질을 정확하게 측정하거나 구문 오류를 포착하지 못함


### Embedding-based Metrics 

- 임베딩 기반 메트릭은 단어 또는 문장 임베딩을 사용하여 생성된 텍스트와 참조 텍스트 간의 의미론적 유사성을 측정

- WMD(Word Mover's Distance)는 워드 임베딩을 기반으로 두 텍스트 사이의 거리 계산
- BERTScore: 텍스트 간 유사성을 평가하기 위해 BERT의 상황별 임베딩을 활용
- MoverScore: 보다 강력한 유사성 측정을 위해 부드러운 정렬과 새로운 집계 방법으로 BERTScore를 향상
- 제안된 다른 메트릭은 전체 의미 정렬에 초점을 맞추어 문장 임베딩을 사용하여 다중 문장 텍스트 간의 유사성을 계산

### Task-specific Evaluators 

- 작업별 메트릭은 요약 또는 대화 응답 생성과 같이 특정 작업에 맞춘 기준에 기초하여 NLG 모델을 평가
- 요약을 위해 이 지표들은 생성된 요약의 일관성을 평가하며, 여러 연구들은 이 측면에 초점
- 대화 응답 생성을 위해서는 생성된 응답의 일관성이 핵심적인 평가 기준
- 그들의 특정 애플리케이션들에 효과적이지만, 태스크-특정 메트릭들은 상이한 NLG 태스크들에 걸쳐 일반화가능성이 부족
- 이러한 메트릭은 생성된 텍스트의 전체 품질에 대한 포괄적인 측정을 제공하지 않음

### Unified Evaluators 

- 다양한 입력/출력들 또는 상이한 모델 변형들을 이용하여 텍스트 품질을 다차원으로부터 평가
- 통합 평가자인 UniEval은 QA 과제를 통해 텍스트 생성의 다양한 측면을 평가
- UniEval은 질문 형식을 수정함으로써 텍스트 품질 평가에 대한 유연성과 포괄적인 접근 방식을 보여주면서 다양한 평가 작업에 적응


### LLM-based Evaluators 

- Fu et al.(2023)은 GPT-3와 같은 생성적 사전 훈련 모델을 사용하여 텍스트를 평가하는 프레임워크인 GPTScore를 도입하였는데, 이들 모델이 고품질 텍스트에 더 높은 확률을 부여한다는 가정에 근거
- Wang et al.(2023)은 사전조사에서 NLG 업무의 평가자로서 ChatGPT의 활용방안을 탐색
- Kocmi와 Federmann(2023)은 기계번역 과제 평가를 위해 GPT 모델을 구체적으로 활용할 것을 제안하였으며, NLG 평가에서 LLM의 적용을 확대


# 6 Conclusion

- 생성된 텍스트 품질을 평가하기 위해 CoT(Chain of Thought) 접근 방식과 함께 LLM(Large Language Model)을 사용하는 프레임워크인 G-EVAL을 소개
- 텍스트 요약과 대화 생성 과제에 대한 실험을 수행하여 기존 평가자에 비해 G-EVAL의 우수한 성능 입증
- LLM 생성 텍스트에 대한 잠재적 편향에 주목하여 LLM 기반 평가자의 행동에 대한 예비 분석을 제공


