---
layout: post
title:  "[PAPER] Stock Recommendations from Stochastic Discounted Cash Flows"
author: jaealways
categories: [ paper, data science, quant ]
tags: [paper]
---


# [PAPER] Stock Recommendations from Stochastic Discounted Cash Flows

Giulio Bottazzi et al 분의 "Stock Recommendations from Stochastic Discounted Cash Flows"(2021) 논문을 참고하여 본문의 내용을 구성하였습니다.

[reference: Giulio Bottazzi et al, "Stock Recommendations from Stochastic Discounted Cash Flows", 2021](https://deliverypdf.ssrn.com/delivery.php?ID=677119021086099003107004113112120088127008049065074002106109012027029105077120086073029003016045000030051090126087017114113080057042094035072064001004122066086122031089032086024031106116075084082002093085093098090116124028013090083084122064102106106013&EXT=pdf&INDEX=TRUE)


# 0. Abstract

- 기존 현금흐름할인법을 확장해서 확률적 특성을 적용한 두 가지 주식 추천 방법론을 제시
    - single-stock quantile recommedation system: 시장가와 공정가치를 통해 추정된 분포를 비교해서 mispricing 측정
    - cross-sectional quantile system: 모든 회사의 공정가치 분포를 통해 mispricing 측정
- 통계검정을 통해 해당 방법론이 지속적인 초과수익을 보임을 밝힘
</br></br>
# 1. Introduction

- 노이즈와 불확실성으로 인해 현금흐름 분석을 정확하게 예측하는 것은 어려움
- 떄문에 증권 분석을 통해 정확한 고유가치를 추정하기 보다, 매수 매도를 정당화하기 위해 과소과대평가를 진행
- 본 논문에서는 stochastic discounted cash flow(이하 SDCF) 분석을 통해 실제 수익을 거둘 수 있는지 살펴보고자 함
- SDCF는 미래 현금흐름의 불확실성을 고려해서, 공정가치분포와 시장가격을 비교. 이를 통해 mispricing을 탐지.</br></br>


- single stock quantile system에선 해당 기업의 공정가치분포를 통해 기업의 mispricing 정도와 방향 지정
- cross-sectional quantile system에선 모든 기업의 공정가치분포를 통해 mispricing을 비교적으로 도출
    - mispricing 지표를 시장가와 공정가치 기대값의 차이를 표준편차로 표준화해서 구함
</br></br>
- 두 시스템의 성능분석은 Fama-French 3요소 모델에 모멘텀 팩터를 더해 수행
- 가장 많이 추천된 주식으로 포트폴리오를 구성했을 때, 지속적으로 양의 수익률을 달성함을 확인

- 본 논문은 아래와 같은 순서로 전개
    - Section 2: SDCF에 대한 설명
    - Section 3: 분석에 사용된 데이터베이스아 벼수에 대해 설명
    - Section 4: 두 주식 추천 시스템 소개
    - Section 5: 추천 시스템 성능 분석
    - Section 6: 결론

</br></br>
# 2. The stochastic discounted cash flow (SDCF) approach

- SDCF는 미래 기업 성과의 불확실성을 고려해서 공정가치 분포를 계산. (자세한 것은 Bottazzi et all(2020) 논문 참고)
- 그 중 부채 현금흐름을 측정하기 어렵기 때문에, 무상현금흐름 접근을 사용

## 2.1 unlevered free cash flow approach

- 자산의 현재가치 $V_0$를 현재 기업가치 $\tilde{V_0}$에서 현재 부채 가치를 뺴서 구함
    - $TD$: total debt
    - $C_sI$: cash and short-term investments
    - $MI$: minority interest
    - $PS$: preferred stocks

$$V_0=\tilde{V_0}-(TD-C_sI+MI+PS)$$

- 무상현금흐름 접근을 통해 기업 현재가치는 부채에 대한 추정치 없이 구할 수 있음
- t 시점에서 정의된 미래 무상현금흐름 추정치는 아래와 같이 정의
    - $NOPAT$: net operating profit after tax
    - $D\&A$: depreciation and amortization (감가상각과 상각)
    - $\Delta WC$: change in the working capital

$${CF}_t:={NOPAT}_i+D\&A_t-{CAPEX}_t-\Delta WC_t$$

- 


