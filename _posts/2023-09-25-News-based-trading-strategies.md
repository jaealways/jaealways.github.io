---
layout: post
title:  "[PAPER] News-based trading strategies"
author: jaealways
categories: [ paper, data science, quant, NLP]
tags: [paper]
---


# [PAPER] News-based trading strategies

Stefan Feuerriegel et al 분의 "News-based trading strategies"(2018) 논문을 참고하여 본문의 내용을 구성하였습니다.

[reference: Stefan Feuerriegel et al, "News-based trading strategies", 2018](https://arxiv.org/pdf/1807.06824.pdf)


# 0. Abstract
- 뉴스 감성을 모멘텀 전략과 조합하고, 랜덤 포레스트, 강화학습으로 트레이딩을 학습시키니 좋은 성능을 보임(자세한 방법론이 online appendix에 있다하는데 찾지 못함)

<br><br>
# 1. Introduction

- 시장이 효율적인지는 정보 이용가능성에 의해 좌우되는데, 뉴스 정보는 실제 주식 시장에 크게 영향을 미치기 때문에 시장 효율성에 중요함
- 선행 연구를 통해 뉴스와 주식시장 사이를 연결했지만, 구체적으로 어떻게 투자 의사 결정을 용이하게 하는지는 미지수
- 본 논문은 뉴스 감성이 **<u>실제 뉴스기반 트레이딩에 어떻게 활용될 수 있는지</u>** 말하고자 함<br><br>

- 본 논문은 아래와 같은 방법으로 트레이딩을 위한 Decision Support System에 대해 살펴보고, 결과의 정확성을 평가하고자 함
    - 다양한 규칙기반 거래전략을 제시
    - 뉴스 기반 거래 시스템이 뉴스 기반 데이터에 유의미한 투자 결정을 낸다는 증거 발견

<br><br>
# 2. Related work
- 뉴스 기반 거래 시스템의 구성에 대해 간단하게 설명하고자 함
## 2.1 Benchmarks
- 거래 전략의 성과를 검증하기 위해선, 주가 지수와 같은 벤치마크를 삼아야 함
## 2.2 Transaction fees
- 거래 비용이 고려되야 하지만, 많은 선행연구는 이를 무시하고 있음
## 2.3. Trading strategies
- 최근 논문들은 딥러닝(오토인코더) 학습을 통해 결과를 예측하는데, 재무 공시의 예측력이 고려되지 않음
- 본 논문에선 다양한 데이터 마이닝 알고리즘을 사용해서 **<u>규칙 기반 뉴스 거래 알고리즘</u>** 을 제안하고자 함

# 3. Background
- 데이터셋과 감성 분석에 대한 배경을 소개하는 챕터

## 3.1 Data sources
- 기업들이 하는 **<u>수시(ad-hoc)공시(정보 불균형을 해소하기 위해 기업의 주요 경영정보 변경을 지체없이 공시하도록 하는 것)</u>** 는 가격에 단기적인 영향을 미치는 내용이 담겨있고, 사전 승인이 필요하기 때문에 퀄리티를 보장할 수 있음
- 이에 본 논문은 수시공시를 기반으로 한 뉴스 데이터를 활용하고자 함

## 3.2 Sentiment analysis

- 감성분석: 문서의 텍스트 재현(representation: 텍스트를 수치형 데이터로 변환하는 것)을 통해 긍정 부정을 측정하는 방법론 
- 본 논문에선 **<u>뉴스 발표 후 주가 움직임을 통해 텍스트 데이터의 영향을 측정하고자 함</u>**

# 4. Trading strategies

![Alt text](posts/Image/2023-09-25-News-based-trading-strategies/Fig1.png)

- Figure1은 뉴스 거래 기반 의사결정 시스템을 그림으로 표현한 것. 크게 다음 과정을 포함하고 있음
    - 텍스트를 통해 뉴스 감성을 추출함
    - 거래 전략에 기반해 거래를 실행
- 본 논문에선 아래 두 가지 전략을 결합해서 거래 전략 제안
    - **<u>자산의 과거수익률 기반으로 변화율을 극대화하는 모멘텀 매매 기법</u>**
    - 뉴스 심리 신호를 통해 투자의사결정 제안
    - 위의 두 가지를 혼합해 거래를 자동화하기 위해 지도학습과 강화학습 활용
- 다만 뉴스에 지나치게 민감하게 반응하는 동전주는 평가에서 제외

## 4.1 Benchmarks: momentum trading and portfolio approach

$$ RoC_{i,t}=\frac{p_{i,t}-p_{i,t-\delta}}{p_{i,t-\delta}} $$
- $p_{i,t}$는 t 시점의 i 주식의 가격

- 변화율(Rate-of-change, 이하 RoC)을 하나의 벤치마크로 삼아 변화율이 높은 주식으로 매수, 매도하는 전략 사용
- 이 때 **<u>20개의 주식 포트폴리오 간의 비교를 통해 상대적 변화율로 매수 매도 결정을 하므로, 수익률과 위험이 평균화되는 효과 가짐</u>**

## 4.2 Rule-based news trading
- 뉴스에 기반한 거래를 위해, 새로운 공시가 나오면 감성점수를 새롭게 산출
- **<u>특정 보도의 뉴스심리가 지나치게 긍정적이거나 부정적이면 그에 맞게 주가가 움직일 확률 높음</u>**
- 주식 A에 대한 시그널이 positive 임계값인 $\theta_S^+$ 보다 크거나, negative 임계값인 $\theta_S^-$ 보다 작으면 거래

## 4.3 Combined strategy with news and momentum trading
- 앞의 두 챕터에서 다룬 모멘텀과 뉴스심리 방법을 결합해서, **<u> 1) 극단값으로 가는 뉴스 공시를 갖고, 2) 이전 모멘텀과 동일한 방향으로 가는 자산에 투자하고자 함</u>**

