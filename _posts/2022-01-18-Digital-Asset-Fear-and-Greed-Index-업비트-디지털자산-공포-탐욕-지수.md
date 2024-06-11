---
layout: post
title:  "[PAPER] Digital Asset Fear and Greed Index, 업비트 디지털자산 공포-탐욕 지수"
author: jaealways
categories: [PAPER, Quant]
tags: [Risk, Crypto]
---


본 게시글은 두나무 Datavalue 팀에서 발간한 [디지털자산 공포-탐욕 지수 Methodology Book](https://datavalue.dunamu.com/static/pdf/%EB%91%90%EB%82%98%EB%AC%B4%20%EB%94%94%EC%A7%80%ED%84%B8%EC%9E%90%EC%82%B0%20%EA%B3%B5%ED%8F%AC-%ED%83%90%EC%9A%95%20%EC%A7%80%EC%88%98%20Methodology%20Book%202.0.pdf)을 바탕으로 작성한 내용임을 밝힙니다.

코드 보기: https://github.com/jaealways/fear-and-greed

<br><br>


## 1. 공포-탐욕 지수의 정의

자본시장에서 공포 또는 탐욕에 의한 투자는 장기적으로 건전한 시장을 조성하는데 방해가 됩니다. 본 지표는 이에 현재 시장에서 일어나는 거래가 공포와 탐욕의 스펙트럼 중 어디에 위치하는지 보여주고, 투자자로 하여금 이성적 판단을 할 수 있도록 도와줍니다. 공포/탐욕에 대한 과도한 판단은 투자자에게 지표에 대한 신뢰를 잃게 하므로, 본 지표는 공포와 탐욕이 어떤 상황인지 명확히 정의할 필요가 있습니다.
<br><br>

![image](https://github.com/jaealways/must-read-paper-CV-daily/assets/71856506/1a5463cf-9b32-4380-b7ca-65da4d57be9e)

Figure 1


디지털자산 시장에서 냉정한 판단이 필요한 시점은 거래량과 변동성이 폭발할 때입니다. 가격상승으로 이어지면 탐욕, 가격하락으로 이어지면 공포에 물들었다 할 수 있습니다. 이에 본 공포탐욕 지수는 다음 3가지를 주요지표로 삼습니다.

**1. 거래량이 높은/낮은 편인가?**

**2. 변동성이 높은/낮은 편인가?**

**3. 가격이 상승/하락하는가?**

위 3가지 기준에 따라 다음과 같은 판단을 할 수 있습니다.


**공포: 거래량 매우 증가, 변동성 매우 증가, 가격 하락**

**중립: 양 극단 사이**

**탐욕: 거래량 매우 증가, 변동성 매우 증가, 가격 상승**
<br><br>

![image](https://github.com/jaealways/must-read-paper-CV-daily/assets/71856506/305917b8-e118-48bb-96d6-5ca13ab416ad)
Figure 2


하지만 좀 더 정확히 말하자면, 위의 그래프처럼 공포-탐욕 점수와 자산가격의 방향은 정비례하지 않습니다. 투자자는 한계효용을 가지므로, 가령 어느 자산의 가격이 오늘 10%, 내일 20% 상승하여도, 둘의 탐욕 정도는 두 배 차이가 아니라 비슷한 수준이라는 의미입니다. 본 공포탐욕지수는 위에 열거한 사항을 수치화해서 최종 점수로 도출합니다.


## 2. 계산과정
 

### 2.1. 변동성-거래량 점수

위의 Figure1에서 Y축을 수치화하기 위해, 거래량과 변동성을 하나의 점수로 측정합니다.

![image](https://github.com/jaealways/must-read-paper-CV-daily/assets/71856506/8dbca278-44c0-4710-a68e-f306bee06bb7)

그 중 우선 변동성(σ)은 위와 같이 특정 일자까지의 지수가중이동평균(EWMA)을 사용하여 특정 일자 보다 추세의 흐름을 보다 반영해줍니다.

![image](https://github.com/jaealways/must-read-paper-CV-daily/assets/71856506/8ad03590-fe33-4c2a-a01a-e2695875b705)

특정 시점의 변동성이 높은지 낮은지 판단하기 위해 365일치를 기준으로 자연로그를 취해줘서 각 일자에 해당하는 변동성 점수를 계산합니다. 이 때 변동성점수가 극도로 커지거나 작아지는 것을 막기 위해 [-4, 4] 폐구간을 둡니다.

![image](https://github.com/jaealways/must-read-paper-CV-daily/assets/71856506/fd9098ea-93ea-47ca-941c-1fc50adae6ad)
 
특정 시점의 거래량이 높은지 낮은지 측정하기 위해 거래량도 마찬가지로 지수가중이동평균을 반영해줍니다. 이 때 장기(60일)와 단기(20일)를 동시에 반영하여 보다 정확한 추세적 흐름을 파악합니다. 나머지는 변동성 때와 동일합니다.

![image](https://github.com/jaealways/must-read-paper-CV-daily/assets/71856506/c397a670-2d60-49f0-932a-6dc645d7ee4c)

이렇게 구한 변동성 점수와 거래량 점수를 취합하여 변동성-거래량 점수를 계산합니다. 변동성-거래량 점수가 높을수록 Figure1의 Y축은 큰 값이 됩니다.
<br><br>


### 2.2. 모멘텀 점수

![image](https://github.com/jaealways/must-read-paper-CV-daily/assets/71856506/68c9ccfa-c4f7-4d52-8527-ce60c54b3c0e)

변동성-거래량이 높은 구간은 급등/급락이 많이 이루어집니다. 이를 명확히 반영하여 공포탐욕지수를 계산하려면, 변동성-거래량 점수가 높은 구간은 단기 추세를 높게 반영해야 합니다. 이에 모멘텀 점수를 통해 장단기 가중치(L)를 조절하여 현재 모멘텀을 점수화합니다.

![image](https://github.com/jaealways/must-read-paper-CV-daily/assets/71856506/f14b4aa6-0e94-4cf1-85b6-e4921b7b63d6)

![image](https://github.com/jaealways/must-read-paper-CV-daily/assets/71856506/e9aada28-d6fe-4f95-9c95-c722dafa44df)

특정 시점 자산의 이동 방향(세기가 포함된)을 구하기 위해 지수가중이동평균을 구합니다. 이 때, 장기(30일, 약 한달)와 단기(7일, 1주일) 이격도(주가가 이평선과 얼마나 떨어져있는지 측정, X)를 구해 장기와 단기를 모두 반영할 수 있게 모델링합니다.


### 2.3. 공포-탐욕 지수

![image](https://github.com/jaealways/must-read-paper-CV-daily/assets/71856506/d8039d7c-d33f-4568-83c7-4369451e020a)

