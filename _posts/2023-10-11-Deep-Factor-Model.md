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
- **<u>딥러닝을 기반으로 다양한 요인을 사용해서 수익률 모델을 구현한 후, LRP를 통해 어떤 팩터가 예측에 기여하는지 판단</u>**
- 위의 Deep Factor Model을 통해 실증 분석 후, 우수한 성능을 가짐을 입증하고자 함 



# 1. Introduction

- 멀티 팩터 모델은 Ross의 Arbitrage Pricing Theory(APT) 모형에서 출발했는데, 실제로는 파마 프렌치 접근이나 BARRA 접근법이 널리 사용되고 있음
- 위의 접근들은 주식 수익률이 공통 요인에 의해 설명된다고 가정하기 때문에, 이를 횡단면 회귀분석을 통해 계산
- 멀티 팩터 모델은 크게 수익률 향상과 위험 통제 목적에서 사용됨
- **<u>팩터가 수익률에 미치는 영향을 구할 수 있다면, 좋은 팩터를 선별에 포트폴리오를 구성해 높은 수익률을 달성할 수 있음. (수익률 모델)</u>**
- **<u>또 주요 주식 수익률 간의 상관관계를 계산해서 위험을 분산시키는데 사용할 수도 있음. (리스크 모델)</u>**
</br></br>
- 수익률 모델과 리스크 모델 모두 선형 관계를 통해 수익률과 팩터를 설명하고자 함.
- **<u>선형 모형은 굉장히 유용하지만, 금융 시장의 복잡성을 충분히 반영하지 못한다는 한계</u>**
- 이에 본 논문은 비선형 관계를 표현할 수 있는 딥러닝 모델을 사용하려 하지만, 딥러닝 모델은 해석력이 떨어진다는 단점이 있음
- 수익률 모델로 다양한 팩터를 통해 주식 수익률을 예측하고, 예측된 수익률을 리스크 모델처럼 속성에 따라 분해하려 함
- **<u>Layer-wise relevance propagation(이하 LRP)은 입력의 기여도를 계산하는 방법으로, 어떤 요인이 예측에 기여했는지 살펴보고자 함 (이를 deep fator model이라 칭함)</u>**
- 본 논문에선 일본 주식시장을 통해 실증분석을 실시해서 deep factor model이 어떻게 우수한지 설명하고자 함


# 2. Related Works

- 주식 수익률을 예측하는 것은 중요한 이슈로 수 백개의 선행 연구와 요인을 통해 설명하고자 시도했음
- 오늘날에 가장 유용한 팩터인 Value, Size, Momentum을 통해 수 십 년간 연구가 지속됨
- 특히 파마 프렌치 모델을 통해 미국 주식 시장을 market(CAPM에 기반), size(기업 사이즈에 따라 구분), value(book-to-market) 팩터로 구분하려는 시도가 가장 유명
- 최근에는 5요인 모형이 더 활발히 연구되고 있음.
</br></br>
- 투자자는 어떤 팩터를 통해 수익률을 예측할지 방법을 선택해야 하는데, 그 중 머신러닝 기반으로 시도한 선행 연구들이 많이 있음
- **<u>선행 연구를 통해 딥 뉴럴 네트워크가 성능이 좋음을 밝힘. 하지만, 선행 연구들은 리스크 모델로의 관점이 부족함.</u>**


# 3. Methodology – Deep Factor Model

## 3.1 Deep Learning


- 딥러닝은 가장 낮은 레벨에서 인풋 데이터를 받고, 여러 레이어를 거치며 학습해서, 최상위에서 결과를 출력함.
- 대략적인 구조는 아래와 같음. 이 때 Notation은 아래와 같음
    - $l$: 1~L까지 레이어 인덱스. L은 레이어의 깊이를 나타냄
    - $z^{(l)}$: Layer l의 hidden features
    - $W^{(l)}$: Layer l에서 가중치 행렬
    - $Y$: 마지막 아웃풋
    - $b^{(l)}$: bias term(threshold)

