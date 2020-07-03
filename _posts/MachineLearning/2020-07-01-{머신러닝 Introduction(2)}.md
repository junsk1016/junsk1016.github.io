---
layout: single
title: "머신러닝 기본 용어(2) - Machine Learning"
categories:
  - MachineLearning
tags:
  - jeykll
  - github
  - Minimalmistakes
  - blog
  - MachineLearning
use_math: true
comments: true
---

###### 이번 포스팅은 머신러닝 기초와 기본 용어에 대해 포스팅하겠습니다.  

##### 본 포스팅은 광운대학교 정한울 교수님 머신러닝 과목을 바탕으로 작성을 하였습니다.  

# Type of Machine Learning
## 1. Supervised vs. Unsupervised Learning  
#### Supervised Learning  
+ 지도학습  
+ Training set : D = {($$\vec{x}_i, \vec{y}_i$$)}<sup>N</sup><sub>i=1</sub> ,  정답(label)이 같이 존재  
+ 인건비, 시간 많이 필요  
+ ex) Classification, Regression, Neural Network ...  

#### Unsupervised Learning  
+ 비지도학습  
+ Training set : D = {$$\vec{x}_i$$}<sup>N</sup><sub>i=1</sub> ,  label 없이 feature만 존재  
+ ex) Clustering, Association rule, Density   estimation, Dimension control ...  

-> 이 둘이 할 수 있는 일은 다름  

## 2. Semi-supervised vs. Reinforcement Learning  
#### Semi-supervised Learning  
+ labeling + unlabeling  

#### Reinforcement Learning
+ reward & punishment
+ reward가 많은 방향으로 polish를 만들도록 함
+ ex) AlphaGO를 학습 시 이겼을 때 reward를 주고 학습을 많이 진행하게 되면 supervised보다 더 좋은 학습 결과 나타냄  

## 3. Parametric vs. Non-parametric method  
#### Parametric
+ Data 튜닝 파라미터 수가 변하지 않음  

#### Non-parametric  
+ Data가 늘어남에 따라 parameter의 수가 증가  
+ 장점 : Parametric보다 최적화 잘 될 수도 있음  
+ 단점 : 계산 복잡  

## 4. Batch vs. Online Learning  
#### Batch Learning  
+ 학습할 때 한번에 Data 대량 넣고 학습  
+ 간헐적 업데이트  

#### Online Learning  
+ Data가 조금씩 들어오면서 학습  
+ Data 끊임없이 들어옴  

---


# Simple Classification & Regression

## Simple Classification  

ex) Age,Vision vs. Glass wearing  
![ML-Simple Classification](https://user-images.githubusercontent.com/61397479/86396586-269f7480-bcdd-11ea-969c-17c56a64c8dc.png)  

이 때 (*X<sub>i</sub>*, y<sub>i</sub>)  
--> *X<sub>i</sub>* = (vision, age), y<sub>i</sub> = 0 or 1  
boundary는 computer가 만듦

## Simple Regression  

ex) Study hour vs. Exam score  
![ML-Simple Regression](https://user-images.githubusercontent.com/61397479/86397478-dde8bb00-bcde-11ea-8e30-38e41919a30c.png)  

이 때 (*X<sub>i</sub>*, y<sub>i</sub>)  
--> *X<sub>i</sub>* = Study hour, y<sub>i</sub> = Exam score  

+ regression 결정 방법
모든 datapoint를 지나갈 수는 없지만 최대한 fitting할 수 있는 직선을 찾음

잘못 찾으면? 점들과 직선 사이의 거리가 최소가 되도록 학습
--> ![ML-Simple Regression-latex](https://user-images.githubusercontent.com/61397479/86397964-c78f2f00-bcdf-11ea-87db-8387a6d146c3.png)  

이 식을 cost라고 하며 이 cost를 최소화하는 a,b를 찾는 것이 목적

이런 방식으로 학습하면 study hour가 주어졌을 때 예상되는 exam score를 출력하게 됨

---

# Probability & Statistics을 알아야 하는 이유
+ Machine Learning에서 기본으로 다룸
+ 실제로 모든 data는 "Uncertain" & "Ambiguous"
+ outlier 들을 Handling하기 위해서

ex) 숫자 5를 주고 무엇인지 맞춰야 하는 알고리즘에 대해 이 숫자가 5인지 3인지를 구별하기 위해서는 확률 기반으로 예측을 하는 것이 더 정확


## Conditional Probability
+ 조건부 확률
+ P(A|B) = B가 일어날 때 A의 확률

---

이번 포스팅은 Introduction인 만큼 용어에 대해 간단하게만 짚고 넘어겠습니다.
다음 포스팅부터 차근차근 알아가보도록 하겠습니다.
