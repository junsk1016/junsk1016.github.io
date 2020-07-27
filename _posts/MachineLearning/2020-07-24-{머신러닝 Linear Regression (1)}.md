---
layout: single
title: "Linear Regression (1) (수정중) - Machine Learning"
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

###### 이번 포스팅은 머신러닝에 필요한 Probability & Bayesian Learning에 대해 포스팅하겠습니다.  

##### 본 포스팅은 광운대학교 정한울 교수님 머신러닝 과목을 바탕으로 작성을 하였습니다.  
---  

Linear Regression에 들어가기 앞서 먼저 알아야할 것들에 대해 미리 알아보는 시간을 가지도록 하겠습니다. 일단 base로 알아야할 것이 선형대수학인데요. 그 이유가 바로 Matrix와 Vector의 사용때문입니다.  

# Vextor & Matrix  

이전 포스팅들에서 언급한 것처럼 붓꽃을 분류하는 알고리즘에 대해 말하자면, 분류할 수 있는 특징이 꽃잎의 너비와 길이, 꽃받침의 너비와 길이. 이렇게 4개의 특징을 가지고 분류를 하게 되는데 vector로 표현을 하게 됩니다. 이러한 data가 1개가 아니라 무수히 많으니 매번 수많은 값을 표기하는 것이 아니라 Matrix로 간단하게 표현하다고 생각하시면 쉽게 이해할 수 있습니다. 보통 하나의 feature vector는 column vector로 표현하는데요, 데이터 개수를 N개라고 하면 $$\mathbf{X}$$ = $$\mathbf{x}_{1}^{T}$$ ... 로 정의가 가능하며 design matrix라고 부릅니다. size는 N x D가 되겠습니다. 또한 이 Design matrix를 weight matirx와 곱하게 되고 이 weight 값을 변경하여 각각의 중요도를 바꾸는 것이고 model을 수정한다고 말씀드렸습니다.  

# Linear Regression 예  

+ 공부시간 vs. 시험 점수  
+ (지역, 상권, 교통) vs. 집값  
+ (키, 몸무게, 운동시간) vs. 신체 나이  

이렇게 결과로 연속적인 값이 나온다고 보시면 됩니다. 공부시간처럼 input이 1차원으로 이루어진 것을 linear Regression이라고 합니다. 집값이나 신체나이의 경우처럼 input의 차원이 높아지는 경우도 있는데요. 2차원인 경우는 multi-dimension, 3차원 이상부터는 hyper-demension이라고 부르며, 추가적으로 $$\hat{y} = a_{1}x_{1} + a_{2}x_{2} + a_{3}mx_{1}^{2}$$처럼 제곱 이상이 들어간 경우를 polynomial regression이라고 부릅니다.  

# Linear Regression model  

$$\hat{y} = \theta_{0} + \theta_{1}x_{1} + \theta_{2}x_{2} + \theta_{3}x_{3} + ... + \theta_{D}x_{D}$$  
+ $$\hat{y}$$ = 모델이 예측한 값  
+ $$D$$ = feature의 수  
+ $$x_{i} = i^{th}$$ feature value  
+ $$\theta_{j} = j^{th}$$ model parameter  
+ $$\theta_{0}$$ = bias  

$$\hat{y}$$는 model이 예측한 값이지 정답을 말하는 것이 아님을 주의해야 합니다. 또한, bias값 때문에 matrix의 곲을 진행할 때에는 실제로 없는 $$x_{0}$$을 만들고 1값으로 해줘야합니다. 아래 식은 벡터로 표현한 식입니다. 참고하시면 됩니다.  

$$\hat{y} = h_{\theta}(\mathbf{x}) = \mathbf{\theta}^{T}\mathbf{x} = \begin{bmatrix}
\theta_{0} & \theta_{1} & \theta_{2} & \theta_{3}
\end{bmatrix}
\begin{bmatrix}
x_{0}=1\\
x_{1}\\
x_{2}\\
x_{3}
\end{bmatrix}$$  

+ $$h_{\theta}$$ : hypothesis function with model parameter vector $$\theta$$  

이제 개념은 얼추 잡았으니 어떻게 모델이 학습되는지 알아야겠죠? 학습은 주어진 데이터에 가장 적합한 MLE의 선형 곡선을 결정해야합니다. 즉 $$\hat{y}$$을 올바르게 정해야합니다. N=1(feature vector가 1일 때)를 예로 들어 보겠습니다. 이때의 예측값 $$\hat{y}$$은  
$$\hat{y} = h_{w}(\mathbf{x}) = \mathbf{w}^{T}\mathbf{x} = w_{0} + w_{1}x_{1}$$  
이며 우리는 아래 그림처럼 data의 분포를 최대한 만족하는 $$\mathbf{w}$$을 결정해야 합니다.  

![N=1_Example](./img/N=1_Example.jpg)  

참고로 \vec{\theta} = (\theta_{0}, \theta_{1})를 linear regression에 한해서는 w라고 합니다!  
