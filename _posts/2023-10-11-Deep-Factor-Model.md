---
layout: post
title:  "[PAPER] Deep Factor Model"
author: jaealways
categories: [ paper, data science, quant]
tags: [paper]
---


# [PAPER] Deep Factor Model

Kei Nakagawa et al 분의 "Deep Factor Model"(2018) 논문을 참고하여 본문의 내용을 구성하였습니다.

[reference: Kei Nakagawa et al, "Deep Factor Model", 2018](https://arxiv.org/pdf/1810.01278.pdf)


# 0. Abstract

- 비선형 관계를 표현한다는 점에서 딥러닝은 강점을 가짐.
- 하지만, 모델의 해석 가능성에서 한계를 갖기 때문에, Layer-wise relevance propagation을 통해 모델의 해석가능성을 높이고자 함
- 딥러닝을 기반으로 다양한 요인을 사용해서 수익률 모델을 구현한 후, LRP를 통해 어떤 팩터가 예측에 기여하는지 판단
- 위의 Deep Factor Model을 통해 실증 분석 후, 우수한 성능을 가짐을 입증하고자 함 



# 1. Introduction

- 멀티 팩터 모델은 Ross의 Arbitrage Pricing Theory(APT) 모형에서 출발했는데, 실제로는 파마 프렌치 접근이나 BARRA 접근법이 널리 사용되고 있음
- 위의 접근들은 주식 수익률이 공통 요인에 의해 설명된다고 가정하기 때문에, 이를 횡단면 회귀분석을 통해 계산
- 멀티 팩터 모델은 크게 수익률 향상과 위험 통제 목적에서 사용됨
- 팩터가 수익률에 미치는 영향을 구할 수 있다면, 좋은 팩터를 선별에 포트폴리오를 구성해 높은 수익률을 달성할 수 있음. (수익률 모델)
- 또 주요 주식 수익률 간의 상관관계를 계산해서 위험을 분산시키는데 사용할 수도 있음. (리스크 모델)
</br></br>
- 수익률 모델과 리스크 모델 모두 선형 관계를 통해 수익률과 팩터를 설명하고자 함.
- 선형 모형은 굉장히 유용하지만, 금융 시장의 복잡성을 충분히 반영하지 못한다는 한계
- 이에 본 논문은 비선형 관계를 표현할 수 있는 딥러닝 모델을 사용하려 하지만, 딥러닝 모델은 해석력이 떨어진다는 단점이 있음
- 수익률 모델로 다양한 팩터를 통해 주식 수익률을 예측하고, 예측된 수익률을 리스크 모델처럼 속성에 따라 분해하려 함
- Layer-wise relevance propagation(이하 LRP)은 입력의 기여도를 계산하는 방법으로, 어떤 요인이 예측에 기여했는지 살펴보고자 함 (이를 deep fator model이라 칭함)
- 본 논문에선 일본 주식시장을 통해 실증분석을 실시해서 deep factor model이 어떻게 우수한지 설명하고자 함


# 2. Related Works

- 주식 수익률을 예측하는 것은 중요한 이슈로 수 백개의 선행 연구와 요인을 통해 설명하고자 시도했음
- 오늘날에 가장 유용한 팩터인 Value, Size, Momentum을 통해 수 십 년간 연구가 지속됨
- 특히 파마 프렌치 모델을 통해 미국 주식 시장을 market(CAPM에 기반), size(기업 사이즈에 따라 구분), value(book-to-market) 팩터로 구분하려는 시도가 가장 유명
- 최근에는 5요인 모형이 더 활발히 연구되고 있음.
</br></br>
- 투자자는 어떤 팩터를 통해 수익률을 예측할지 방법을 선택해야 하는데, 그 중 머신러닝 기반으로 시도한 선행 연구들이 많이 있음
- 선행 연구를 통해 딥 뉴럴 네트워크가 성능이 좋음을 밝힘. 하지만, 선행 연구들은 리스크 모델로의 관점이 부족함.


# 3. Methodology – Deep Factor Model
- 딥러닝은 가장 낮은 레벨에서 인풋 데이터를 받고, 여러 레이어를 거치며 학습해서, 최상위에서 결과를 출력함.
- 대략적인 구조는 아래와 같음. 이 때 Notation은 아래와 같음
    - $l$: 1~L까지 레이어 인덱스. L은 레이어의 깊이를 나타냄
    - $z^{(l)}$: Layer l의 hidden features
    - $W^{(l)}$: Layer l에서 가중치 행렬
    - $Y$: 마지막 아웃풋
    - $b^{(l)}$: bias term(threshold)

$$ z^{(1)}=f^{(1)}(W^{(0)}X+b^{(0)}) \\ z^{(2)}=f^{(2)}(W^{(1)}z^{(1)}+b^{(1)})  \\...\\ Y=f^{(L)}(W^{(L-1)}z^{(L-1)}+b^{(L-1)}) $$

- 


## 3.1 Deep Learning

## 3.2 Layer-Wise Relevance Propagation


## 3.3 Deep Factor Model


# 4. Experiment on Japanese Stock Markets



## 4.1 Data


## 4.2 Model



## 4.3 Results


## 4.4 Interpretation


# 5. Conclusion

