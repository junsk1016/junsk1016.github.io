---
layout: single
title: "Probability & Bayesain Learning (1) -수정중 - Machine Learning"
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

###### 이번 포스팅은 머신러닝에 필요한 Probability & Bayesain Learning에 대해 포스팅하겠습니다.  

##### 본 포스팅은 광운대학교 정한울 교수님 머신러닝 과목을 바탕으로 작성을 하였습니다.  

# Random Variable(확률 변수)

+ Random Variable **X** 는 Probability sapce (**S**, **P**) 실수 R로 mapping 하는 **함수**  

![Random_Variable](https://user-images.githubusercontent.com/61397479/86534602-7ae06980-bf14-11ea-8e7e-8f7022b01e61.JPG)  

  + S = event, P = Probability, **X** = S를 숫자로 mapping하는 함수
ex)
  + S(=event) : 동전이 앞면이 나온다.  
  + P(=probability) : 1/2      

# Discrete(이산) Random Variable (RV)  

+ **X** 는 유한하거나(ex) {0,2,6}) countabley infinite(ex) 정수={0, 1, -1, 2, -2, ...})  

+ 이산 랜덤 변수 **X** 에서 x라는 event가 발생할 확률(**X** = x)은 p(X = x) or p(x)라고 함($$x\in X $$)  

+ 모든 확률의 총합은 1이며 각 확률을 알 수 있는 것을 확률 분포라고 부름  

+ p()를 probability mass function(pmf)라고 부름  

+ EX) 윷놀이에서 확률 분포  
![Discrete_RV](https://user-images.githubusercontent.com/61397479/86558105-62636400-bf93-11ea-89f2-8b6e02d13c04.JPG)  
  이것도 확률 분포(pmf)라고 할 수 있으며 각 확률들의 총합은 1인 것을 확인  

# Continuous(연속) Random Variable(RV)  

+ 이산적인 확률을 구할 수 없음 (예 : P(x=1))  

+ 따라서, 임의의 구간에 있을 확률을 구함  
  + A = (X ≤ a) , B = (X ≤ b), W = (a ≤ X ≤ b)라고 정의했을 때  
  + p(B) = p(A) + p(W)이며 -> p(W) = p(B) - P(A)  
  + 연속에서는 q가 q보다 같거나 작을 확률로 정의하며 cdf(cumulative distribution function)라고 함 (F(q) = P(X ≤ q))  
  + ex) P(a < X ≤ b) = F(b) - F(a)  
  + cdf의 미분 $$f(x) = \frac{\mathrm{d} F(x)}{\mathrm{d} x}$$으로 f(x)를 정의하며 pdf(probability density function)라고 함  
  + pdf를 적분함으로써 확률 분포를 표현(* pdf는 확률이 아님)  
  + $$P(a < X \leq b) = \int_{a}^{b}f(x)dx = F(b) - F(a)$$  

+ f(x)는 모든 x에 대해 항상 0보다 크며 총합은 1  

# Continuous RV에서 Gaussain (Normal) Distribution  

+ why 사용? [Central limit theorem](https://ko.wikipedia.org/wiki/%EC%A4%91%EC%8B%AC_%EA%B7%B9%ED%95%9C_%EC%A0%95%EB%A6%AC)를 만족하기 때문  

Central limit theorem은 간단히 설명하면 각각의 확률이 독립적이지만 합은 가우시안 분포를 띈다는 정리입니다. 자세한 사항은 링크를 참조하시길 바랍니다.  

+ 통계와 Machine Learning에서 가장 많이 쓰이는 분포  
+ ![Gaussain_Distribution_Contiuous_RV](https://user-images.githubusercontent.com/61397479/86562270-64322500-bf9d-11ea-8994-131aba3347d0.JPG)  
+ $$\mu = E[X]$$ : 평균(mean), $$\sigma ^{^{2}} = var[X]$$ : 분산(variance)  
+ **X** ~ N($$\mu, \sigma^{^{2}}$$)의 의미는 확률 p(X=x)가 N(x\\$$\mu, \simga^{^{2}}$$)를 따른다는 의미  
+ Gaussian의 cdf
![Gaussain_Distribution_Contiuous_RV_cdf](https://user-images.githubusercontent.com/61397479/86562799-44e7c780-bf9e-11ea-908b-bf704ef844fe.JPG)  

+ ex) 2~3의 확률을 구하려면 2~3까지 적분만 하면 됩니다.
+     P(2 ≤ x ≤ 3) = $$\Phi (3) -  \Phi (2)$$  

# Joint Probability  
+ A와 B가 동시에 일어날 확률  
+ $$P(A,B) = P(A \cap B)$$ 라고 정의
