---
layout: single
title: "Probability & Bayesian Learning (1) - Machine Learning"
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
---  

# Random Variable(확률 변수)

+ Random Variable **X** 는 Probability sapce (**S**, **P**) 실수 R로 mapping 하는 **함수**  

![Random_Variable](https://user-images.githubusercontent.com/61397479/86534602-7ae06980-bf14-11ea-8e7e-8f7022b01e61.JPG)  

  + S = event, P = Probability, **X** = S를 숫자로 mapping하는 함수
ex)
  + S(=event) : 동전이 앞면이 나온다.  
  + P(=probability) : 1/2  

---  
# Discrete(이산) Random Variable (RV)  

+ **X** 는 유한하거나(ex) {0,2,6}) countabley infinite(ex) 정수={0, 1, -1, 2, -2, ...})  

+ 이산 랜덤 변수 **X** 에서 x라는 event가 발생할 확률(**X** = x)은 p(X = x) or p(x)라고 함($$x\in X $$)  

+ 모든 확률의 총합은 1이며 각 확률을 알 수 있는 것을 확률 분포라고 부름  

+ p()를 probability mass function(pmf)라고 부름  

+ EX) 윷놀이에서 확률 분포  
![Discrete_RV](https://user-images.githubusercontent.com/61397479/86558105-62636400-bf93-11ea-89f2-8b6e02d13c04.JPG)  
  다음과 같은 그래프도 확률 분포(pmf)라고 할 수 있으며 각 확률들의 총합은 1인 것을 확인  

---  
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

---  

# Continuous RV에서 Gaussain (Normal) Distribution  

+ why 사용? [Central limit theorem](https://ko.wikipedia.org/wiki/%EC%A4%91%EC%8B%AC_%EA%B7%B9%ED%95%9C_%EC%A0%95%EB%A6%AC)를 만족하기 때문  

Central limit theorem은 간단히 설명하면 각각의 확률이 독립적이지만 합은 가우시안 분포를 띈다는 정리입니다. 자세한 사항은 링크를 참조하시길 바랍니다.  

+ 통계와 Machine Learning에서 가장 많이 쓰이는 분포  
+ ![Gaussain_Distribution_Contiuous_RV](https://user-images.githubusercontent.com/61397479/86562270-64322500-bf9d-11ea-8994-131aba3347d0.JPG)  
+ $$\mu = E[X]$$ : 평균(mean), $$\sigma ^{^{2}} = var[X]$$ : 분산(variance)  
+ **X** ~ N($$\mu, \sigma^{^{2}}$$)의 의미는 확률 p(X=x)가 N(x\|$$\mu$$, $$\sigma ^{^{2}} $$)를 따른다는 의미  
+ Gaussian의 cdf
![Gaussain_Distribution_Contiuous_RV_cdf](https://user-images.githubusercontent.com/61397479/86562799-44e7c780-bf9e-11ea-908b-bf704ef844fe.JPG)  

ex) 2~3의 확률을 구하려면 2~3까지 적분만 하면 됩니다.  
P(2 ≤ x ≤ 3) = $$\Phi (3) -  \Phi (2)$$  

---  

# Joint Probability  
+ A와 B가 동시에 일어날 확률  
+ $$P(A,B) = P(A \cap B)$$ 라고 정의  

---  
# Conditional Probability(조건부 확률)  

+ p(A\|B) = $$\frac{p(A,B)}{p(B)}$$ if p(B) > 0  

+ given을 하였으므로 전체 집합이 줄어듦  

---  
# Bayes Rule(or Bayes theorem)  

$$p(X=x \mid Y=y) = \frac{p(X=x \mid Y=y)}{p(Y=y)} = \frac{p(X=x)p(Y=y\mid X=x)}{\sum_{x^{'}}p(X=x^{'})p(Y=y \mid X=x^{'})}$$  

증명)  
p(A\|B) = $$\frac{p(A,B)}{p(B)}$$ 에서 분모를 곱하면 P(A,B) = p(A\|B)P(B)  
      p(B\|A) = $$\frac{p(A,B)}{p(A)}$$ 에서 분모를 곱하면 P(A,B) = p(B\|A)P(A)이므로  
      P(X=x\|Y=y)를 P(Y=y\|X=x)로 구할 수 있음  

분모의 경우 P(Y=y)는 $$x^{'}$$들의 합으로 볼 수 있으므로 다음과 같습니다.

수식으로만 보면 어렵고 이해가 잘 안되므로 문제를 풀면서 이해해보도록 하겠습니다.

---


Q1) 암에 걸렸을 때 조직검사에서 양성이 나올 확률이 0.8%라고 하자. 그렇다면 조직검사에서 양성이 나왔을 때 실제로 암일 확률은 얼마나 될까??  
given : 암에 걸릴 확률 = 0.004 , 암에 걸리지 않았을 때 양성이 나올 확률 = 0.1  

과연 암에 걸렸을 때 조직검사에서 양성이 나올 확률이 이전과 마찬가지로 0.8일까요? 아닙니다. 방금 전에 배운 Bayesian rule을 이용해서 풀어봅시다.  

Y = 1 : 양성, X = 1 : 암에 걸렸을 때라고 놓겠습니다.  
그렇다면 P(Y = 1 \| X = 1) = 0.8이겠죠? 우리가 구하려는 것은 P(X = 1 \| Y = 1)입니다. 위에서 배웠다시피  

P(X = 1 \| Y = 1) = $$\frac{P(X=1, Y=1)}{P(Y=1)}$$ 로 볼 수 있습니다.  
분자먼저 보겠습니다.  
P(X = 1 ,Y = 1) = P(Y = 1 \| X = 1)P(X = 1)로 놓으면  
P(Y = 1 \| X = 1) = 0.8, P(X = 1) = 0.4이므로 분자는  
0.8 x 0.4 = 0.32%입니다.  
이제 분모를 보면, P(Y = 1)은 P(Y = 1 \| X = 1)P(X = 1) + P(Y = 1 \| X = 0)P(X = 0)이므로  
(0.004)x(0.8)+(0.1)(0.096) = 0.1 입니다.  
따라서 암에 걸렸을 때 양성이 나올 확률은 (0.32%)x(0.1)이므로 3%인 것을 알 수 있습니다.

이해에 도움이 되었길 바랍니다. 그렇다면 조금 더 머신러닝다운 문제를 하나 보도록 하겠습니다.  

---

Q2) 뽑기통에서 티켓을 뽑았는데 Ocean View에 당첨되었다고 하자. 그렇다면 어느 호텔에 있을 확률이 제일 클까?
![Ocean_Veiw_Room_Ex](https://user-images.githubusercontent.com/61397479/86785606-dbe77900-c09d-11ea-9c88-5923591d9b7f.JPG)  

Ocean View = O라고 하겠습니다. 티켓애서 뽑는거니 각각의 확률을 쉽게 알 수 있습니다. 그렇다면 이제 어느 호텔에 있는지를 구해보겠습니다.  
먼저 각각의 확률을 구해봅시다.

 + P(A\|O) = $$\frac{P(A, O)}{P(O)}$$ = $$\frac{P(A \mid O)P(A)}{P(O)}$$ - (1)  
 + P(B\|O) = $$\frac{P(B, O)}{P(O)}$$ = $$\frac{P(B \mid O)P(B)}{P(O)}$$ - (2)  
 + P(C\|O) = $$\frac{P(C, O)}{P(O)}$$ = $$\frac{P(C \mid O)P(C)}{P(O)}$$ - (3)  
 인 것을 금방 알 수 있습니다! 하지만 우리는 정확한 확률값을 구하고 싶은 것이 아닙니다. 이 3가지의 확률 중 가장 큰 확률을 찾는 것이 목적이기 때문에 공통인 분모 P(O)는 무시할 수 있고, 위의 식들은 아래와 같이 정의할 수 있습니다.


 + (1) P(A\|O) ∝ $$P(A \mid O)P(A)$$ = 0.75 x $$\frac{4}{9}$$  
 + (2) P(B\|O) ∝ $$P(B \mid O)P(B)$$ = 0.25 x $$\frac{2}{9}$$  
 + (3) P(C\|O) ∝ $$P(C \mid O)P(C)$$ = 0.5 x $$\frac{3}{9}$$  

위 식을 풀어보면 (1)식이 제일 크므로 A에 있을 확률이 제일 큰거죠.  

이렇게 조건부 확률을 머신러닝에 적용할 수 있습니다. 특히 머신러닝은 상대적 확률만 중요하다는 것을 기억하시면 됩니다. 그러면 여기서 Ocean View는 뭐라고 볼 수 있을까요? feature라고 볼 수 있겠죠?  
여기서 feature란 정답을 알기위한 힌트?라고 보시면 됩니다. 주어진 input같은 거죠. 더 깊게 들어가면 정의가 달라질 수는 있지만 추후에 더 정확하게 말씀드리겠습니다.

방금과 같은 문제에 대해 수식 하나를 가져와서 보면  
$$P(y=c \mid \mathbf{x})$$ ∝ $$P(\mathbf{x} \mid y=c)P(y=c)$$라고 볼 수 있습니다.  

수식을 설명하면,  
$$P(y=c \mid \mathbf{x})$$ : feature **x** 가 주어질 때 여러개의 정답(class) 중에서 c일 확률은  
$$P(\mathbf{x} \mid y=c)$$ : 정답 c일 때의 feature 확률과  
$$P(y=c)$$ : 정답들 중에서 c일 확률의 곱에 비례한다는 겁니다.

왼쪽의 수식은 저희가 모르는 수식인데 오른쪽의 확률은 알고 있을 확률이 매우 큽니다.  

즉, 모르는 것을 아는 것들의 곱으로 구할 수 있는데요. 각 확률이 어떠한 분포를 가진다고 할 때, 모르는 왼쪽 식의 분포에 상관없이 각각 class에 대해 확률 분포를 구할 수 있다는 것입니다.

다음 포스팅에서는 $$P(y=c \mid \mathbf{x})$$ ∝ $$P(\mathbf{x} \mid y=c)P(y=c)$$ 식에 대해 더 알아보고 머신러닝에서 어떻게 답을 찾는지 알아보겠습니다.  
