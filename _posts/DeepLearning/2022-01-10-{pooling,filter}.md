---
layout: single
title: "pooling과 filter란?"
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

## 딥러닝에서 pooling과 filter에 대해 기초 다지기    
단순히 용어의 설명이 아닌 역할에 대해 알아보겠습니다.  

## Pooling?  
학습 시 backpropagation할 때, 중간값을 필요로 하게 됩니다. 이때 굉장히 많은 계산량을 소모하게 됩니다. 이때 pooling layer를 사용하게 되면 계산량/메모리의 사용량을 줄여주며 overfitting을 방지하는 역할을 하게 됩니다. 그 이유는 학습할 때 파라미터가 감소해서이겠죠. 또한 pooling layer를 사용함으로써 특정 데이터를 강조하는 역할도 하게 됩니다. 다른 특징으로는 pooling layer 자체는 파라미터를 요구하지 않으며, 연속적으로 pooling layer를 쌓지는 않으니 유의하시길 바랍니다.  

종류로는 max pooling, mean pooling 등 다양하게 있습니다. max pooling의 경우, 정보 소실의 가능성이 있으며 mean pooling보다 연산량이 적은 특징이 있습니다. mean pooling의 경우 정보손실은 상대적으로 적으나, max pooling에 비해 성능이 조금 떨어진다고 합니다. 제 개인적인 생각엔 성능 면에서는 확실히 뭐가 좋다고는 말할 수는 없다고는 생각합니다..  

## Filter?  

<p align="center"><img src="/img/KnowledgeDistillation.jpg"></p>  

kernel이라고도 하며, convolution layer에 n개의 필터가 적용된다면 출력 data는 n개의 채널을 가집니다. 만약 input channel이 여러개라면 어떻게 될까요??  

정답은 "input channel과는 상관 없이 필터(커널) 개수로 feature map이 생성된다." 입니다. channel 별로 나온 결과를 단순히 더하는 것이라고 생각해도 될 것 같습니다..  

input이 3x3x3이며, filter가 2x2이고 stride가 1일때를 생각해보겠습니다. input의 각 채널들이 필터와 convolution 연산을 하며 output으로 2x2x1을 3개 출력하게됩니다. 이 3개가 최종 output이 아니라 이 3개를 합하여 각 index에 맞는 값끼리 더하여 최종 output은 2x2x1이 된다고 생각하시면 되겠습니다. 여기서 최종 출력의 channel수는 filter 수와 같습니다.(머리속으로 생각해보면 이해하실 수 있을거에요.)  

특징은 아래와 같습니다.  
+ convolution layer의 학습 파라미터 수 = 입력 채널 개수 X 필터 width X 필터 height X 출력 채널 개수(=필터 개수)  

+ pooling으로 들어가는 input의 h,w는 pooling layer h,w의 배수여야 한다.  

+ convolution layer의 output data 크기는 아래와 같다.(H:input의 height, P:padding size, FH:filter의 height, FW:filter의 width, S:stride)    
  - Height = $\frac{H+2P-FH}{S}+1$  
  - Width = $\frac{W+2P-FW}{S}+1$  

+ fully connected layer의 파라미터 수 = input data shpae x 분류할 class 수  
  - 예) input data : (160, 1), 분류할 class 수 : 100개(=(100, 1) )  
  - 파라미터 수는 160x100 = 160,000개  
