---
layout: single
title: "Probability & Bayesian Learning (2) - Machine Learning"
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

$$P(y=c \mid \mathbf{x}) = \frac{p(y=c,\textbf{x})}{p(x)} = \frac{P(\mathbf{x} \mid y=c)P(y=c)}{P(x)}$$  

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

 + 숫자를 하나씩 알려주면서 규칙을 맞추는 게임  
 + 숫자의 범위는 1 ~ 100  
 + ex) 1부터 10까지의 숫자, 2의 제곱, 짝수 등  

숫자 게임을 예로 들어서 Posterior를 이용해 어떻게 문제를 해결하는지 설명해보도록 하겠습니다.  

먼저 사람이 이 문제를 풀어본다고 하겠습니다. 만약 문제자가 "16"을 말했다고 하면 우리는 여러가지 가설을 생각할 수 있습니다.  
+ 연속된 수라고 생각하면 16 주변의 수들이 연관성이 있다.  
+ "16"은 짝수이니까 2의 배수인 규칙일 확률도 있다.  
+ "16"의 배수, "4"의 배수 등  

반대로 97은 연관성이 없어보이죠? 이렇게 사람이 생각하는 것처럼 머신러닝에서도 모델을 통해 연관성이 높은지 낮은지를 수치화할 수 있어야합니다. 이걸 Posterior Predictive Distribution이라고 합니다.  

---  

# Posterior Predictive Distribution  

다시 한번, 문제자가 "16"을 말했다고 하겠습니다. 그렇다면 Data = {16}인 것이고, 여러개의 클래스(2의 배수, 5의 제곱 등) 중 해당 클래스에서 가장 클 확률을 구해야합니다.  
$$p(\tilde{x}|D)$$로 표현할 수 있으며, $$\tilde{x}$$는 여러 클래스 중 하나의 클래스($$\tilde{x} \in C$$)이고 D는 주어진 Data입니다. 그리고 이것을 Posterior Predictive Distribution라고 합니다.  

그러면 그림으로 확인해 보도록 하겠습니다. 주어진 Data가 16밖에 없으므로 우리들은 다음과 같이 예측할 수 있습니다.  
![Posterior_Predictive_Distribution](https://user-images.githubusercontent.com/61397479/87509904-7ef74e80-c6ad-11ea-8d34-ed301a7ad4b5.jpg)  

각각의 확률을 나타내며 16은 주어진 data이니 1이고, 짝수일 확률, 16의 배수 등 연관성이 높은 수들이 확률이 높아진 것을 확인 할 수 있습니다.  

Q) Data가 더 들어오면 어떻게 될까요?  

