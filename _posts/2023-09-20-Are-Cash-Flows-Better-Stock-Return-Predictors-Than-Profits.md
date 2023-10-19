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
![Alt text](/_posts/Image/2023-09-20-Are-Cash-Flows-Better-Stock-Return-Predictors-Than-Profits/image.png)



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
</br></br>

# 3. Data and Methodology

- S&P 1500(시총 상위 500 종목, 중형주 400 종목, 소형주 1500 종목)의 펀더멘탈, 가격 데이터를 획득
- 생존 편향을 피하기 위해 리스트를 매달 갱신
- 대부분의 회계 지표 관련 연구처럼 레버리지 같은 구조적 차이에 의해 금융회사(은행, 보험, 리츠 등)를 제거함(?). 전체의 약 15% 정도
- 94년 10월 ~13년 12월까지의 데이터 사용
- 업계 관행에 따라 기본적 변수에 대해 TTM(직전 12개월 실적으로 순이익 산정) 사용
    - (ex: TTM P/E가 100이면, 직전 네 개 분기 순이익 대비 주가가 100배)
- Table1을 기본으로, 직접 간접 현금흐름 지표를 만듦
    - CFAFAT: (C)-Capex
    - CFAF: (B)-Capex
    - CFO: (A)-Capex
- 위의 직접현금흐름(DMCF) 지표를 간접현금흐름(CFIM, OANCF-Capex) 지표와 비교
- 그 외에 굉장히 많은 지표들이 존재
</br></br>


# 4. Information in Cash Flow Components

- 다양한 현금흐름 구성요소가 주식 수익률에 대한 정보를 추가로 제공하는지 분석

![Alt text](/_posts/Image/2023-09-20-Are-Cash-Flows-Better-Stock-Return-Predictors-Than-Profits/Table2.png)

- 우선 회사 간 동일 가중치의 다양한 현금흐름 측정치의 correlation을 측정
- 이 때 지표는 아래와 같음
    - CFODM: 영업활동으로 현금흐름을 측정하는 직접 방법
    - CFOIM: 영업활동으로 현금흐름을 측정하는 간접 방법
    - CFONM: Novy-Marx's의 free 현금흐름 지표(순수익+감가상각/상각-운영자본변화-자본지출)
    - FinAct: financing activities
    - TaxAct: 세금
    - OthAct: 다른 활동
    - Capex: 자본지출
- 위 지표들을 total assets(TA)이나 market value of equity(MVE)로 나눠줌
- CFODM/TA,CFOIM/TA 간 상관관계는 0.81인데, CFODM/TA, CFONM/TA를 측정하면 0.45까지 감소
- 때문에 현금흐름 구성요소들에 대해 보다 엄격한 분석을 할 필요
</br></br>
- 추가적으로, Fama and MacBeth cross-section regression을 진행했는데, 결과는 아래와 같음



![Alt text](/_posts/Image/2023-09-20-Are-Cash-Flows-Better-Stock-Return-Predictors-Than-Profits/Table3.png)

- 

# 5. Cash Flow, Profitability Return and Yield Measures, and One Month-Ahead Returns 

![Alt text](/_posts/Image/2023-09-20-Are-Cash-Flows-Better-Stock-Return-Predictors-Than-Profits/Table4.png)

- 

## 5.1 Portfolio-Sorted Regressions

- 

# 6. Robustness Checks 

## 6.1 Extended-Horizon Returns

## 6.2 Sector Analysis

# 7. Conclusion

- 일반적인 손익계산서 지표가 의미가 있지만, 현금기반 측정치가 보다 우수함을 밝힘
- 보다 자유로운 현금흐름을 추정하기 위해 직접법을 사용
- 다양한 보유기간과 부문을 통제하는 강건성 측정 후에도 유사한 결과