---
layout: single
title: "분류성능 평가 지표(TP, FP, FN, TN, Precision, Recall, Accuracy, IOU, AP)"
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

###### 분류성능 평가 지표(TP, FP, FN, TN, Precision, Recall, Accuracy, IOU, AP)  

Object Detection에서 가장 기본적으로 사용하는 평가지표에 대해 알아보겠습니다.

## 모델의 분류와 정답  

+ True Positive(TP) : True를 True로 예측 (정답)  
+ False Positive(FP) : False를 True로 예측 (오답)  
+ False Negative(FN) : True를 False로 예측 (오답)  
+ True Negative(TN) : False를 False로 예측 (정답)  

앞 글자가 정답/오답이고 뒷 글자가 model이 예측한 결과인 것을 알 수 있습니다.  

## Precision, Recall and Accuracy  

+ Precision(정밀도)  
  - 정답을 정답으로 맞춘 비율  
  - model이 True라고 한 것 중에서 실제 True인 비율 (= $ \frac{TP}{TP+FP} $ )  
  - PPV(Positive Predictive Value)라고도 부름  

+ Recall(재현율)
  - 실제 True인 것들 중에서 model이 True라고 예측한 비율 (= $ \frac{TP}{TP+FN} $ TP/(TP+FN))  

Precision과 Recall을 함께 고려하면 제대로 평가할 수 있다. 하지만 trade-off 관계 존재.  

+ Accuracy(정확도)  
  - False를 False로 예측한 결과까지 포함하는 것 (= $ \frac{TP+TN}{TP+FN+FP+TN} $ )  
  - 가장 직관적으로 모델의 성능을 나타내는 지표  
  - domain의 bias(편중)를 주의해야 함(data가 불균형이 있을때).

## F1 score  
  - Precision과 Recall의 조화평균  
  - data가 불균형 구조일 때 정확하게 파악할 수 있음
  - $ 2 \times \frac{1}{\frac{1}{precision} + \frac{1}{recall}} = 2 \times \frac{Precision \times Rcall}{Precision + Recall} $

## Intersectino over union (IOU)  
  - Boundary Box label이 가진 data에서 예측한 Bounding Box의 값이 옳은지 아닌지를 결정하는 측정 방법  
  - $ IOU = \frac{area(B_{gt}\cap B_{p})}{area(B_{gt}\cap B_{p})} $  

## Precision-Recall 곡선  
  - confidence level에 대한 threshold 값의 변화에 의한 object detector의 성능을 평가하는 방법  
    + threshold 값을 낮추어가며 측정  

## Average Precision(AP)  
  - 단 하나의 숫자로 성능을 평가할 수 없는 PR 곡선의 단점을 보완  
  - PR 곡선에서 선 아래의 면적으로 계산(단조적으로 감소하는 그래프가 되게하기 위해 PR 곡선으로 바꿈 )  

## mAP(mean Average Precision)  
  - class 당 AP를 모두 구한 후 더한 다음 class 개수로 나눈 값  