Data가 더 주어져서 D = {2, 8, 16, 64}라고 하면 우리는 2의 제곱을 생각해볼 수 있습니다. 이를 귀납적 추론이라고 하며 Posterior Predictive Distribution은 위의 그림에서 아래로 바뀌게 됩니다.
![How_about_with_More_Data](https://user-images.githubusercontent.com/61397479/87510142-faf19680-c6ad-11ea-80a0-cbc4bd56153e.jpg)  

아니면 Data가 2, 8, 64가 아니라 17, 20, 19 가 더 주어진다면 16 근처의 수들이 확률이 올라갈 것입니다.

즉, 주어지는 Data에 따라 Posterior Predictive Distribution가 바뀐다는 것이 중요합니다. 그리고 이것을 머신러닝의 모델이 학습할 수 있도록 해야되겠습니다.

머신러닝에서는 우리가 앞서 정의한 것처럼 2의 배수, 짝수 등 여러 가정들이 있었는데 이것을 hypothesis라고 하며 말 그대로 가설입니다. 주어진 Data들을 모든 H들에 넣은 후 가장 확률이 큰 것을 찾으면 되겠죠? 방법은 likelihood로 주어진 H에 대한 D의 확률을 구하면 될 것 같습니다.  

Likelihood는 $$P(D|h) = [\frac{1}{size(h)}]^{N} = [\frac{1}{\left | h \right |}]^{N}$$로 표현이 가능합니다.  
h = "2의 제곱" = {2, 4, 8, 16, 32, 64}라고 하면 size(h) = 5일 것이며, 주어진 Data가 {2, 8, 16, 64}이므로 N = 4가 될 것입니다. N은 h에 속한 D 수입니다.  

예를 들어, D = {16}일 때,  
+ h가 2의 제곱일 likelihood는 $$P(D|h_{two}) = \frac{1}{6}$$  

+ h가 2의 배수일 likelihood는 $$P(D|h_{even}) = \frac{1}{50}$$  

인 것을 알 수 있습니다.

이렇게 쉽게 계산해서 해결할 수 있으며, D를 포함하면서 가장 작은 h가 더 높은 값을 가짐을 확인하였습니다. 하지만 이것에도 문제가 있을 수 있습니다. 무엇이 문제일까요?  

다시 돌아가서 D = {16, 2, 64, 8}이라고 주어졌다고 했을 때, 대부분의 사람들이 "2의 제곱"을 생각할 수 있지만 가설 중 하나의 case인 "32를 제외한 2의 제곱"이라면 어떻게 될까요? "32를 제외한 2의 제곱"의 가설의 확률이 높아지게 됩니다.

이것은 생각만으로도 쉽게 상상할 수 있습니다. 하지만 이것은 굉장히 특이한 case이므로 우리의 생각으로는 확률이 적다고 생각하고 넘어갑니다. 이것 또한 이 문제를 푸는데 굉장이 중요한 key입니다. 이것을 suspicious coincidences(우연의 일치)를 피한다고 말할 수 있습니다. 하지만 likelihood로는 단순히 가설에 대한 확률이므로 "32를 제외한 2의 제곱"의 확률이 더 크게 나올 것입니다. 그래서 여기서 적용한 것이 앞서 말씀드렸던 Prior입니다!

그 가설이 나올 확률을 애초에 작은 값으로 줄이는 것이 Prior입니다. 특이 case의 확률을 줄여서 둘의 곱인 최종 posterior가 적절하게 추론할 수 있도록 하는 것입니다. Prior는 사전 지식으로 미리 알아야 하는 문제가 있기도 합니다. 이번 포스팅은 여기까지만 알아도 문제 없으니 다음에 더 자세히 알도록 하고 prior의 확률분포를 한번 볼까요?  
![Prior_Example_for_Number_Game](https://user-images.githubusercontent.com/61397479/87511761-d9de7500-c6b0-11ea-90a6-53f3202a8b46.jpg)  

특이 case의 확률 값을 굉장히 작게 가진 것을 확인할 수 있죠? 그렇다면 최종 Posterior는 이 둘이 곱이 되겠습니다.  

Posterior의 식: $$P(h|D)$$ ∝ $$p(D|h)$$ x $$p(h)$$  
+ D = {16} 일 때,
![Finally_Posterior](https://user-images.githubusercontent.com/61397479/87512358-d13a6e80-c6b1-11ea-812f-161be6ed80aa.jpg)  

+ D = {2, 8, 16, 64}일 때,
![Posterior_when_D_2,8,16,64](https://user-images.githubusercontent.com/61397479/87512498-15c60a00-c6b2-11ea-9569-4297da57176c.jpg)  

이제 감을 잡으셨으리라 생각합니다. 이러한 방법으로 접근하는 것을 앞서 MAP라고 했습니다. 따라서
$$\hat{h}^{MAP} = \underset{h}{argmax}P(h|D)$$입니다. 하지만 대부분 안에 log 텀을 추가하는데요. 그 이유는 안의 수가 매우 크거나 작을 때 값을 보기가 너무 힘들고 계산하기도 너무 어렵기 때문입니다.

그래서 log 텀을 씌워서 다시 보면  
$$\hat{h}^{MAP} = \underset{h}{argmax}P(D|h)P(h) = \underset{h}{argmax}[logP(D|h) + logP(h)]$$  
입니다.

하지만 Data가 매우 많아서 특이 case를 무시해도 된다면 훨씬 쉽게 계산할 수 있겠죠? 그래서 prior를 빼고 Posterior를 보게 되면,  
$$\hat{h}^{mle}$$ 는 $$\underset{h}{argmax}P(D|h) = \underset{h}{argmax}logP(D|h)$$로 정의하고  
MLE(Maximum liklihood estimate)라고 부릅니다.

---

# MAP vs. MLE  

Data가 많으면 MAP도 MLE처럼 볼 수 있습니다. 그리고 prior가 constant라면 MAP=MLE가 되는 것을 알 수 있습니다.

---

다음 포스팅은 linear regression을 알아보도록 하겠습니다. 읽어주셔서 감사합니다.
