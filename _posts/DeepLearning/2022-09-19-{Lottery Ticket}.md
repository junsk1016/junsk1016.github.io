---
layout: single
title: "The Lottery Ticket Hypothesis: Finding Sparse, Train able Neural Network와 후속 연구"
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

######  Lottery Ticket Hypothesis와 그 후속 연구 관련 글을 보고 정리했습니다. 공부한 링크는 아래와 같습니다.  
---

https://velog.io/@bismute/The-Lottery-Ticket-Hypothesis%EC%99%80-%EA%B7%B8-%ED%9B%84%EC%86%8D-%EC%97%B0%EA%B5%AC%EB%93%A4-%EB%A6%AC%EB%B7%B0

---

## The Lottery Ticket Hypothesis  
+ ICLR 2019의 Best Paper Award 수상  
+ 현재 딥러닝 모델은 데이터 수, 복잡성에 대해서도 훨씬 많은 파라미터 사용   
+ "The curse fo dimensionality"  
  - '더 많은 파라미터', '더 복잡한 모델'은 데이터의 수가 적을수록 암기하기 때문에 문제 발생(overfitting)
+ 하지만, Gradinet Descent는 자체적으로 어느정도 정규화 효과가 존재하며, 그 결과 빅데이터와 딥러닝의 조합이 훌륭한 feature extractor를 만드는 현존하는 최고의 방법(sota)라는 것이 알려짐  

## 논문의 가설  
+ random 초기화된 dense neural network는 개별 학습할 때 동일한 iteration에서 원래 네트워크의 성능과 같은 subnetwork를 포함한다.  
  - 즉, 부분적인 네트워크를 잘 골라내면 기존의 큰 네트워크와 같은 성능을 낼 것이라는 주장!  
  - 이를 Lottery Ticket Hypothesis라고 함  

+ 찾는 여러 방법들 중 하나인 iterative Manitude Pruning을 통해 네트워크를 잘 찾는 방법을 제안  
  - Pruning : 가중치의 크기가 작은 것은 제거  
  - 제거해도 괜찮은가?  
    + 레이어를 시각화하면 sparse 구조 나타남(90%까지 앞축해도 성능은 1~2%차이)  

## 그렇다면 어차피 가지치기하고 재학습을 해야하는데 가지치기를 먼저 하면 안되는건가?
+ 경헙적으로 불가하였다. (실험해봤는데 실패했다는 뜻)
+ 아웃 뉴런이 있어야 바르게 학습했다.  
+ 가지치기 이후 fine-tuing이 쉽지 않음  

+ 논문에서는 [reinit] 기법을 통해 해결하였음  
  - 무엇이 중요한지 저장된 mask는 학습된 네트워크를 기준으로 가져오되, 가중치는 학습하기전의 네트워크를 가져오면(reinit) fine-tuning 가능  

  - 이렇게 찾은 네트워크는 기존의 네트워크보다 같거나 좋은 성능을 학습 iteration에서 도달함  

+ 논문의 한계 : 가지치기를 위해 학습이 필요  
+ 논문의 의의 : winning ticket의 존재를 발견  


## 후속 연구들..

### 다른 모델, optimizer, 과제, 데이터셋에서도 동작할까?
+ "The Lottery"에서는 vgg, resnet과 같은 고전 모델, SGD, Image Classification, CIFAR10에서 실험하였음  

+ 논문: "One ticket to win them all: generalizing lottery ticket initialization across dataset and optimizers"  
  - winning ticket이 Fashion MNIST, SVHN, CIFAR 10/100, ImageNet, Places365 등 다양한 데이터셋에서 찾아졌음  
  - SGD뿐만 아니라 Adam에서도 동작함을 확인  

+ 논문: "Playing the lottery with rewards and multiple languages: lottery tickets in RL and NLP"  
  - 강화 학습과 자연어 처리에서도 lottery ticket 현상이 나타남을 증명  

### 어떻게 하면 학습을 덜 하거나 안하고 당첨될 수 있는가?  
+ 논문: "Drawing Early-Bird Tickets: Towards More Efficient Training of Deep Networks"  
  - 학습을 끝까지 할 필요 없이 큰 learning rate로 조금 학습시키고 mask를 가져와도 winning ticket을 찾을 수 있음    

+ 논문: "SNIP: Single-Shot Network Pruning Based On Connection Sensitivity"  
  - 데이터를 전체 다 볼 필요 없이 한번씩만 보고 mask를 가져와도 됨  

+ 논문: "Picking Winning Tickets Before Training by Preserving Gradient Flow"  
  - weight 말고도 gradient를 보면 더 정확한 mask를 가져올 수 있음  

+ 논문: "Pruning Neural Network without any data by iteratively conserving synaptic flow"  
  - 데이터를 안 보고도 처음부터 중요한 뉴런을 찾을 수 있음  

### 왜 데이터를 보지도 않고 딥뉴럴넷에서 중요한 뉴런들을 찾을 수 있을까?  
맨 마지막 논문("Pruning Neural Network without any data by iteratively conserving synaptic flow")에 대한 질문을 해봅시다.  
+ "Network Pruning" 기법을 통해서 딥뉴럴넷이 무엇이 중요하다고 학습할 수 있고, 이를 통해 딥뉴럴넷을 더 깊게 이해할 수 있음  

+ 데이터를 보지 않고도 중요한 뉴런이 무엇인지 알아가는 과정에서 아래를 알 수 있음  
  - 1) 딥뉴럴넷이 무엇을 학습하고 있는지  
  - 2) 왜 어떤 뉴런은 초기화되자마자 중요하거나 중요하지 않은지  

+ 딥뉴럴넷이 초기 학습에서 배우는 것  
  - 딥뉴럴넷이 학습 초기에 현재 나에게 있는 뉴런과 시냅스 중 어느 것이 중요한지를 판단  
  - 초기에 이상한 data를 학습시키면 상당한 정확도 손실이 있는 반면, 초기 이후에 이상한 data가 있어도 안정적으로 학습  
  - 참고로 CNN은 초기에 형태와 같은 정보들을 학습하고 이후에 점점 detail한 부분 학습  

+ 레이어 붕괴(Layer Collapse)  
  - "Pruning Neural Network without any data by iteratively conserving synaptic flow"에서 제시한 문제점  
  - pruning의 문제점은 앞에서 길이 끊어져버리면, 뒤의 중요한 뉴런들이 정보를 받지 못하여 학습 자체가 불가  
    + 이 문제를 미리 방지할 수 있으면 이론상으로 압축 가능한 최대치 달성이 가능할 것이라고 저자들은 주장  

### 결론  
- "Lottery Ticket Hypothesis"는 딥뉴럴넷이 학습에 필수적이라고 믿어왔던 것들이 실제로는 그렇지 않다는 것을 보여줌  
- 가설이 처음 나왔을 때, winning ticket을 찾는 일이 Iterative Magnitude Pruning처럼 값비싼 방법론이 필요했음
- 하지만, data를 보지 않고도 학습 가능한 작은 네트워크를 찾을 수 있음  
