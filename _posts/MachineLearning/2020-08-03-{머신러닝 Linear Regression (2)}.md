---
layout: single
title: "Linear Regression (2) - Machine Learning"
categories:
  - MachineLearning
classes: wide
tags:
  - jeykll
  - github
  - Minimalmistakes
  - blog
  - MachineLearning
use_math: true
comments: true
---

###### 이번 포스팅은 Linear Regression에 대해 포스팅하는 두 번째 시간입니다.  

##### 본 포스팅은 광운대학교 정한울 교수님 머신러닝 과목을 바탕으로 작성을 하였습니다.  
---  

지난 포스팅에 이어서 Gradient Descent Method에 대해 설명하겠습니다.

# Gradient Descent Method

+ $$MSE(\textbf{w}) = \frac{1}{N}\sum_{i = 1}^{N}(y_{i}-\mathbf{w}^{T}x_{i} )^{2}$$
+ $$w_{next} = w_{present} - \eta \bigtriangledown MSE(\mathbf{w})$$, $$\eta$$ = learning rate

MSE가 convex하다면, 훨씬 시간이 단축되며 learning rate란 $$\bigtriangledown MSE$$가 0이 되는 속도를 의미한다고 생각하시면 됩니다. 그러므로 굉장히 중요한 파라미터가 됩니다.

---  

# Learning rate

 + 일반적으로 큰 learning rate부터 시작하여 점점 작은 값으로 변경하며 learning schedule이라고 부름

![Learning rate](/img/Effect_of_Learning_Rate.png
  )  

---  

# Gradient Descent Method vs. Stochastic Gradient Descent

Data가 매우 많다고 가정을 하게 되면 Gradient Descent도 모든 data에 하게 됩니다. 하지만 모든 Data에 적용을 하면 매우 오래 걸리며 연산량도 많아집니다.
그래서 Stochastic Gradient Descent라는 개념이 나왔는데요. 매 step마다 random하게 골라 업데이트를 진행하게 됩니다. 대신에 불안정하며 learning rate가 매우 중요하게 됩니다. learning schedule을 매우 잘 잡아야 되겠죠? 추가적으로 Mini batch Gradient Descent라는 개념도 있습니다.

---  
