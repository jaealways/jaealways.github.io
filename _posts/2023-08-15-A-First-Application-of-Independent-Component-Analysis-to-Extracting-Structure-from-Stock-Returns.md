---
layout: post
title:  "[PAPER] A First Application of Independent Component Analysis to Extracting Structure from Stock Returns "
author: jaealways
categories: [ paper, data science, quant ]
tags: [paper]
---


# [PAPER] A First Application of Independent Component Analysis to Extracting Structure from Stock Returns 

Andrew D. Back, Andreas S. Weigend 분의 "A First Application of Independent Component Analysis to Extracting Structure from Stock Returns"(1997) 논문을 참고하여 본문의 내용을 구성하였습니다.

[reference: Andrew D. Back, Andreas S. Weigend, "A First Application of Independent Component Analysis to Extracting Structure from Stock Returns", 1997](https://archive.nyu.edu/bitstream/2451/14180/1/Is-97-22.pdf)

# 0. Abstract

- 주식 수익률을 Independent Component Analysis (ICA) 방법론을 통해 분해하고자 함
- 추정된 IC는 1) 드물지만 큰 충격, 2) 자주 있는 작은 변화 두 가지로 나눌 수 있음
- 전반적인 주가를 가중치 IC를 사용해서 재구성하는데, PC를 사용했을 때보다 더 높은 성능을 보임

# 1. Introduction
- ICA를 사용하기 위해, 관측된 다변량 시계열(일일 수익률)이 독립된 시계열(주식시장 등)로부터 반영된 것이라 가정
- ICA는 또 독립성분을 추출하고자 하는데, 아웃풋의 결합확률과 marginal probability의 KL-divergence를 최소화하여 찾을 수 있음
- IC를 찾는 것은 결합확률을 분해하는 방향, 가장 적은 공통정보를 갖는 방향, 보다 흥미로운 방향을 찾는 것
- ICA와 PCA는 component를 통해 선형변환을 한다는 공통점
- PCA는 상관관계가 없는 주성분을 얻으려 하고 분산이 중요하지만, ICA는 통계적으로 독립적인 component를 얻고자 함
- PCA는 2차통계량까지만 사용하지만, ICA는 더 높은 통계량을 사용할 수 있음
</br></br>


# 2. ICA in General
## 2.1 Independent Component Analysis
- ICA는 Input vector $S$로부터 신호 x를 가져와서 독립적인 벡터 세트 y를 추출하는 과정
- 다항변수 시계열을 관측한 후, mixing process를 통해 $Z_i(t)= \SUM _{j=1}^n$
