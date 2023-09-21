---
layout: post
title:  "[PAPER] Are Cash Flows Better Stock Return Predictors Than Profits?"
author: jaealways
categories: [ paper, data science, quant ]
tags: [paper]
---


# [PAPER] Are Cash Flows Better Stock Return Predictors Than Profits? 

Stephen Foerster et al 분의 "Are Cash Flows Better Stock Return Predictors Than Profits?"(2017) 논문을 참고하여 본문의 내용을 구성하였습니다.

[reference: Stephen Foerster et al, "Are Cash Flows Better Stock Return Predictors Than Profits?", 2017](https://deliverypdf.ssrn.com/delivery.php?ID=975094099100006027070070069079112028118048034028083053113065073025103006067105094087000019119031026058055083030124109002019083027027021084004066066116010006034044002080112070117010087115086116001064069002092074070093029089082120065028111076004&EXT=pdf&INDEX=TRUE)


# 0. Abstract

- 간접현금흐름법을 직접적인 추정치로 변환하여 포트폴리오를 구성한 결과, 현금흐름이 높은 주식이 아닌 주식보다 10%의 이상 우수한 결과를 거둠

# 1. Introduction

- 투자자들은 평균 수익의 단면을 예측하기 위해, 재무정보에 의존
- 파마 프렌치 및 다른 모델들을 통해 주식 수익을 더 잘 예측하는 재무 정보에 대한 탐색이 계속 됨
- 기존 GAAP가 많은 방식으로 기존 재무제표를 표시하기 떄문에, 회계 정보가 표시되는 방식과 기업의 근본적인 경제적 결과 사이의 관계를 이해하기 힘듬
- 재무제표 간의 일치성이 떨어지기 때문에, 표준화된 탬플릿을 통해 수익 가능성을 더 잘 이해함을 보이고자 함
</br></br>

```
Direct cash flow vs Indirect cash flow


Direct: Income Statement 중 현금흐름 관련 항목을 모두 나열 후 구함. 학계에서 이론적으로 권장하는 방법

Indirect: Income Statement의 마지막 항목인 Net Income으로 시작해서 각 현금흐름을 구하는 방법으로, 간단해서 실무에서 주로 사용

아래의 

```
![Alt text](Image\2023-09-20-Are-Cash-Flows-Better-Stock-Return-Predictors-Than-Profits\image.png)



- 직접 재무제표를 권장하는 국제회계기준(IFRS), US GAAP와 달리, 대다수의 회사들은 간접 현금흐름법을 사용
- 이로 인해 operating cash flow가 거의 포함되지 않아, 미래 현금흐름을 공통된 방식으로 모형화할 수 없음
- 투자자들은 free cash flow to equity(FCFE) 같은 근사치를 사용해서 이런 이슈를 해결하려 하는데, 상관관계가 45%에 불과
</br></br>

- 본 연구는 불규칙적인 비영업 활동 또는 자본조달 현금흐름 보다, (영업)기술 기반 활동으로 현금을 조달한 것에 관심 있음
- 직접현금흐름이 우수하게 미국 주식시장을 예측할 수 있다고 생각해 실증 테스트
- 구체적으로 영업이 적은 자본지출에서 발생한 현금흐름을 financing과 taxes를 조절해서 새로운 지표 
- 새로운 현금흐름 비율과 high-low 포트폴리오로 측정된 미래 주식 수익률, 정보비율, 위험 조정 알파 속에서 유의미한 양의 상관관계 발견
- 본 논문에선 영업 현금흐름 정보 외에, 현금, 세금, 자본지출 등의 정보가 점진적인 예측력을 제공해준다는 사실을 보이고자 함

</br></br>

# 2. The Direct Cash Flow Template 

- 회계학의 깊은 내용을 다루는 것 같아 패스하겠습니다.

# 3. Data and Methodology

- 

# 4. Information in Cash Flow Components



# 5. Cash Flow, Profitability Return and Yield Measures, and OneMonth-Ahead Returns 



## 5.1 Portfolio-Sorted Regressions


# 6. Robustness Checks 

## 6.1 Extended-Horizon Returns

## 6.2 Sector Analysis

# 7. Conclusion
