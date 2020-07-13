---
layout: single
title: "Probability & Bayesian Learning (2) (수정중)- Machine Learning"
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

###### 이번 포스팅은 머신러닝에 필요한 Probability & Bayesian Learning에 대해 포스팅하겠습니다.  

##### 본 포스팅은 광운대학교 정한울 교수님 머신러닝 과목을 바탕으로 작성을 하였습니다.  
---  
저번 포스팅에 이어서 알아보도록 하겠습니다.

$$P(y=c \mid \mathbf{x}) = \frac{p(y=c,\textbf{x}))}{p(x)}= \frac{P(\mathbf{x} \mid y=c)P(y=c)}{P(x))}$$  

저번 포스팅에서 bayesian rule을 이용해 다음과 같은 식을 유도하였고 예제를 통해 알아보았습니다.  

다시 한 번 정리하면, feature **x** 가 주어졌을 때 어느 클래스에 속할지에 대한 확률을 구하는 것은 해당 클래스일 때의 feature의 확률과 클래스가 c일 확률의 곱으로 표현됩니다. 분모에서의 확률은 class와 관련이 없는 고정된 상수이므로 다음과 같이 식을 변형하였습니다.  

# Generative Classifier

$$P(y=c \mid \mathbf{x})$$ ∝ $$P(\mathbf{x} \mid y=c)P(y=c)$$  

그러면 이제 각각의 용어들을 알아보겠습니다.

+ $$P(y=c \mid \mathbf{x})$$ = Posterior(사후 확률) : 우리가 원하는 확률  
+ $$P(\mathbf{x} \mid y=c)$$ = Likelihood : 클래스가 c일 때의 feature들이 입력 feature일 확률  
+ $$P(y=c)$$ = Prior : 해당 클래스 c일 확률  

즉, Posterior는 Likelihood와 Prior의 곱에 비례합니다. 이 식으로 우리가 구하는 것은 무엇이냐, 바로 어느 클래스에 속할지 구하는 것이였죠? 이 말을 식으로 표현해보도록 하겠습니다.  

$$\hat{c} = \underset{c'}{argmax} P(y=c'|\textbf{x})$$  
위 식을 설명하면, $$\hat{c}$$은 여러개의 클래스 중 Posterior의 값을 가장 크게 만드는 클래스입니다. 그래서 우리는 이 $$\hat{c}$$을 찾아야 합니다.

이 식을 구하기 위해서 위에 말했던 Posterior를 Likelihood와 Prior의 곱으로 구하는 것이고 이러한 접근을 MAP(Maximum a posterior)라고 부릅니다.
말 그대로 posterior를 최대로 만드는 클래스 c를 찾는 것입니다.  

---  

# Application to Classification  

붓꽃을 분류하는 것을 예로 들어보겠습니다. 붓꽃에서 3종류(setosa, versicolor, virginica)가 있다고 할 때, 무엇인지 구별하기 위해서는 4가지(꽃잎의 길이와 너비, 꽃받침의 길이와 너비)의 정보가 필요하다고 합니다. 그렇다면 이것을 머신러닝의 관점에서 분류를 해보도록 하겠습니다.  

![Application_to_Classification](https://user-images.githubusercontent.com/61397479/87275803-e4b2d180-c519-11ea-8655-67cb6778e108.JPG)

그림처럼 feature x가 있을 때(주로 벡터로 표현합니다.) 각각의 Posterior 확률을 구한 후 argmax를 통해 Posterior 값을 크게 만드는 class를 선택합니다. 위 그림에서는 versicolor일 때의 확률이 0.75로 가장 크므로 versicolor를 정답으로 선택하는 것입니다.

---  

# Bayesian Concept Learning

머신러닝에서는 모델을 만들어서 분류를 해야되므로 앞서 유도한 식에서 parameter vector $$\theta$$를 추가합니다.

$$P(y=c \mid \mathbf{x,\theta})$$ ∝ $$P(\mathbf{x},\theta \mid y=c)P(y=c \mid \theta)$$  

모델이 $$\theta$$일 때의 Posterior를 구하는 것입니다. 우리는 이 $$\theta$$를 조절하여 모델을 변경할 수 있습니다. 인풋인 feature를 바꿀 수는 없으니까요.
$$\theta$$도 벡터로 구성되며 각각을 weight라고 합니다. 다음의 예로 쉽게 이해해보도록 하겠습니다.

---  

# Number Game Example
