---
layout: single
title: "L1 Regularization and L2 Regularization"
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

###### L1 Regularization과 L2 Regularization에 대해 알아보겠습니다.  

이를 알아보기 전에 Norm과 Loss에 대해 먼저 알면 도움이 됩니다.  

### Norm  
+ 두 벡터 혹은 벡터 하나의 크기(길이)를 측정하는 방법(함수)

$\left\|\overrightarrow{x} \right\|_{p} := (\sum_{i=1}^{N}\left|\overrightarrow{x_i} \right|^{p})^{\frac{1}{p}}$  

+ L1 Norm : 벡터 $\vec{p}$ , $\vec{q}$ 의 각 원소들의 차이의 절댓값의 합  

$d_{1}(\vec{p},\vec{q}) = \left\|\vec{p}-\vec{q} \right\|_{1} = \sum_{i=1}^{n}\left|\vec{p_{i}}-\vec{q_{i}}\right|$  

+ L2 Norm : 벡터 $\vec{p}$ , $\vec{q}$ 의 유클라디안 거리(직선 거리)  

$\left\|\vec{x} \right\|_{2} := \sqrt{\sum_{i=1}^{N}\left|x_{i} \right|^{2}}$  

### Loss  

+ L1 loss : 실제 값과 예측치 사이의 차이(오차)값의 절댓값들의 합  

$L = \sum_{i=1}^{n}\left|y_{i}-f(x_{i}) \right|$  

  - 부르는 용어는 매우 다양함
    + Least absolute deviations, Least absolute Errors, Least absolute value, Least absolute residual, Least absoluteSum of absolute deviations  

+ L2 loss : 오차의 제곱의 합 (Least Square Error (LSE) 라고 부름)  

$L = \sum_{i=1}^{N} (y_{i}-f(x_{i}))^{2}$  

L1 loss가 L2 loss에 비해 outlier에 대해 더 robust하다. 즉. outlier가 적당히 무시되길 바란다면 L1 loss를 사용하면 된다(하지만 L1 loss는 0에서 미분 불가능한 단점이 존재). outlier 등장에 신경을 써야한다면, L2 loss를 사용하면 된다.  

그러면 본 포스팅의 타이틀인 Regularization에 대해 알아보자.  

### Regularization(정규화)  

Overfitting을 방지하고 generalization(일반화) 성능을 높이는데 도움
+ Generalization? model을 쉽게 만드는 방법은 단순하게 cost function을 작아지게 하는 방향으로의 학습이지만, 특정 가중치가 매우 큰 값을 가지기 때문에 model의 '일반화' 성능이 떨어지게 된다. 따라서 Regularization은 이렇게 특정 가중치가 과도하게 커지는 것을 방지한다고 보면 된다.  

#### L1 Regularization

$Cost = \frac{1}{n}\sum_{i=1}^{n}{L(y_{i}, \hat{y_{i}})+\frac{\lambda }{2}\left| w\right|}$  

+ 기존의 cost function에 가중치의 크기가 포함되면서 가중치가 너무 크지 않는 방향으로 학습  
+ L1 정규화를 사용하는 regression model을 Lasso Regression이라고 함  


#### L2 Regularization  

$Cost = \frac{1}{n}\sum_{i=1}^{n}{L(y_{i}, \hat{y_{i}})+\frac{\lambda }{2}\left| w\right|^{2}}$  

+ 기존 cost fucntion에 가중치의 제곱을 포함하여 더함 (weight decay라고도 부름)  
+ L2 정규화를 사용하는 Regression model을 Ridge Regression이라고 함  


#### L1 loss는 feature selection(=Unique하지 않기 때문에)이 가능하기 때문에 convex optimization에 유용하지만 미분 불가능한 점이 있기 때문에 주의 필요!
