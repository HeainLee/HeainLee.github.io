---
title: "데이터 분석을 위한 기초 수학"
date: 2020-07-20
permalink: /dev/post11
tags : data-science math
categories: machine-learning
---


## 데이터 분석가에게 요구되는 기초적인 수학 능력에 대하여

예측 모델을 작성하거나 데이터 시각화를 위한 좋은 패키지들이 많다. 그 덕분에 누구나 모델을 구축하거나 데이터 시각화를 할 수 있다.    
그러나 최적의 성능으로 신뢰할 수 있는 모델을 생성하기 위해서는 패키지에만 의존할 수 없고, 수학에 대한 배경 지식이 있어야 한다.    
또한 모델을 해석하고, 데이터 기반의 의사 결정을 하고, 의미있는 결론을 도출하는 일은 또 다른 얘기다.(모두 수학적 해석이 요구되는 일들이다.)   
이러한 패키지를 사용하기 전에 각 패키지의 수학적 기초를 이해하고 이러한 패키지를 단순히 블랙 박스 도구로 사용하지 않는 것이 중요하다.    


### 데이터 과학 및 머신러닝을 위한 기초적인 수학

--- 
- **통계와 확률**

> 통계 및 확률은 변수 시각화, 데이터 전처리, 피쳐 변환, 데이터 대체, 차원 축소, 피쳐 엔지니어링, 모델 평가 등에 사용된다.

1. 평균(mean), 중앙값(median), 최빈값(mode), 표준편차(standard deviation), 분산(variance)
2. 상관계수(Correlation coefficient), 공분산 행렬(covariance matrix)
3. 확률 분포(Probability distributions) - Binomial, Poisson, Normal, etc ...
4. 유의 확률(p-value)
5. 베이즈 정리(Bayes' theorem) <br>
    양성 예측도(Positive Predictive Value), 음성 예측도(Negative Predictive Value)
    정밀도(Precision, 예측 값 중에 실제 맞춘 확률), 재현율(Recall, 실제 값 중에 예측한 확률), ROC/AUC
6. 중심극한정리(Central Limit Theorem)
7. 결정계수(R2 score)
8. 평균제곱오차(Mean Square Error, MSE)
9. A/B Testing, 몬테카를로 방법(Monte Carlo Simulation) <br>

--- 

- **다변량 미적분**

> 대부분의 머신러닝 모델은 여러 개의 변수가 있는 데이터셋을 사용하게 된다.    
> 따라서 다변량 미적분학에 대한 지식은 모델을 구축하는 데 매우 중요하다.

1. Functions of several variables (다변수 함수)
2. Derivatives and gradients (도함수와 기울기)
3. Step function, Sigmoid function, Logit function, ReLU function
4. Cost function
5. Plotting of functions
6. Minimum and Maximum values of a function <br>
--- 

- **선형대수학**

> 선형대수학은 머신러닝에서 가장 중요한 수학 스킬이다. 데이터셋은 많은 경우 행렬로 표현될 수 있다.    
> 선형대수학은 데이터 전처리, 데이터 변형, 차원 축소, 모델 평가 등에 사용된다.

1. 벡터(Vectors), Norm of a vector
2. 행렬(Matrices), Transpose of a matrix
3. The inverse of a matrix; The determinant of a matrix; Trace of a Matrix;
4. 내적(Dot product), 고유값(Eigenvalues), 고유벡터(Eigenvectors) <br>

--- 

- **최적화 방법론**

> 대부분의 머신러닝 알고리즘은 목적함수를 최소화하여 예측 모델링을 수행한다.    
> 예측된 레이블을 얻기 위해 테스트 데이터에 적용해야하는 가중치를 학습한다.

1. 비용함수(Cost function)
2. 목적함수(Objective function)
2. 가능도 함수(Likelihood function) 
4. 오차함수(Error function)
5. 경사하강법과 그 변형들(Gradient Descent Algorithm) <br>

--- 

<br>
 
여기까지는 어디까지나 기초적인 수학적 스킬을 몇 가지 나열한 것이다.    
수학적/이론적 기초는 신뢰할 수있는 모델을 구축하는 데 매우 중요하다.    
따라서 각 머신 러닝 알고리즘의 수학적 이론을 연구하기에 충분한 시간을 투자한다.    
특히 딥러닝 모델링을 위해서 행렬, 미적분을 좀 더 깊게 이해할 필요가 있다.    
프로그래밍 스킬만으로는 데이터 분석가가 될 수 없다는 얘기를 '갑자기' 남기고 싶어서 포스팅해봤다.   

아 물론 이 포스팅은 직접 쓴게 아니고 번역한 것이다. 
출처는 [여기](https://medium.com/towards-artificial-intelligence/how-much-math-do-i-need-in-data-science-d05d83f8cb19) 미디엄 글이다.  
