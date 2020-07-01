---
layout: single
title: "머신러닝 기본 용어(2)(수정중) - Machine Learning"
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

# Type of Machine Learning에 대해 알아보겠습니다.  
## 1. Supervised vs. Unsupervised Learning  
#### Supervised Learning  
+ 지도학습  
+ Training set : D = {($$\vec{x}_i, \vec{y}_i$$)}<sup>N</sup><sub>i=1</sub>,  
정답(label)이 같이 존재  
+ 인건비, 시간 많이 필요  
+ ex) Classification, Regression, Neural Network ...  

#### Unsupervised Learning  
+ 비지도학습  
+ Training set : D = {$$\vec{x}_i$$}<sup>N</sup><sub>i=1</sub>
label 없이 feature만 존재  
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

# Simple Classification & Regression
