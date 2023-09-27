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

</br></br>
# 1. Introdiction

- 시장이 효율적인지는 정보 이용가능성에 의해 좌우되는데, 뉴스 정보는 실제 주식 시장에 크게 영향을 미치기 때문에 시장 효율성에 중요함
- 선행 연구를 통해 뉴스와 주식시장 사이를 연결했지만, 구체적으로 어떻게 투자 의사 결정을 용이하게 하는지는 미지수
- 본 논문은 뉴스 감성이 **<u>실제 뉴스기반 트레이딩에 어떻게 활용될 수 있는지</u>** 말하고자 함</br></br>

- 텍스트 마이닝 기법이 발전함에 따라 뉴스 공급업체가 감성분석을 적극 도입했는데, 사용자가 트레이딩에 직접 사용하기 위해선 우위가 있어야 함
- 본 논문은 아래와 같은 방법으로 트레이딩을 위한 Decision Support System에 대해 살펴보고, 결과의 정확성을 평가하고자 함
    - 다양한 규칙기반 거래전략을 제시
    - 뉴스 기반 거래 시스템이 뉴스 기반 데이터에 유의미한 투자 결정을 낸다는 증거 발견
- ?? 3pg 마지막

</br></br>
# 2. Related work
- 뉴스 기반 거래 시스템의 구성에 대해 간단하게 설명하고자 함
## 2.1 Benchmarks
- 거래 전략의 성과를 검증하기 위해선, 주가 지수와 같은 벤치마크를 삼아야 함
## 2.2 Transaction fees
- 거래 비용이 고려되야 하지만, 많은 선행연구는 이를 무시하고 있음
- 