## 4.4 Strategy learning
- 규칙 기반 알고리즘은 새로운 패턴에 취약하기 때문에, 지도학습을 통해 알고리즘의 예측 성능과 자유도를 높이고자 함
- 규칙 기반 알고리즘은 지난 트레이딩 경험에 의존해서 현재 트레이딩 결정을 하기 때문에, 강화학습을 통해 트레이딩 시행착오 경험을 학습하고자 함
    - state-action 함수 $Q(s_t,a_t)$: 각 state $s_t$에서 가능한 action $a_t$에 대한 기대값을 계산하는 함수
    - 최적의 정책인 $\pi^*(s_t,a_t)$를 통해 $Q(s_t,a_t)$의 값을 최대화하고자 함 (구체적으론 Q-Learning 사용)
- 랜덤 포레스트를 통해 주식을 보유하거나, 새로운 것에 투자하는 결정을 학습하고, 강화학습을 통해 현재 및 이전 공시를 나타내기 위한 상태를 학습시킴?


# 5. Evaluation
- 본 챕터에선 여러 다른 거래 전략의 성과를 밴치마크와 비교해서 평가
## 5.1 Benchmarks: stock market index and momentum trading
- 독일 주식시장 지수인 CDAX를 벤치마크 삼음. 해당 지수는 04년에서 11년까지 50.99% 상승하고, 양의 수익률을 1092일, 음의 수익률을 864일 기록
- 과거 주가 추세를 활용해 모멘텀 거래를 할 경우, 일평균 수익률의 변동성은 ?

