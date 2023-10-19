---
layout: post
title:  "[PAPER] An Automated Framework for Incorporating News into Stock Trading Strategies"
author: jaealways
categories: [ paper, data science, quant, NLP]
tags: [paper]
---


# [PAPER] An Automated Framework for Incorporating News into Stock Trading Strategies

Wijnand Nuij et al 분의 "An Automated Framework for Incorporating News into Stock Trading Strategies"(2013) 논문을 참고하여 본문의 내용을 구성하였습니다.

[reference: Wijnand Nuij et al, "An Automated Framework for Incorporating News into Stock Trading Strategies", 2013](https://www.frederikhogenboom.nl/papers/tkde14-ganews.pdf)


# 0. Abstract
- 주식거래에서 뉴스를 자동으로 활용할 수 있는 프레임워크에 대해 제시
- 기술적 지표와 뉴스 변수를 결합해서 전략을 구성하고, 뉴스 변수를 통해 뉴스의 부가가치를 나타냄.

# 1. INTRODUCTION
- 금융 시장의 주요 정보인 뉴스를 통해 의사결정하는 과정은 어려움
- 뉴스가 투자에 중요한 영향을 미치기 때문에, 뉴스 정보 처리를 자동화하려는 시도들이 있었음
- 선행연구들이 10-K 보고서의 토론 섹션부터 재무 뉴스, 월스트리트 저널 칼럼 등 다양한 출처를 사용한만큼, 다양한 텍스트 데이터가 활용될 수 있음
- 뉴스 텍스트 정보처리를 다음과 같은 과정으로 자동화하려 함
    - 뉴스 메시지 텍스트에서 관련 이벤트와 엔티티(개체: 가령 goldman sachs(company), Powell(person) 등) 추출
    - 이벤트 간의 영향을 연관시킴
    - 거래 전략에서 뉴스 이벤트 활용
- 이벤트가 어떤 영향을 미치는지 정의하면, 기술적 지표 외에 펀더멘탈 지표와도 결합하여 (유전 프로그래밍을 통해 트리로 재구성하여) 사용할 수 있음
- 본 논문은 뉴스와 주식시장의 정량적 분석을 하고, 이를 기술지표와 결합한 프레임워크에 대해 논함

# 2. RELATED WORK
- 뉴스와 주식시장의 관계를 고려할 때 아래 측면들을 고려함
    - 뉴스 발표와 금융 시장 사이에 관계가 존재하는가?
    - 뉴스 이벤트가 금융 시장에 미치는 영향을 정량화할 수 있나?
    - 뉴스 정보와 금융시장 사이의 관계가 사소하지 않은가?
- **<u>다양한 선행 연구들을 통해 뉴스 메시지의 이벤트는 주가와 거래량에 중요한 영향을 미친다는 것을 확인</u>**
- 본 논문은 뉴스 발표를 통해 관련된 이벤트를 추출해서 거래 규칙에 포함하는데 초점


# 3. A PRELIMINARY ANALYSIS OF THE RELATIONSHIP BETWEEN NEWS AND THE STOCK MARKET

## 3.1 Event Information Extraction
- 뉴스에서 이벤트를 추출하기 위해선, 사전에 정의된 이벤트 집합과 관련 엔티티가 필요함
- ViewerPro라는 툴을 사용해서, 전처리 과정 거침

## 3.2 Descriptive Statistics of the Dataset
- 역사적 주가 데이터와 관련 회사의 뉴스 데이터를 분석에 사용(2007.01.01 ~ 2007.04.30)
- 2008년 8월 1일 FTSE 350 주가 지수에 포함된 모든 350개 회사로 구성
- 뉴스 데이터는 해당 모든 종목의 Reuter 뉴스 피드를 통해 수집(5157개 이벤트 정의)
- 중복된 이벤트를 제거하고, 0.5% 비중 미만의 뉴스를 제거하여 총 2112개 이벤트로 줄어듬</br></br>
![Alt text](/_posts/Image/2023-09-27-An-Automated-Framework-for-Incorporating-News-into-Stock-Trading-Strategies/Figure1.png)

## 3.3 Relationship between News and Share Prices
- 한 종목 수익률은 아래와 같이 구함
$$r_i=\frac{P_{i+n}-P_i}{P_i}\times100$$
- 같은 종류의 여러 이벤트가 다른 일(N) 동안 일어나면
$$R_i=\frac{\Sigma_{j=1}^N r_j}{N}$$
- 시장 수익률을 고려해서 종목의 초과수익률($a_i$)을 아래와 같이 정의. 이 때 $r_i^I$는 벤치마크로 사용되는 인덱스 수익률
$$a_i=r_i-r_i^I$$ 
- 여러 날의 초과 수익률을 다음과 같이 정의
$$A_i=\frac{\Sigma_{j=1}^N a_j}{N}$$
- 본 챕터에선 FTSE 350 지수를 벤치마크 삼았고, 자세한 결과는 다음과 같은 과정으로 이루어짐. 결과는 아래 Table1과 같음
    - 각 이벤트가 공개되고 1,2,5,10일 후의 수익률과 이벤트 발생 당일의 절대수익률(R0), 초과수익률(A0) 계산
    - 각 이벤트가 수익률의 방향과 일치하는 퍼센트를 계산(d)
    - 각 이벤트의 수익률 t검정 결과(p)

![Alt text](/_posts/Image/2023-09-27-An-Automated-Framework-for-Incorporating-News-into-Stock-Trading-Strategies/Table1.png)

- 위 테이블의 결과를 해석할 때 아래 세 가지를 주목해야 함
    - 여러 이벤트가 수익률에 영향을 미쳤겠지만, 리스트에 있는 이벤트가 큰 비중을 차지했다고 가정
    - 이벤트가 공개되기 전에 이미 시장이 반응했을 수 있기 때문에, 이벤트 전날 종가를 이벤트에 반영
    - 어떤 이벤트가 주가에 미치는 영향을 평가할 때, 다른 상호작용을 고려하지 않음
- 양의 수익률을 기록하는 이벤트라도, 실제론 다른 이벤트와 동시에 발생하기 때문에, 항상 양의 수익률이 나오지는 않음
- **<u>R0, R1, R2 등 단기수익률을 고려할 때, SharesUp, SharesDown, RatingUp 이벤트가 유의미한 수익률 보임</u>**
- **<u>R5, R10 등 장기수익률을 고려하면, SharesUp, RatingUp, RatingPositive, ProfitUp, SharesDown이 유의미한 수익률 보임</u>**
- **<u>대부분의 이벤트는 긴 시간이 지나도 영향이 관측됨을 확인함</u>**
- 초과 단기 수익률(A0,A1,A2)에선 rating up, shares up, acquisition start, shares down 이벤트가, 초과 장기 수익률(A5,A10)에선 rating up, shares up, rating positive, price target raised, profit up, shares down 이벤트가 유의미한 결과 보임

- 사전에 정의된 impact와 절대수익률(초과수익률) 간의 상관관계는 아래 Table2와 같음
</br></br>
![Alt text](/_posts/Image/2023-09-27-An-Automated-Framework-for-Incorporating-News-into-Stock-Trading-Strategies/Table2.png)
</br></br>
- 본 챕터의 결론은 다음과 같음
    - 뉴스에서 추출된 이벤트를 거래전략에 사용할 수 있으며, 유의미한 양의 수익률을 기록
    - 이벤트의 (사전에 정의된) impact와 수익률 간의 유의미한 상관관계

# 4. TECHNICAL TRADING
- 본 챕터에선 거래전략에 사용할 기술 지표에 대해 설명하고자 함

## 4.1 Simple Moving Average
- 지난 20일 간의 주식 평균을 나타내는 단순이동평균
$$M_i=\frac{\Sigma_{i=1}^NP_i}{N}$$
- 20일 이평선 위로 주가가 돌파하면 매수, 아래로 돌파하면 매도

## 4.2 Bollinger Bands
- 볼린저 밴드는 주가가 정규분포를 따른다는 가정하에, 이동평균을 바탕으로 두 밴드를 만듦. (recursive한 움직임에 따라 다시 평균으로 회귀할 것이기 때문에) 아래 밴드(L)에 닿으면 매수, 위 밴드(U)에 닿으면 매도하는 전략
$$L=M-2\times\sigma_M \\ U=M+2\times\sigma_M$$

## 4.3 Exponential Moving Average
- 지수이동평균은 최근의 가중치를 더 주는 방법으로, 단기를 5일, 장기를 20일로 설정
$$E_i=\frac{2}{N+1}\times(P_i-E_{i-1})+E_{i-1}$$
- 장기이평선 위로 단기 이평선이 돌파하면 매수, 아래로 돌파하면 매도

## 4.4 Rate of Change
- Rate of Change(RoC)는 변화율에 기반한 방법으로, RoC가 0 위에서 감소하면 피크에 도달했기 때문에 매도. 반대로 0 밑에서 증가하면 매수
$$C_i=\frac{P_i-P_{i-10}}{P_{i-10}}$$

## 4.5 Momentum
- RoC를 사용해서 모멘텀을 측정하는데, RoC가 0을 상승돌파하면 매수, 하락돌파하면 매도

## 4.6 Moving Average Convergence Divergence
- MACD는 12일 26일 지수이동평균의 차이를 구한 값, MACD가 0을 상승돌파하면 (12일 지수이동평균 값이 높기 때문에 최근에 상승세) 매수, 하락돌파하면 매도
$$D_i=E[12]_i-E[26]_i$$

## 4.7 Performance of Technical Trading Indicators
- 아래 Table3을 통해 각 기술지표가 생성한 수익률 확인할 수 있음. 단순이동평균과 지수이동평균이 좋은 성능 보임.</br></br>
![Alt text](/_posts/Image/2023-09-27-An-Automated-Framework-for-Incorporating-News-into-Stock-Trading-Strategies/Table3.png)

# 5. A NEWS-BASED TRADING FRAMEWORK
- 유전 프로그래밍(초기값을 로직에 따라 변형해서 후대 유전자를 만들어 변화가 없을 때까지 해당 과정을 반복)을 통해 뉴스를 주식 거래에 활용하는 프레임워크를 구축
- 아래 Figure2처럼 트리 기반으로 AND, OR 연산자를 넣어 거리 신호를 발생시킴
</br></br>
![Alt text](/_posts/Image/2023-09-27-An-Automated-Framework-for-Incorporating-News-into-Stock-Trading-Strategies/Figure2.png)
</br></br>
- 구체적인 알고리즘은 아래와 같고, **<u>기술적 지표의 모수를 변경시키는 방법으로 알고리즘 학습시킴</u>**
</br></br>

```
Algorithm 1 Genetic programming.

Require: α ≥ 0: minimum improvement
        β > 0: maximum times of no improvement
        γ > 0: population size
        0 < ρ ≤ γ: number of parents
        0 ≤ µ ≤ 1: mutation probability

π = generateRandomPopulation(γ)
σ_old = −∞, σ_new = calcFitness(π), b = 0
while b < β do
    addIndividual(π′, getBest(π, σnew))
    while |π′| < |π| do
        θ = selectRandomParents(π, σnew, ρ)
        ϑ = crossOver(θ)
        ϑ′ = mutate(ϑ, µ)
        addIndividual(π′, ϑ′)
    end while
    π = π′, σold = σnew, σnew = calcFitness(π)
    if σnew − σold ≤ α then
        b = b + 1
    else if b > 0 then
        b = 0
    end if
end while
return π
```
- 아래 Figure3을 통해 프레임워크를 한 눈에 정리
</br></br>
![Alt text](/_posts/Image/2023-09-27-An-Automated-Framework-for-Incorporating-News-into-Stock-Trading-Strategies/Figure3.png)
</br></br>

# 6. EXPERIMENTS AND RESULTS
- 본 챕터에선 위의 프레임워크에 대한 검증을 하고, 결과에 대해 말하고자 함

## 6.1 Performance of Individual Events
- 뉴스 데이터를 통해 생성된 시그널의 수익률은 아래 Table4와 같음.
</br></br>
![Alt text](/_posts/Image/2023-09-27-An-Automated-Framework-for-Incorporating-News-into-Stock-Trading-Strategies/Table4.png)
</br></br>
## 6.2 News and Technical Indicators
- **<u>뉴스와 기술지표 각각이 좋은 성능을 보였기 때문에, 이 둘을 결합하여 더 개선된 성과를 얻었음(Table5)</u>**

</br></br>
![Alt text](/_posts/Image/2023-09-27-An-Automated-Framework-for-Incorporating-News-into-Stock-Trading-Strategies/Table5.png)
</br></br>

## 6.3 Optimal Trading Strategies
- 유전 프로그램을 통해 무작위로 생성한 50개의 거래 규칙을 15세대에 거쳐 모수를 변형한 결과는 Table6과 같음
</br></br>
![Alt text](/_posts/Image/2023-09-27-An-Automated-Framework-for-Incorporating-News-into-Stock-Trading-Strategies/Table6.png)
</br></br>
- 결과를 검증하기 위해 2010.05.01~2010.09.30의 기간에 **<u>S&P500 데이터로 실험을 수행했을 때, 많은 부분에서 FTSE 데이터와 유사함.</u>**
</br></br>
![Alt text](/_posts/Image/2023-09-27-An-Automated-Framework-for-Incorporating-News-into-Stock-Trading-Strategies/Table7.png)
</br></br>

# 7. PRACTICAL CONSIDERATIONS
- 위의 기술을 새로운 도메인에 적용할 때는, 뉴스에서 이벤트 추출, 이벤트 기반으로 거래 규칙을 생성하는 다양한 단계를 고려해야 함
- 도메인에 맞게 알고리즘의 규칙이나 추출 방법을 다르게 할 필요

# 8. CONCLUSIONS AND FUTURE WORK
- 본 논문은 뉴스를 주식 거래 전략에 접목하기 위해, 이벤트를 추출해서 전문가가 영향을 할당해서 뉴스를 정량화시킴
- 뉴스 지표를 수익률과 상관관계가 있음을 확인하고, 여러 기술지표와 결합한 전략을 통해 높은 수익률을 거둠.
    - **<u>최적화된 거래 전략엔 뉴스 정보가 반영됨</u>**
    - **<u>뉴스 이벤트를 포함한 전략은 양의 수익률을 창출함</u>**
- 뉴스 메세지를 보다 세밀하게 분석하거나, 동일한 날에 발생한 사건 간의 상호작용을 고려하면 더 깊은 이해를 이끌 수 있어보임.