$$ z^{(1)}=f^{(1)}(W^{(0)}X+b^{(0)}) \\ z^{(2)}=f^{(2)}(W^{(1)}z^{(1)}+b^{(1)})  \\...\\ Y=f^{(L)}(W^{(L-1)}z^{(L-1)}+b^{(L-1)}) $$

- 레이어 아웃풋을 보통 sigmoid나 ReLU 활성화함수를 통해 변환함


## 3.2 Layer-Wise Relevance Propagation

- LRP는 네트워크를 예측으로부터 이전 노드의 기여도를 역산하는 방법임.
- 보존 법칙(layer 별 Relevance socre가 일정함)에 의해 LRP는 각 벡터 요소를 만족하는 Relevance Score $R_d$를 찾고자 함

$$ f(x)=\sum_{d\in V(L)} R_d^{(L)}=...=\sum_{d\in V(l)} R_d^{(l)}=...=\sum_{d\in V(1)} R_d^{(1)} $$


![Alt text](/_posts/Image/2023-10-11-Deep-Factor-Model/Figure1.png)

- 또 한 뉴런에서 다른 뉴런으로 들어올 때와 나갈 때 같은 relevance score가 보존되는데, 아래 식처럼 계산할 수 있음

$$ R_i^{l}=\sum_j \frac{z_{ij}^{(l,l+1)}}{\sum_k z_{kj}^{(l,l+1)}}R_j^{(l+1)}, z_{ij}^{(l,l+1)}=w_{ij}z_i^{(l)} $$


## 3.3 Deep Factor Model

- 본 논문에서는 딥러닝의 비선형 관계를 통해 수익률과 리스크를 통합된 방식으로 표현하고자 함
- 전통적인 멀티 팩터 모델은 아래와 같은 선형 모델로 수익률을 예측했음
    - $r_i$: 주식 수익률
    - $F_i$: 주식 i의 팩터 집합
    - $X_{iN}$: 팩터 n의 exposure
    - $\alpha_i$: risk free rate를 나타내는 APT에서 절편
    - $\epsilon_i$: random term (평균 0이고, 주식 수익률 간 상관관계 없음)

$$ r_i=\alpha_i+X_{i1}F_1+...+X_{iN}F_N+\epsilon_i $$

- 선형 멀티 팩터 모형은 포트폴리오 분석과 투자 관리에서 매우 유용하지만, 선형성이라는 엄격한 가정을 기반으로 함
- **<u>각 팩터는 수익률에 대해 독립이어야 하고, 수익률이 팩터 익스포져에 따라 제한 없이 커질 수 있음</u>**
</br></br>
- 금융시장의 복잡성을 고려했을 떄, 비선형 관계로 팩터와 수익률을 나타내는 것이 더 적합할 수 있음
- 비선형 모델에서 익스포져와 팩터 간 관계식은 아래와 같이 나타낼 수 있음. 이 때 $\tilde{f}$는 비선형함수

$$ r_i=\tilde{f}(X_{i1},...X_{iN},F_1,...,F_N)+\epsilon_i $$

- 선행 연구에 의하면 팩터 추정치를 역사적 팩터 평균 $\bar{F}_n$으로 대체할 수 있고, 이 때 팩터값은 상수로 바뀜. 아래 식처럼 변환할 수 있음

$$ r_i=\tilde{f}(X_{i1},...,X_{iN},\bar{F_1},...,\bar{F_N})+\epsilon_i \\
= f(X_{i1},...,X_{iN})+\epsilon_i $$

- $X_{in}$는 모델을 통해 예측하고자 하는 팩터 익스포져임. 딥러닝 모델을 통해 팩터 익스포져와 미래 주식 수익률 간의 관계를 밝히고자 함
- 하지만, 딥러닝은 모델의 투명성과 해석력이 떨어진다는 치명적인 단점이있기 때문에, **<u>LRP를 통해 예측된 수익률을 리스크 모델처럼 분해하려 함.</u>**
- LRP를 개별 주식이나 포트폴리오 퀀타일에 적용해서, 어떤 팩터가 예측에 기여했는지 판단할 수 있음.

