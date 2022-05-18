---
layout: single
title: "Validation and Test dataset, Cross validation, Normalization"
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

###### Validation and Test dataset, Cross validation, Normalization에 대해 알아보겠습니다.  

## Validation and Test dataset  

### Test dataset  
+ Unseen data가 입력되었을 때, 학습 결과와 유사한 결과가 나오는지 확인하는 과정  
+ Generalization(일반화)를 평가한다고도 볼 수 있음  
+ Model이 overfitting 되었는지도 확인  


### Validation dataset  
+ Model의 Hyperparameter를 조절하는 phase  
  - Hyperparameter? 사용자가 임의로 지정할 수 있는 parameter. 예) learning rate  
+ Valid set을 model에 입력해보며 hyperparameter를 조정해 model의 학습 방향을 정함  

## Corss Validation  

+ Validation보다는 Test 목적으로 쓰이는 방법론 (Training data가 적을 경우 사용)  
+ 전체 dataset이 적을 경우, Test dataset을 어떻게 잡느냐에 따라 성능이 상이하게 평가된다면 모델 평가 지표에 편향이 생겼다고 말할 수 있다. 이럴 경우를 극복하기 위해 corss validation 사용  
+ Cross Validation  
  - i) Dataset을 K개로 나눈다.  
  - ii) 학습이 완료된 model을 각각 iteration에서 나눈 dataset에 대해 한 번씩 test 수행(나머지 K-1개는 train)  
  - iii) N번의 test 결과를 평균하여 model 평가  
  - iv) 이후 hyperparameter set을 일부 변경하여 반복해 구한 정확도의 평균을 비교하여 최적의 hyperparameter set 찾기  
  - 이 방법을 K-fold cross validation 이라고 부름  

+ 단점은 다음과 같다.  
  - test에 seen data를 사용하기 때문에 test 결과가 model이 정말로 generalize한지 알기가 애매함  
  - 따라서 아래의 방법을 추구한다.  
  - 1) 전체 dataset을 최대한 늘리는 것이 좋다.  
  - 2) 처음부터 dataset에서 일부를 떼어내어 test용으로 Unseen 상태로 두는 것이 좋다.  


## Normalization  

### Data Normalization  
+ 데이터의 범위를 사용자가 원하는 범위로 제한하는 것  
+ ex: 이미지의 경우 0~255를 0~1.0   
+ 수식은 아래와 같다.  

$\frac{정규화하고자 하는 값 - 데이터 값들 중 최소값}{데이터 값들 중 최대값 - 데이터 값들 중 최소값}$  
or  
$\frac{정규화하고자 하는 값 - 데이터의 평균}{데이터의 표준 편차}$  

### 왜 Normalization을 할까?  
+ 학습을 더 빨리하고 local minimum에 빠지는 것을 막을 수 있다.  
+ 데이터의 분포가 길쭉한 타원(0)보다 구형(O)인 것이 gradient descent 방법으로 minimum을 찾을 때, 더 빨리 찾을 수 있음  
+ Local optical에서 더 빨리 나올 수 있음.
