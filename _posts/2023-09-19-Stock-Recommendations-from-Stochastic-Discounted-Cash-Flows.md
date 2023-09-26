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
    - Section 3: 분석에 사용된 데이터베이스아 변수에 대해 설명
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

- 이 때, t 시점의 NOPAT는 아래의 관계식으로 가정. 즉, NOPAT는 세전이익, 세금, 감가상각 EBITDA로부터 구할 수 있음 ($\tau_0$는 한계세율)

$${NOPAT}_t=(EBITDA-D\&A)(1-\tau_0)$$

- 이 때 아래 두 가정이 필요함
    - 자본비용 k를 동일하다고 가정하며, 이를 통해 미래 현금흐름 할인. 자본비용은 자산비용, 세후 부채, 우선주비용의 가중평균으로 계산
    - $T>0$, 영구성장률 g를 $0<g<k$로 가정
- 때문에 미래 실현 현금흐름은 아래의 식으로 나타남
$$ \tilde{V_0}(\omega)=\sum_{t=1}^T\frac{CF_t(\omega)}{(1+k)^t}+\frac{CF_T(\omega)(1+g)}{(1+k)^T(k-g)} $$

- 위의 모델은 현금흐름을 신뢰성 있게 추정하는게 중요하기 떄문에, 

</br></br>
# 3. Data and Sample Selection Criteria

- SDCF 모델은 일반적인 고려사항에 외존하지만, 세부사항을 구현하고 검증하기 위해선 특정 회사의 데이터를 활용해야 함
- 본 섹션에선 방법론의 개발 및 테스트를 위한 분기별 데이터 소스를 살펴보고자 함

- Thomson Reuters Eikon Datastream 데이터베이스에서 필요한 데이터를 수집했는데, 2009년 1월부터 2017년 12월까지 S&P 500에 포함된 모든 비금융 기업으로 구성됨
</br></br>

![Alt text](Image\2023-09-19-Stock-Recommendations-from-Stochastic-Discounted-Cash-Flows\image.png)
</br></br>

- $k$: 회사의 자본 조달 비용 추정치
- $\tau_0$: 회사들의 한계세율
- discount rate for the cash flow terminal value: 
</br></br>

# 4. Recommendations from Fair Vaule Distributions

- 2절에서 정의한 공정가치 분포를 통해 mispricing 기업을 식별하는데, 적정가로 회귀할 것이라는 가설하에 매수매도 진행
- 강매수(SB), 매수(B), 보유(H), 매도(S), 매도(SS)로 분류

## 4.1 Single-Stock Quantile Recommendations System

- $FV_t^i$를 특정 회사의 i 시점 공정가치 분포함수라 하고, $P_t^i$를 시장가라 가정
- $q_t^i=FV_{th}^i(P_{th}^i)$ 값을 0.125, 0.25, 0.75, 1을 기준으로 다섯 단계(SB, B, H, S, SS)로 구분

- 이런 분류법을 Single-Stock Quantile(SSQ)라 하는데, 기업 공정가치 분포를 최대한 활용할 수 있지만, SB나 SS 같은 극단값 라벨을 비어있을 수도.

## 4.2 Cross-Sectional Quantile Recommendations System

- 모든 기업의 공정가치 분포를 사용해서, mispricing을 계산할 수 있음
- $\mu_t^i$와 $\sigma_t^i$는 t 시점 주식 i의 공정가치 로그분포 평균과 분산
- t 시점 기업 i의 mispricing 지표는 $z_t^i$ 로그 공정가치 기대값에 로그 가격 차이를 로그 공정가치 분포의 표준편차로 나눔
- mispricing 지표에 따라 alpha 퀀타일로 나타내고, 각각 다른 신호, 이런 시스템을 Section Squartial Quantile(CSQ)라고 함
- CSQ는 전반적인 시장변화는 신호에 영향을 미치지 않는다는 장점이 있음
</br></br>
- SSQ와 CSQ의 성능을 테스트하기 위해, 최초에 모든 회사에 동일한 가중치를 부여한 후, 매일 시그널을 갱신해서 일일 수익률을 계산
</br></br>

# 5. Performance Evaluation

