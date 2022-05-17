---
layout: single
title: "Object funcion vs. Loss function vs. Cost function"
categories:
  - DeepLearning
classes: wide
tags:
  - jeykll
  - github
  - Minimalmistakes
  - blog
  - DeepLearning
use_math: true
comments: true
---

###### Object funcion vs. Loss function vs. Cost function  

헷갈리는 용어에 대해 알아보겠습니다.  

## Loss function  
  - 하나의 input data에 대해 오차를 계산하는 함수  
  - linear regression에서, square loss $$ l(f(x_{i}|\theta, y_{i})) = (f(x_{i}|\theta - y_{i}))^{2} $$  
  - SVM에서, hinge loss $$ l(f(x_{i}|\theta, y_{i})) = max(0, 1-f(x_{i}|\theta)y_{i})  
  - theorectical analysis, definition of accuracy에서, 0/1 loss $$ l(f(x_{i}|\theta, y_{i})) = 1 \Leftrightarrow f(x_{i}|\theta) \neq y_{i}$$  


## Cost function  
  - 모든 input dataset에 대해 오차를 계산하는 함수  
  - 모든 input dataset에 대해 계산한 loss function의 평균값  

Loss function : $$ L(\hat{y},y) = -(y log\hat{y} + (1-y)log(1-\hat{y})) $$  
Cost function : $$ J(w,b) = -\frac{1}{m}\sum_{i=1}^{m}(y^{(i)} log\hat{y}^{(i)} + (1-y^{(i)})log(1-\hat{y}^{(i)})) $$  

## Object function  
- 학습을 통해 최적화시키려는 함수  
  + 최적화란 최댓값 or 최솟값을 구하는 함수  



개념의 크기로 봤을 때는 아래와 같다.  

Loss function $\leq  $ Cost function $\leq  $ Object function  
