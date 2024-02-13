---
layout: post
title:  "[PAPER] DoLA: DECODING BY CONTRASTING LAYERS IMPROVES FACTUALITY IN LARGE LANGUAGE MODELS
"
author: jaealways
categories: [ paper, NLP]
tags: [PAPER, ICLR'24, LLM]
---


# [PAPER] DoLA: DECODING BY CONTRASTING LAYERS IMPROVES FACTUALITY IN LARGE LANGUAGE MODELS
[reference: Yung-Sung Chuang et al, "DoLA: DECODING BY CONTRASTING LAYERS IMPROVES FACTUALITY IN LARGE LANGUAGE MODELS", 2023](https://arxiv.org/pdf/2309.03883.pdf)


# TLDR

- 동일한 LLM에서 서로 다른 layer의 next-word probability를 추출해서 디코딩. 두 layer의 차이를 contrasting해서 next token 선택하자!

# ABSTRACT

- LLM의 hallucination 문제를 외부 지식이나 fine-tuning을 사용하지 않고, 해결하려 함
- LLM의 factual knowledge가 특정 계층에 국한되어 나타난 점을 이용해서, 위 계층과 아래 계층의 차이를 비교해서 디코딩하고자 함
- DoLA는 12~17% 정도의 성능을 향상시켜, LLM이 진실을 안정적으로 생성할 수 있는 가능성 보임


# 1 INTRODUCTION

- LLM의 무한한 잠재력에도, hallucinate하기 때문에 광범위한 적용에 어려움 겪음
    - 특히 clinic, legal 등의 high-stakes(위험 부담이 큰) application에선 더욱 어려움
- hallucinations이 발생하는 이유는 데이터와 모델 분포 간의 forward KL divergence를 최소화하도록 설계된 Maximum Likelihood Language Modeling 때문일지도?
    - KL divergence: 모델 아웃풋에 대한 이상적인 값을 P, 실제 결과를 Q라 함. <br>
    $ D_{KL}(P||Q) = \sum_{x\in \mathcal{X}} P(x) \log_b \left( \frac{P(x)}{Q(x)} \right) $
    - P를 기준으로 본 Q의 cross-entropy와 P의 정보엔트로피(엔트로피의 평균)의 차이 -> 즉 P와 Q가 크게 차이나면 KL divergence 값도 커짐
- 즉, 인풋이 학습데이터에 있는 지식과 일치하지 않아도 non-zero 확률을 부여(어떻게든 학습데이터 속 내용과 매칭시켜야 KL divergence 줄기 때문)
- 결과적으로 LLM이 실제 사실을 인식하지 못하고, 학습 예제의 피상적 패턴만 익히게 됨

![KLdivergence](https://github.com/jaealways/must-read-paper-NLP-daily/assets/71856506/5d561250-e9b7-4b6a-a7de-7a85d92a9b59)

- 트랜스포머 기반 언어모델은 earlier layer에서 lowerlevel(e.g., part-of-speech tags) information을 encode하고, later layer에서 semantic information을 인코딩하는 것으로 드러남
- earlier와 later layer의 로짓 차이를 이용해서, LM이 factual information을 더 학습시킬 수 있게 구조화
- Figure1처럼 16레이어에서 32레이어의 가장 큰 증가를 보인 'Olympia'를 채택
    - Seattle이 유명해서 초반엔 정답확률 높지만, LLM layer가 깊어져서 의미론적 정확도가 높아질수록 둔화됨
    - 반면 Olympia는 모델이 학습할수록 빠르게 증가해서, 실제 정답에 근사하다고 결론
- 이를 통해 외부지식을 검색하거나, fine-tuning하지 않아도 LLM의 factual information을 더 잘 표현할 수 있음

![Figure1](https://github.com/jaealways/must-read-paper-NLP-daily/assets/71856506/0204bc76-593e-4abc-bf36-3ea1d9831233)

- 본 논문에선 Decoding by Contrasting Layers(이하 DoLa)를 사용해서 사실추론을 높일 수 있음을 실험적으로 입증함


# 2 METHOD

- 요즘 언어모델들은 embedding layer + N개의 transformer layer + affine layer(next-word 분포를 예측함)로 이루어짐

$p(x_t | x_{<t}) = \text{softmax}(\phi(h_t^{N})), \quad x_t \in \mathcal{X},$
- 위 식처럼 모든 vocab set(X)에서 다음 단어의 확률을 예측함

$q_j(x_t | x_{<t}) = \text{softmax}(\phi(h_t^{(j)})), \quad j = 1, \ldots, N.$
- $\phi$를 단순히 마지막 레이어에만 적용하지 말고, 중간에도 적용해서 next token prediction에 사용하자

$\hat{p}(x_t | x_{<t}) = \text{softmax}(F(q_N(x_t), q_M(x_t)))_{x_t}, \quad \text{where } M = \underset{j \in J}{\arg\max} \, d(q_N(\cdot), q_j(\cdot)).$
- middle layer에서 next token pred하는 것을 early exit라고 표현
- premature layer: layer M, mature layer: final layer
- jensen-shannon divergence: KL divergence가 p, q 바뀌어도 symmetric한 결과 낼 수 있도록 개량. 교환법칙 성립하기 때문에 distance 개념으로 사용 가능

$D_{KL}(p||q) \neq D_{KL}(q||p)$

- 즉 여러 candidate layer 중 mature layer 사이의 거리를 최대화할 수 있는 layer를 premature로 고름


## 2.1 FACTUAL KNOWLEDGE EVOLVES ACROSS LAYERS

- 32 layer LLAMA-7B로 두 layer outout 간의 JSD 측정해서 분석 진행

![Figure2](https://github.com/jaealways/must-read-paper-NLP-daily/assets/71856506/1f92015e-0634-4820-b637-894817ea52aa)

- pattern 1: 중요한 name entities or date처럼 factual knowledge 필요한 지식 (Wole Soyinka and 1986) 예측할 때, JSD가 여전히 higher layer에서 높음
    - 모델이 여전히 factual knowledge 맞추기 위해 변하고 있다는 뜻
- pattern 2: was, the, to 같은 function word나 first Nigerian, Nobel Prize 같이 input에서 그대로 copy한 토큰 예측할 때, (즉 easy token 예측할 때) JSD는 레이어 중간부터 작아짐.
    - 즉 거의 초반에 아웃풋 결정나서 거의 안 바뀐다는 뜻
- factual knowledge는 high layer에서 많이 변하기 때문에, mid-high layer 정도를 contrast하여 변화를 leverage할 수 있음 -> 즉 최종확률이 더 낮아도 높일 수 있음


## 2.2 DYNAMIC PREMATURE LAYER SELECTION

![Figure3](https://github.com/jaealways/must-read-paper-NLP-daily/assets/71856506/010de99f-afcb-4967-94bb-05d11084de12)

- contrastive decoding의 효과를 극대화하기 위해선, optimal premature layer를 최종 output layer와 가장 큰 차이를 보이는 것으로 골라야 함
- 그래서 아래 식처럼 두 layer의 JSD를 측정해서 premature layer 선택

$d(q_N(\cdot|x_{<t}), q_j(\cdot|x_{<t})) = JSD(q_N(\cdot|x_{<t})||q_j(\cdot|x_{<t})),$

$M = \underset{j \in J}{\arg\max} \, JSD(q_N(\cdot | x_{<t}) || q_j(\cdot | x_{<t})),$

- 이 때 전체 레이어의 갯수에 따라 2~4개 정도의 bucket을 선택하고 bucket 내의 layer를 돌면서 JSD argmax인 값을 bucket의 exit로 지정
- DoLa-static: 위처럼 동적으로 layer 선택하는게 아니라, 하나의 Premature Layer 선택해서 고정하는 방법도 있음. 하지만 아래 같은 단점 있음
    - 레이어의 search space가 큼 (32 layer 가진 LLM에선 31번의 validation performance를 측정해야)
    - best layer가 데이터 분포에 따라 민감함
- 하지만 동적인 전략이 더 효과적인 것으로 드러남. 논문 뒷부분에 이를 실험적으로 입증함


## 2.3 CONTRASTING THE PREDICTIONS

$F(q_N(x_t), q_M(x_t)) = \begin{cases}
\log \frac{q_N(x_t)}{q_M(x_t')} & \text{if } x_t \in \text{V}_{\text{head}} (x_{t|x_{<t}}), \\
-\infty, & \text{otherwise}.
\end{cases}$ <br>
$\hat{p}(x_t) = \text{softmax}(F(q_N(x_t), q_M(x_t)))$

- 결국 하고 싶은 건, Mature Layer의 영향력 높이고, Premature Layer 영향력 낮추는 것
- 위의 2.2에서 등장한 premature와 mature의 차이를 위 식처럼 log 확률 사용해서 계산함
- 만약 vocab에 없을 경우, -inf 부여해서 softmax 취하면 확률값 0되게 함

- false negative: 너무 당연한 정답 토큰이 있어서 (0.809->0.813)로 확률이 변하면 F 값 0.027, next token으로 선택 안됨
- false positive: 확률값이 매우 낮은 토큰이 (0.003 -> 0.017)의 높은 상승보이면, F 값이 3.13. next token으로 선택될 수 있음

$V_{\text{head}} (x_{t|x_{<t}}) = \left\{ x_t \in \mathcal{X} : q_N(x_t) \geq \alpha \max_{w} q_N(w) \right\}.$

- 위 식처럼 Vhead 선택시 alpha*qN에서의 최대값해서, Mature layer에서 확률이 낮으면 생성 후보에서 아예 제외시켜버림
    - false negative는 어떻게 잡을까?

- Repetition Penalty: factual knowledge를 강화시키는 것이 DoLa의 목표이지만, 이로 인해 모델이 문법적으로 틀린 문단을 생성할 수 있음
    - 특히 chain-of-thought reasoning을 활용한 긴 시퀀스 생성할 때, 이전에 생성된 문장을 그대로 따라하는 경향 있음. 이에 실험할 때, repetition penalty 부여

# 3 EXPERIMENTS

## 3.1 TASKS

- 크게 multiple choice와 open-ended generation, chatbot task 진행
    - multiple choice: TruthfulQA, FACTOR
    - open-ended generation: 주어진 문장에 이어질 문장 생성. TruthfulQA, StrategyQA, GSM8K
    - chatbot: Vicuna QA benchmark

## 3.2 SETUP

- 여러 파라미터의 LLaMa 모델을 오리지널, Contrasive Decoding(서로 다른 모델과 contrast 진행), DoLa, Inference Time Intervention(ITI)로 비교 
    - CD: LLaMA-7B를 amateur model로, 13B/33B/65B를 expert model로 사용
    - ITI: LLaMa-7B에서 사용
- 모델 별로 다음과 같이 파라미터 나눔
    - LLaMA-7B (32 layers): [0, 16), [16, 32)
    - LLaMA-13B (40 layers): [0, 20), [20, 40)
    - LLaMA-33B (60 layers): [0, 20), [20, 40), [40, 60)
    - LLaMA-65B (80 layers): [0, 20), [20, 40), [40, 60), [60, 80)
- 효율적인 실험을 위해 짝수 layer만 candidate에 포함시킴

## 3.3 MULTIPLE CHOICE

![Table1](https://github.com/jaealways/must-read-paper-NLP-daily/assets/71856506/8c1d1f1c-ab7b-488d-8c43-4e3c351f4ed7)

- 모든 모델 사이즈에서 DoLa가 가장 높은 성능
- 데이터셋 별 최적 bucket
    - TruthfulQA-MC: 모든 모델에서 후반 bucket. 생성할 텍스트가 짧고, Fact가 중요
    - FACTOR: 모든 모델에서 초반 bucket. 길이가 긴 문장을 생성해야 함



## 3.4 OPEN-ENDED TEXT GENERATION

![Table2](https://github.com/jaealways/must-read-paper-NLP-daily/assets/71856506/2c82b290-bb97-4e10-b13b-8dae87d318d3)

- 성능 지표
    - Truthfulness: 얼마나 진실된 답을 하는가
    - Informativeness: 얼마나 의미있는 정보를 전달하는가
    - "I have no comment": Truth 100%, Inform 0%
- TruthfulQA에서 DoLa 적용시 Vanilla에 비해 Truthfulness와 Informativeness 둘 다 상승
- StrategyQA, GSM8K: CD 적용시 Vanilla 보다 오히려 성능 저하
    - 별도의 모델이 아니라 단일 모델로 다른 layer에서 contrast하는 것이 효과적이다


## 3.5 AUTOMATIC EVALUATION WITH GPT-4

![Figure4](https://github.com/jaealways/must-read-paper-NLP-daily/assets/71856506/28dddc4c-643d-46f6-93a0-e64b22a30c31)

- GPT-4에게 evaluation 맡겨서 챗봇 능력 평가
- DoLa는 챗봇 상황에서도 효과적


# 4 ANALYSIS

## 4.1 STATIC VS DYNAMIC PREMATURE LAYER SELECTION

![Figure5](https://github.com/jaealways/must-read-paper-NLP-daily/assets/71856506/4f2f2506-ded3-421a-a3bb-dc8e72ae1e09)

- DoLa-static은 전체 decoding에서 고정된 layer 사용
- 초기 layer에선 static이 DoLa 보다 높은 성능 보임
- 동일한 데이터셋에서도 valid set 바뀌면, 최적의 premature layer가 달라짐
- DoLa는 좀 더 robust한 결과 보임

## 4.2 RANDOM LAYER SELECTION BASELINE

![Table3](https://github.com/jaealways/must-read-paper-NLP-daily/assets/71856506/7b97178b-1d5a-4841-9a6d-248e347bdc22)

- Premature Layer를 랜덤하게 선택하면 어떨까?
- Vanilla LLaMa 보다 낮아짐

## 4.3 REPETITION PENALTY

![Figure6](https://github.com/jaealways/must-read-paper-NLP-daily/assets/71856506/23cc48ab-3c81-4fe0-8727-2219300c0947)

- Repetition penalty 준 DoLa가 전반적으로 우수한 성능 보임


## 4.4 NON-LLAMA MODEL

![Table4](https://github.com/jaealways/must-read-paper-NLP-daily/assets/71856506/0937e141-3834-4994-8c15-816463d9f0ae)

- DoLa의 효과가 LLAMA 계열에만 한정되는지 확인하기 위해 MPT-7B(MosaicML)에도 테스트 진행
- DoLa 사용했을 때 전반적으로 우수한 성능 보임

## 4.5 QUALITATIVE STUDY

![Table5](https://github.com/jaealways/must-read-paper-NLP-daily/assets/71856506/5ed58b0a-315b-414e-9635-ba1d47721db6)

- DoLa 적용하면 Truthfulness 대부분 향상하지만, example3처럼 오히려 하락하는 경우도 있었음

## 4.6 LATENCY

![Table6](https://github.com/jaealways/must-read-paper-NLP-daily/assets/71856506/be6c0ef4-9fab-4723-b9a7-8b359448559c)

- Baseline과 비교해서 1.01~1.08 배 정도의 생각보다 적은 latency 가짐


# 6 LIMITATIONS

- DoLa는 LLM의 factuality를 향상시키지만, 아래와 같은 한계 가짐
    - Focusing on Factuality: 사실성에만 주목해서 instruction following이나 learning from human feedback 같은 접근은 고려하지 못함
    - Inference-Only: human-label 사용이나 fine-tuning을 위한 knowledge base 같은 새로운 아키텍처에 대한 고민은 하지 못함
    - Not Grounding on External Knowledge: 내부 지식만을 사용하기 때문에, 모델의 잘못된 정보를 수정할 수 없음

# 7 CONCLUSION
- decoding 방법론인 DoLa를 통해 LLM의 Factual Hallucination을 완화하려 함.
- Information Retrieval 또는 Model fine-tuning 없이 다양한 task에서 Truthfulness 향상시킴
- Retrieval Module과 결합하면 더 좋지 않을까?


