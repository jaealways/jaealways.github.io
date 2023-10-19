---
layout: post
title:  "[PAPER] The predictive power of singular value decomposition
entropy for stock market dynamics"
author: jaealways
categories: [ paper, data science, quant ]
tags: [paper]
---


# [PAPER] The predictive power of singular value decomposition entropy for stock market dynamics

Petre Caraiani 분의 "The predictive power of singular value decomposition entropy for stock market dynamics"(2014) 논문을 참고하여 본문의 내용을 구성하였습니다.

[reference: Petre Caraiani, "The predictive power of singular value decomposition entropy for stock market dynamics", 2014](https://www.sciencedirect.com/science/article/abs/pii/S0378437113008212)


# 1. Introduction

- 금융위기로 인해 금융 시장을 더 다이나믹하게 이해할 수 있는 도구가 필요함을 느낌
- 최근 연구들은 주식 간의 상관관계를 통해 시장의 상태를 분석


# 2. Methodology

## 2.1. Correlation matrices of stocks

- 금융 시계열로부터 상관관계를 구하는 표준적인 방법은 서로 다른 주식 사이의 Pearson 상관 관계를 사용하는 것

$$R_{i,j}=\frac{mean\ of\ [(y_i-\bar{y_i})-(y_j-\bar{y_j})]}{\sigma_i\sigma_j}$$

## 2.2  Singular value decomposition

- SVD를 통해 아래처럼 행렬을 분해할 수 있음
$$A=USV^T$$

## 2.3 Entropy

- 엔트로피를 복잡성 지표로 사용할 수도 있음
$$Ent=-\sum \lambda_k ln(\lambda_k) $$

## 2.4 Granger causality tests

- 통계적으로 상관관계를 판별하는 Granger Tests는 특정 시리즈를 통해 미래 다이나믹스를 예측할 수 있음

$$y_t=\beta_0+\sum_{k=1}^{N}\beta_ky_{t-k}+\sum_{l=1}^{N}\alpha_ly_{t-l}+u_t$$

- Granger test는 어떤 알파라도 0에서 상당히 다른지 검정을 통해, 귀무가설 기각 여부를 F 검정을 통해 결정
    - $H_0: \alpha_l=0$ for any l
    - $H_1: \alpha_l\neq0$ for at least l
$$F=\frac{(RSS_v-RSS_u)/l}{RSS_u/(N-2l-1)}$$
- F 통계량이 크면, x가 y의 원인이라는 귀무가설이 기각됨
- 최적의 lag는 Bayesian Information Criterion(BIC)를 통해 결정되는데, 모델의 MLE를 $L_n(k)$라 하면 BIC 값을 다음과 같이 구할 수 있음
$$c_n(k)=-\frac{2ln(L_n(k))}{n}+\frac{kln(n)}{n}$$


# 3. Results
## 3.1 Data used

- 다우존스 산업평균지수를 데이터로 사용하는데, 일간 월간 데이터를 모두 민감도 체크에 사용
- 일부 데이터가 누락된 몇 개 종목의 경우 목록에서 제거

## 3.2 Correlation-based matrices

- Pearson 상관계수를 통해 상관 행렬을 구성했는데, 0.3의 threshold를 통해 중요하지 않은 값들은 제거
- 결과 행렬의 엔트로피가 시간 흐름에 따라 갖는 변화를 지켜보기 위해, 2년 기간의 월별 24개 관측치 슬라이딩 윈도우 사용

## 3.3. Singular value decomposition entropy

- 각각의 correlation 행렬마다 SVD를 진행했는데, 벡터가 엔트로피 측정치로 변환됨
- 일별 엔트로피와 달별 엔트로피가 각각 Fig1과 Fig2를 나타내는데, 엔트로피의 변화가 시장 자체의 무질서를 나타냄


![Fig1](/_posts/Image/2023-08-27-The predictive-power-of-singular-value-decomposition-entropy-for-stock-market-dynamics/image.png)

- 다우존스 지수가 엔트로피 지수 보다 더 변동성이 큰데, 시스템 인덱스가 덜 요동친다고 생각할 수 있음

## 3.4. Granger causality for entropy

- 위의 그림들은 주식시장의 상태를 나타낸다고 생각할 수 있는데, 좀 더 확실히 하기 위해 실제 시장 역학의 예측력을 갖고 있는지 검증하고자 함
- 엔트로피 지수와 다우존스 지수(월별 일별)의 Granger causality 검정을 통해 