![Alt text](/_posts/Image/2023-10-11-Deep-Factor-Model/Figure2.png)


# 4. Experiment on Japanese Stock Markets

## 4.1 Data

- 도쿄 거래소의 인덱스인 TOPIX 데이터셋을 사용.
- 1990년 12월부터 2016년 3월까지 수집된 월별 5개의 팩터와 17개의 팩터 익스포져로 구성됨

![Alt text](/_posts/Image/2023-10-11-Deep-Factor-Model/Table1.png)

## 4.2 Model

- Table3처럼 훈련 데이터를 가짐.

![Alt text](/_posts/Image/2023-10-11-Deep-Factor-Model/Table3.png)


- 선형 회귀를 베이스라인으로 삼고, 아래 모델들을 통해 성능을 비교하고자 함

![Alt text](/_posts/Image/2023-10-11-Deep-Factor-Model/Table2.png)

- 최근 5년 간의 60 셋의 데이터를 통해 모델을 학습시켰고, 새로운 월 데이터가 들어올 때 마다 학습이 갱신됨.
- 이를 통해 과거 06년 ~ 16년까지 120개월의 결과를 예측하고자 함. 각 모델의 효과를 검증하기 위해 해당 모델과 분위수 모델의 확률을 비교했음
- 롱숏 전략을 통해 net-zero 투자를 했는데, 분위수 포트폴리오와 같은 가중치로 주식을 매수 매도 함.
- 연평균 수익률, risk, 샤프 비율을 구했고, MAE, RMSE를 통해 예측 정확성을 측정하고자 했음

## 4.3 Results

- 아래 처럼 **<u>deep factor 모델이 전반적으로 좋은 성능 보임. 이를 통해 금융시장에서 수익률과 팩터의 관계가 비선형적임을 추측할 수 있음</u>**

![Alt text](/_posts/Image/2023-10-11-Deep-Factor-Model/Table4.png)


## 4.4 Interpretation

- 아래 Figure3은 LRP를 사용해 예측에 기여한 팩터를 백분율로 나타냄.

![Alt text](Image/2023-10-11-Deep-Factor-Model/Figure3.png)


- **<u>수익률과 분위수 포트폴리오 양쪽에서 quality, value factor가 절반 이상을 차지함</u>**
- 모멘텀 팩터는 그닥 효과가 없지만, 밸류 팩터는 어느 정도 효과를 보임
</br></br>
- LRP를 통해 리스크 모델을 아래 Table5처럼 검증함

![Alt text](/_posts/Image/2023-10-11-Deep-Factor-Model/Table5.png)

- 각 팩터 수익률과 Descriptors 간의 평균 상관계수를 계산함
- **<u>밸류와 사이즈 팩터는 LRP와 상관계수에서 큰 차이를 보이는데, 밸류 팩터는 LRP에서 큰 비중이었지만, 상관계수에선 작은 비중</u>**
- 사이즈 팩터는 반대로 LRP에서 작지만 상관계수에선 큰 비중.
- 때문에 상관계수를 통해 수익률 팩터에 대한 해석을 보정해야 함


# 5. Conclusion

- 딥러닝 기반의 모델을 통해 종목 선택과 리스크 분해를 한 결과 아래의 결론 내림
    - **<u>딥러닝 팩터가 선형 모델의 성능을 능가함. 금융시장에서 팩터와 수익률 간의 관계를 비선형이라 할 수 있음</u>**
    - **<u>얕은 모델이 정확도가 더 높지만, 깊은 모델은 더 높은 수익 거둠</u>**
    - **<u>LRP를 통해 어떤 팩터가 예측에 기여했는지 판단할 수 있음</u>**
- 위의 결과를 통해 RNN을 바탕으로 한 팩터 모델로 확장하고자 함

