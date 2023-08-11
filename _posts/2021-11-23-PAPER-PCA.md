---
layout: post
title:  "[PAPER] A Tutorial on Principal Component Analysis"
author: jaealways
categories: [ paper, data science, linear algebra ]
tags: [paper]
---


# [PAPER] A Tutorial on Principal Component Analysis

Jonathon Shlens 분의 "A Tutorial on Principal Component Analysis"(2014) 논문을 참고하여 본문의 내용을 구성하였습니다.

[reference: Jonathon Shlens, "A Tutorial on Principal Component Analysis", 2014](https://arxiv.org/pdf/1404.1100.pdf)









---

# 0. Abstract





Principal Component Analysis(PCA)는 차원을 축소하는 방법론 중 하나이다. 본 논문에선 PCA를 SVD와 비교해 설명하고자 한다.





# 1. Introduction



PCA는 간단하게 파라미터가 필요없이 중요한 정보를 추출하는 방법론이다. 아주 조금의 노력으로 복잡한 데이터를 낮은 차원으로 치환할 수 있다. 본 논문은 PCA의 여러 수학적 증명에 대해 다루고, PCA가 왜 SVD와 관련 있는지 이해하고자 한다.





# 2. Motivation: A Toy Example





일상 생활의 여러 실험에서, 우리의 데이터는 불분명하고 노이즈도 섞여있다.

이번 챕터에선 Figure1과 같이 여러 개의 카메라를 통해 공을 촬영하는 실험을 가정한다.

![https://blog.kakaocdn.net/dn/duZvwk/btrVzvkfkPW/1vMll6UeNRETVoXtpzpA40/img.png](https://blog.kakaocdn.net/dn/duZvwk/btrVzvkfkPW/1vMll6UeNRETVoXtpzpA40/img.png)

Figure 1. A toy example

카메라 A, B, C는 위와 같이 다른 모양으로 촬영될 것이다. 우리는 어떤 차원과 축이 중요한지 전혀 모르기 때문에, 공의 위치에 대해 3차원 공간의 촬영을 하기로 했다. 하지만, 우리는 어디가 정말 x축, y축, z축인지 알 수 없기 때문에, 세 군데의 카메라의 포지션을 각각 벡터 a, b, c로 했다.

과연 우리는 x축에 대한 간단한 방정식으로 공의 움직임을 나타낼 수 있을까?

우리가 조금 더 지식이 많았다면, 카메라 하나를 x축에 대해 측정했을 수 있다. 하지만, 현실에선 많은 것들이 미지수이고, 따라서 우린 때론 많은 차원으로 데이터를 기록할 필요가 있다. 또 현실의 데이터는 많은 노이즈를 가지고 있다.





# 3. Framework: Change of basis

PCA의 목적은 데이터셋을 가장 의미있는 basis로 다시 표현하는 것이다. 우리의 희망사항은 노이즈와 숨겨진 구조(가령, "Figure1의 예시에서 x축을 따라 움직임이 진행된다"와 같이)를 발견하는 것이다. 즉, 이 때 x축 기저벡터가 중요하고, 차원을 줄인다는 것은 어떤 것이 중요하고, 노이즈인지 판단한다는 의미이다.





## 3.1. A naive basis

차원을 줄이려는 우리의 목표를 달성하기 위해, 먼저 우리의 데이터에 대해 정확히 정의할 필요가 있다. 우리의 데이터는 보통 여러 측정값을 포함하고, 하나의 샘플을 m 차원 벡터로 나타낼 수 있다. (이 때 m은 측정단위이다.) 또 모든 샘플은 어떤 orthonormal basis에서 span했으며, 모든 측정치 벡터는 기저 벡터와 선형결합을 이룰 것이다.



이 때 orthonormal basis는 무엇인가?



FIg1의 실험에서 카메라 A를 기준으로 봤을 때, orthonormal basis는 {(1,0),(0,1)}일 수 있다. 이는 우리의 측정 단위 상 (2,2)는 두 번 위로 두 번 좌측으로 측정된 값이기 때문이다. 초기의 기저가 우리 데이터의 측정에 영향을 미친다는 뜻이다.



각각의 차원에 대한 orthonormal basis vector를 결합하면 matrix B와 같이 단위행렬로 만들 수 있다.





## 3.2. Change of basis



이제 우리는 PCA가 데이터셋을 더 잘 나타내는 또 다른 기저가 있는지 찾는 과정임을 알았다. 이런 PCA에선 다른 기저들로 인해 발생할 수 있는 문제를 해결해줄, 선형성이라는 아주 강력한 가정을 하고 있다.



X가 원래 데이터셋이고, 6x72000 차원으로 이루어졌다고 해보자. Y는 같은 차원의 또 다른 행렬이다. 이 때 우리는 선형변환을 통해 PX=Y로 나타낼 수 있다. 이 때, Y는 X와 다른 기저를 갖게 되고,

$$
PX=Y
$$

yi의 각각의 coefficient는 pi과 xi의 내적의 벡터로 나타낼 수 있다. 즉, $y_i$는 {p1~pm}까지 기저의 정사영의 결과이고, P가 X를 represent하는 새로운 기저벡터가 된다.



## 3.3. Questions Remaining

선형성을 가정하므로 인해, 적절한 기저를 찾는데 여러 문제를 줄일 수 있었다. 여기서 몇 가지 질문이 생길 수 있다.</br>

(1) X를 다시 표현하는 가장 적절한 방법이 뭔가?</br>

(2) 가장 잘 고른 기저 P는 무엇인가?</br>

이는 Y에서 어떤 특징들을 뽑아낼 것인지 고민하면 찾을 수 있는 대답으로, 아래 챕터에서 다루고자 한다.</br>

# 4. Variance and the goal</br>

데이터를 가장 잘 표현한다는게 무슨 의미인가?</br></br>

## 4.1. Noise and rotation</br>

어떤 데이터셋이든 노이즈는 작아야한다. 그렇지 않으면 signal에 대한 정보가 추출되기 힘들다. 노이즈에 대한 절대적인 척도는 없지만, 노이즈는 시그널의 세기와 밀접한 관련이 있다. 가장 대표적인 방법은 signal-to-noise-ratio(SNR)이다.</br>

SNR=~</br>

1 보다 높은 SNR은 높은 적중률을 가진 측정법이라는 의미이고, 낮은 SNR은 데이터에 노이즈가 크다는 의미이다.</br>

![https://blog.kakaocdn.net/dn/wRLOp/btrVzUcQwGn/gLpf3B2wi1KM4ZIntDnfgK/img.png](https://blog.kakaocdn.net/dn/wRLOp/btrVzUcQwGn/gLpf3B2wi1KM4ZIntDnfgK/img.png)

Figure 2. Simulated data of (x,y) for camera A.</br>

위의 figure 2에서, 가장 분산이 큰 방향은 (xA, yA)의 기저에 따르지 않고, 가장 잘 맞는 선을 따른다. 위 실험에서 스프링이 직선으로 움직여도, 각자 카메라의 각도에 의해 노이즈는 발생한다. signal과 noise에 따른 분산은 cloud가 얼마나 얇은지로 판정된다. 가느다란 선으로 수렴할 경우 SNR은 1 보다 크고, 원에 수렴할 경우 SNR=1이 된다.</br>

우리는 우리가 찾는 기저가 naive basis가 아니라 가정한다. 따라서 분산을 극대화하는 방향으로 회전함으로 적절한 naive basis를 찾고자 한다.</br></br>

## 4.2. Redundancy</br>

![https://blog.kakaocdn.net/dn/tpFal/btrVA1i3gQM/VjEXRrKDcXo6PCdiP6S7qK/img.png](https://blog.kakaocdn.net/dn/tpFal/btrVA1i3gQM/VjEXRrKDcXo6PCdiP6S7qK/img.png)

FIG 3. A spectrum of possible redundancies in data from the two separate measurements r1 and r2.</br>

FIG 3에서 왼쪽의 그림은 한 축이 다른 한 축을 예측하는데 도움을 주지 못하기 때문에 uncorrelated하다. 반면, 오른쪽은 매우 중복된(?) 측정으로, highly correlated하다.</br>

가장 오른쪽의 사례의 경우, 하나의 측정값(가령, r1)이 다른 측정값(r2)을 잘 추측하기 때문에, 하나의 변수만으로도 데이터를 표현할 수 있다. 이것이 차원 축소를 관통하는 핵심 아이디어이다.</br></br>

## 4.3. Covariance Matrix</br>

두 개의 변수에 대해선 가장 잘 적합되는 선의 기울기를 찾아서 적합의 정도를 판단하면 된다. 이것이 수없이 높은 차원일 때는 어떻게 응용할 수 있을까?</br>

평균이 0인 두 세트를 가정했을 때,</br>

$$
A=\{a_1,a_2,...,a_n\}\\B=\{b_1,b_2,...,b_n\}
$$

A와 B의 분산은 다음과 같이 정의된다.</br>

sigma A sqr=~

$$
\sigma_A^2=\frac{1}{n}\sum_ia_i^2\\\sigma_B^2=\frac{1}{n}\sum_ib_i^2
$$

이 때 A와 B의 공분산은 아래와 같다.</br>

$$
\sigma_{AB}^2=\frac{1}{n}\sum_ia_ib_i
$$

공분산은 두 변수 간의 선형관계의 정도를 측정한다. 매우 큰 양수값을 갖는 경우, 높은 상관관계를 갖는다는 의미이다. 절대값이 높으면 중복도(?)가 높다는 것이다.</br>

$$
a=[a_1,a_2,...a_n]\\b=[b_1,b_2,...b_n]
$$

A와 B를 위처럼 row vector로 치환하면, dot product 연산으로 나타낼 수 있다.</br>

$$
\sigma_{ab}^2=\frac{1}{n}ab^T
$$

a와 b를 각각 x1과 x2, 새로 추가된 row vector x3,...,xm을 합쳐서 matrix X라 한다.</br>

이 때, X의 각각의 row는 특정 형태의 측정치를 의미하고, 각각의 column은 하나의 시도에서 측정된 결과값들을 의미한다. 따라서 X의 covariance는 다음과 같다,</br>

$$
C_X\equiv\frac{1}{n}XX^T
$$

Cx는 모든 가능한 측정치에 대한 공분산을 포착한다. 이 때 절대값이 높을수록, 높은 중복도를 갖는다는 의미이다. 우리가 공분산 행렬 Cx에서 어떤 특징을 최적화시킬 수 있을까?</br></br>

## 4.4. Diagonalize the covariance matrix</br>

우리의 목표는</br>

(1) 공분산의 절대값으로 나타나는 중복도를 최소화하는 것

(2) 공분산으로 나타나는 signal을 최대화하는 것.

이다.</br>

최적화된 공분산 행렬 Cy에선 다음과 같은 점을 기대할 수 있을 것이다.

(1) Cy는 diagonal matrix가 된다

(2) Y가 rank 순으로 정렬된다</br>

Cy를 대각행렬로 만드는 많은 방법 중, PCA는 모든 기저벡터가 orthonormal하다고 가정해서, 가</br>장 쉬운 편이다. 즉, PCA는 분산을 가장 크게 만드는 축을 기저로 하도록 회전하는 방법론이다.

이 때 orthonormality 조건에 의해, 찾으려는 모든 방향은 이전과 orthogonal한 방향으로 제한된다.

이렇게 선택된 p는 principal components이다.</br></br>



## 4.5. Summary of Assumptions</br>

본 챕터에선 PCA 이면에 있는 중요한 가정에 대해 언급했다.</br>

(1) 선형성</br>

선형성의 가정은 기저를 바꾸는 문제로 PCA를 바꾼다.</br>

(2) 큰 분산이 중요한 구조를 갖고 있다</br>

분산이 높고 SNR이 높아야 차원을 줄이기 좋은 구조이다.</br>

(3) principal components는 orthogonal하다</br>

orthogonal 가정에 의해 PCA를 선형대수분해로 풀 수 있게 한다.</br>

PCA를 푸는데 두가지 방법이 있는데, 첫번째는 직관적이고, 두번째는 대수적인 분해를 해야한다.</br></br>

# 5. Solving PCA using eigenvector decomposition</br>

PCA를 푸는 첫번째 방법은 eigenvector decomposition을 이용하는 것이다. 즉, Y=PX인 상황에서 orthonormal matrix P를 찾는 것이다.</br>

Cy=1/n YYT</br>

$$
\begin{align*}C_Y&=\frac{1}{n}YY^T\\&=\frac{1}{n}(PX)(PX)^T\\&=\frac{1}{n}PXX^TP^T\\&=P(\frac{1}{n}XX^T)P^T\\&=PC_XP^T\end{align*} 
$$

이 때 Cy는 다음과 같이 치환할 수 있다.

우리는 symmetric matrix A가 그것의 eigenvector의 orthogonal matrix로 diagonalized 될 수 있는지 알아보고자 한다.

우리는 아래와 같이 Cy를 증명할 수 있다.

$$
\begin{align*}C_Y&=PC_XP^T\\&=P(E^TDE)P^T\\&=P(P^TDP)P^T\\&=(PP^T)D(PP^T)\\&=(PP^{-1})D(PP^{-1})\\&=D\end{align*} 
$$

P를 고르는 것이 Cy를 diagonalize 한다는 것이 밝혀졌다. PCA의 결과를 행렬 P와 Cy로 나타내면</br>

(1) X의 principal components는 Cx의 eigenvector이다.</br>

(2) i번째 diagonal value Cy는 X의 분산이다.</br>

데이터셋 X에 대한 PCA는 (1) 각각의 측정치에 대한 평균을 빼고 (2) Cx의 eigenvector를 계산하는 과정을 수반한다.</br></br>

# 6. A more general solution using SVD</br>

이번 챕터에선 PCA가 Singular Value Decomposition과 유사한 것을 바탕으로, 다른 솔루션을 제안하려 한다. SVD는 기저를 바꾸는데 조금 더 일반화된 방법론이다.</br></br>

## 6.1. Singular Value Decomposition</br>

몇 가지 수식에 대해 정의하고자 한다.</br>

/math은 XtX에 대한 orthnormal eigenvector다. 즉,

(XtX)~

와 같이 나타낼 수 있다.</br>

sigma i sqrt(lambda i)는 singular values이다.</br>

{u1, u2, ..., ur}은 ui=1/sigma~이다.</br>

이는

ui*uj=~

||Xvi||~</br>

SIngular value decomposition은

Xvi=sigma ui</br></br>

## 6.2. Interpreting SVD</br></br>

## 6.3. SVD and PCA</br></br>

# 7. Discussion</br></br>

## 7.1. Limits of statistics of dimensional reduction</br></br>
