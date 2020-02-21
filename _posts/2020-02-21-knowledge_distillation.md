---
title: "Distilling the Knowledge in a Neural Network
date: 2020-02-21
permalink: /paper/
tags : deeplearning
categories: deeplearning
---

이 포스팅은 [이 블로그][이 블로그]에서 정리한 논문을 내가 읽으면서 정리한 내용이다.

### 01. Intro

딥러닝 모델은 개발 단계와 적용 단계에서 다음과 같은 차이가 있다. 

- 개발 단계 : 최대한 학습 모델의 정확도와 일반화 성능을 높여야 한다. 이를 위해 대규모 데이터 및 리소스의 사용이 비교적 자유롭다.
- 적용 단계 : 모델의 정확도와 일반화 성능을 유지하면서, 실시간 데이터 처리와 제한적인 리소스 사용을 고려해야 한다.


이와 관련해서 구글링해볼 수 있는 키워드는 'Knowledge Distillation'이며, Geoffrey Hinton 교수님이 2015년에 발표한 [논문][힌튼논문]이 자주 인용된다.

### 02. How to Distilling the Knowledge

검색되는 블로그들을 보다 보면 'teacher model'과 'student model'이라는 용어를 만난다.
논문에서는 , 
- 'teacher model'은 개발 단계에서 ~~**(데이터로부터 배워서) 잘**~~ 학습된 모델을 의미한다.
- 'student model'은 적용 단계에서 ~~**(선생님한테 배워서) 잘**~~  학습된 모델을 의미한다. 


'student model'의 학습은 'teacher model'로 부터 배운다는게 요지이다. 그러면 어떻게 선생님으로부터 학생을 가르칠 것인가? 논문에서는 **Model Compression**과 **Specialist Networks** 방법을 제안한다. 


#### 02-1. Model Compression

선생님이 배운 지식을 증류해서 학생에게 전달하기 위해서 사용되는 것이 **Softmax function** 이다. 

softmax function은 '모델이 각각의 클래스에 대해 어떻게 예측하고 있는지' 에 대한 정보를 가지고 있다. 보통 처음 CNN 분류 모델을 배우고 코딩하면 이 softmax function의 결과를 원-핫 인코딩처럼 최종 변환해서 클래스를 예측한다. 그래서 제일 "높은 값" == "모델이 예측한 값" 이라고 생각하는데, 이는 정확히 말하면 hard target 또는 hard prediction이다. 실제로 모델은 softmax function을 통해 그렇게 '딱딱한' 결과를 내놓지 않고, [0.00001, 0.8999, 0.002] 이런 soft target 또는 soft prediction을 뱉어낸다. 여기서 이 'soft target'을 학생에게 지식을 전달하는데 효과적이라고 주장하는게 논문의 내용이다. soft target의 분포를 학생에게 학습시킨다는 것은 선생님이 학습한 지식을 그냥 ~~말 그대로~~ 하드하게 가르치는게 아니라 충분히 일반화해서 전달할 수 있다는 것이다.  

이쯤 왔으면 힌튼교수가 loss function에서 말하는 T(temperatrue)를 이해할 수 있다. 이 T 파라미터를 softmax fuction에 다음과 같이 적용했다. (T=1이면 우리가 아는 그 softmax function이다.)

$$p_i=\frac{exp(\frac{z_i}{T})} {∑_jexp(\frac{z_j}{T})}$$

T가 커지면  exponential의 값이 작아질 것이다. 따라서 큰 값(0.899 같은)이 더 커지는 현상이 줄어들고  작은 값은[0.00001] 기본 softmax function의 결과보다 커지게 되서 전체적인 결과의 분포가 좀 더 부드러워진다. 

#### 02-2. Specialist Networks

학생한테 가르치기 전에 선생님은 미리 배워야 한다. 근데 이 배움의 단계에서 아무리 병렬학습, 리소스 플랙스를 해도 많은 시간이 걸리는건 팩트... 논문에서는 specialist 모델을 만들어서 학습의 효율성을 높일 수 있다고 주장한다. 이 specialist 모델은 '혼동하기 쉬운 특별한 부분 집합'에 대해서만 학습한 모델이다. 즉, '버섯', '스포츠카' 같은 데이터만 보아서 학습한 모델이다. 근데 이렇게 학습하면 완전 오버피팅된 모델이지 않나? 그래서 학습할 때 일반적인 데이터셋과 '특정 클래스의 데이터셋'을 반반 섞어서 학습해야 한다. 학습한 후에는 '특정 클래스의 데이터셋'의 비율에 맞춰서 결과 값은 스케일링해준다. (근데 이 부분은 잘 이해가 되지 않는다.. 흠..)

### 03.  네트워크 구조와 손실함수 이해하기
먼저 네트워크 학습을 위한 **loss function**을 보자 .

![이미지1](https://t1.daumcdn.net/cfile/tistory/997F73405E0E92422E)

> 왼쪽 항은 아래 그림의 **student loss**에 해당된다. 학생의 분류 성능을 개선하기 위한 손실함수 부분으로, 실제 값과 학생이 추정한 값의 결과를 Cross Entropy 손실함수로 계산한다. **Hard label 사용** 

$$(1-α)L_{CE}(σ(Z_s), \hat{y})$$ 


> 오른쪽 항은 아래 그림의 **distillation loss**에 해당된다. 선생님과 학생의 분류 결과 차이를 손실함수에 포함시키겠다는 것이다. 이때 앞서 말했던 T파라미터를 사용해서 두 모델이 추정한 결과를 soften된 분포로 변환해서 Cross Entropy 손실함수를 계산한다. **Soft label 사용**

$$2αT^   2L_{CE}(σ(\frac{Z_t}{T}), σ(\frac{Z_s}{T}), )$$

손실함수를 봤으니, 앞에서 구구절절 얘기했던 '지식 전달'을 다시 생각해보자.  distillation loss를 보면 선생님과 학생 모델의 분류 결과를 비교하기 위해 soft label을 사용한다. 즉, Hard label일 경우에는 정보손실이 발생할 수 있지만, soft label을 사용함으로써  '지식전달'이 가능해진다는 것이 요지인 것 같다. 


![이미지2](https://nervanasystems.github.io/distiller/imgs/knowledge_distillation.png)
전체 구조는 [요 블로그] 가 잘 그려났다.  


### 04. Knowledge Distillation 코드 구현 예시



> 상황 01 : 선생님한테서 학생이 배우는 상황



> 상황 02 : 클래스 3 을 학생한테 일부러 보여주지 않는 상황 (~~알아서 배워 컨셉~~)


[이 블로그]: https://jamiekang.github.io/2017/05/21/distilling-the-knowledge-in-a-neural-network/
[힌튼논문]: https://arxiv.org/abs/1503.02531
[요 블로그]: https://nervanasystems.github.io/distiller/knowledge_distillation.html