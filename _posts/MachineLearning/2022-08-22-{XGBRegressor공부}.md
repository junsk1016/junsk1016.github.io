---
layout: single
title: "XGBRegressor 공부 정리"
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

###### 이번 포스팅은 XGBRegressor 공부하고 정리하는 내용입니다. 자세한 수식은 다루지 않았습니다.  

---  

해커톤을 진행하면서 XGBRegressor를 사용했는데 단순히 성능이 잘 나와 사용했던 터라 이번에 공부하게 되었습니다. 내용이 생각보다 방대하여 간단히만 정리하도록 하겠습니다. 참고한 사이트는 아래에 기재하였으니 더 자세하게 공부하고 싶으신 분들은 아래 링크 참고하시길 바랍니다.  

글의 순서는 큰 개념부터 모르는 개념을 파고드는 식으로 구성하였습니다.  

### XGBRegressor?  
+ Regression을 위한 XGBoost 모델  
+ 그렇다면 XGBClassifier는 Classification을 위한 XGBoost 모델  

결국 XGBRegressor가 무엇인지 알려면 XGBoost가 무엇인지를 알아야 합니다.  

### XGBosst?  
+ Gradient Boosted Decision Trees 알고리즘의 오픈 소스 구현  
+ 위에서 언급했듯이 Regressor와 Classification 등의 모델 제공  
+ 사용할 떄는 XGBoost 라이브러리도 자체 API가 있지만 사이킷런 에서도 XGBRegressor와 XGBClassifier가 있음(저는 사이킷런을 사용했었습니다.)  

### 결정 트리 앙상블(Decision Tree Ensembles)?  
+ 결정 트리 앙상블 중 하나가 XGBoost의 기반 모델인 gradient boosting decision (GBDT)임  
+ 결정 트리 앙상블 모델은 Classification And Regression Tree(CART)로 구성  
+ CART의 예시  
  - 어떠한 조건(하나의 트리가 됨)을 주고 해당하면 가산점, 해당하지 않으면 감산점을 주는 방식  
  - 여러개의 트리를 통해 최종 점수를 얻어서 분류하거나 회귀  


#### 참고한 사이트  
https://webnautes.tistory.com/1643
https://wooono.tistory.com/97  
