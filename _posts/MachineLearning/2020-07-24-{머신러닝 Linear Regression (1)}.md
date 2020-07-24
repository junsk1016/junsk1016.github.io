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

이전 포스팅들에서 언급한 것처럼 붓꽃을 분류하는 알고리즘에 대해 말하자면, 분류할 수 있는 특징이 꽃잎의 너비와 길이, 꽃받침의 너비와 길이. 이렇게 4개의 특징을 가지고 분류를 하게 되는데 vector로 표현을 하게 됩니다.