## 5.2 News trading
- 뉴스 기반 데이터를 투자 결정 모델에 어떻게 포함시킬까?
- [앞서 살펴본 것처럼,](#42-rule-based-news-trading) 임계치 $\theta_s$를 먼저 정의하는 것이 중요. 아래 Figure3에 의하면 10% 내외의 임계치를 정하는게 가장 좋은 결과 보임.
![Alt text](posts/Image/2023-09-25-News-based-trading-strategies/Fig3.png)

- 10% 분위수를 기준으로, 뉴스 기반 전략을 평가하면, 일평균 수익률은 0.4722%, 변동성은 0.078
- 뉴스와 모멘텀을 결합한 전략을 사용하면 일평균 수익률은 0.0335%까지 낮아지지만, 변동성도 0.028로 낮아져서 위험에 상대적으로 덜 노출됨.
![Alt text](posts/Image/2023-09-25-News-based-trading-strategies/Fig4.png)

- Figure4는 두 전략의 첫 500 영업일 동안의 누적수익률을 보여주는데, 모멘텀을 같이 고려한 전략의 변동성(위험)이 감소한 것을 확인
- 두 전략 모두 다른 뉴스 기반 전략 보다 많은 거래(2~3 영업일마다 거래가 발생)를 하지만, 더 높은 평균 수익률을 거둠
![Alt text](posts/Image/2023-09-25-News-based-trading-strategies/Fig6.png)

- 위의 Figure6에서 볼 수 있듯이, 지도학습이 조금 더 우수한 수익률을 보였지만, 강화학습의 변동성이 더 낮았음. 

## 5.3. Comparison
- 2004년 1월에 시작해서 1956 영업일 동안 거래한 결과가 아래 Table 1과 같음
![Alt text](posts/Image/2023-09-25-News-based-trading-strategies/Table1.png)
- 벤치마크 CDAX는 일평균 0.0298%, 모멘텀 전략은 0.0464%, **<u>뉴스 전략 시 0.4722%, 지도학습을 통하면 1.1807%를 달성</u>**
- 거래비용을 0.1%로 가정했으며, **<u>단순히 수익률만 높다고 우수하다고 할 수 없기 때문에 변동성 대비 수익률을 측정함</u>**

## 5.4 Statistical tests
- 본 챕터에선 일일 수익률이 0 보다 높음을 보이기 위해, 윌콕슨 검정, t 검정을 수행
- 아래 Table2를 통해 통계적으로 유의함을 밝혔고, **<u>거래 전략을 사용할 경우 일일 수익률이 0 이상이라 결론</u>**<br><br>
![Alt text](posts/Image/2023-09-25-News-based-trading-strategies/Table2.png)

- 아래 Table3를 통해 비정상수익률(통상적인 시장 움직임 이상의 주가 변화)도 0 이상임이 통계적으로 유의함
![Alt text](posts/Image/2023-09-25-News-based-trading-strategies/Table3.png)

## 5.5 Example
- 아래 Figure7처럼 공시로 인해 SAP 주가는 급상승(3.98%)했는데, CDAX 지수는 일정한 수준 보임. 3.79%의 비정상 수익률 기록
![Alt text](posts/Image/2023-09-25-News-based-trading-strategies/Fig7.png)

# 6. Discussion
- 본 챕터에선 시사점과 한계점에 대해 말하고자 함

## 6.1. Managerial implications
- 본 논문은 자동화된 거래 시스템에 대해 제시했지만, 다음과 같은 한계를 가짐
    - 모든 모델 파라미터를 최신으로 자주 보정해줘야 함
    - 뉴스 정보의 노이즈를 제거하기 위한 노력이 필요함

## 6.2. Generalizability and limitations
- 위의 결과를 실무로 옮길 때, 일반화가 가능한지 고려해야 함
- 본 논문은 트레이딩 시그널을 해석하는 것 보다, 해당 전략이 실제 이익을 얼마나 가져올 수 있는지를 비교하고자 함
- (우선 패스)

# 7. Conclusion and outlook
- 금융 시장이 금융 공시 발표에 매우 민감하기 때문에, 실시간 시장 데이터와 심리 분석을 결합해서 투자 결정을 하고자 함
- **<u>이에 본 논문은 뉴스 기반 거래 알고리즘을 평가하고, 구체적인 트레이딩 학습을 통해, 유의미한 수익률을 거둘 수 있음을 입증함</u>**
