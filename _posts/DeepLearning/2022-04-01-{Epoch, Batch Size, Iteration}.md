---
layout: single
title: "Epoch, Batch Size, Iteration"
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

###### Epoch, Batch Size, Iteration에 대해 알아보겠습니다.  

## 왜 나누어 학습할까?  
#### 다루어야 할 data가 매우 많다. 한 번의 계산으로 최적화 값을 찾는 것은 매우 힘듦.  


### Epoch  
+ Neural Network에서 전체 dataset에 대해 forward pass/backward pass 과정을 거친 것  
+ 전체 데이터셋에 대해 한 번 학습을 완료한 상태  
+ 너무 적으면 underfitting, 너무 크면 overfitting  

### Batch Size  
+ 한 번의 Batch마다 주는 data sample의 size  
+ 메모리의 한계로 (속도 저하의 문제도 존재) 한 epoch을 한 번에 불가  
  - 때문에 data를 나누어주게 되는데  
    + 몇 번을 나누어 주는가 == "iteration"  
    + 각 iteration마다 주는 data size == "Batch size"  


### 1 epoch = (batch size) x (iteration 수)  
