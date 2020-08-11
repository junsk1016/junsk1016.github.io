---
layout: single
title: "머신러닝 기본 용어(1) - Machine Learning"
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

###### 이번 포스팅은 머신러닝 기초와 기본 용어에 대해 포스팅하겠습니다.

##### 본 포스팅은 광운대학교 정한울 교수님 머신러닝 과목을 바탕으로 작성을 하였습니다.

## 1. **Machine Learning** 이란 무엇일까??
 + 데이터를 이용하여 습득  
 (Data Mining or Machine Learning)  
 + 과거의 경험(data)을 이용하여 성능을 개선하거나 의미있는 것을 만듦

### --> 불확실한 Data에서 predictive model을 개발하는 것 (input - output 관계)

## 2. 일반적인 Machine Learning의 흐름

![ML-Flowchart](https://user-images.githubusercontent.com/61397479/85912569-51667480-b868-11ea-8a89-427f1868c533.PNG)  

예전에는 사람이 programming했다면, computer에서 구동하며 Machine Learning은 Data만 주고 computer가 programming을 알아서 한다고 생각하시면 됩니다.

여기서 Model은 Function tool, knowledge, program라고 생각하시면 됩니다.

## 3. Machine Learning의 예
 + Spam Filtering
 + Temperature prediction
 + Classifying Flowers
 + AlphaGO  

다음과 같이 스팸 메일을 미리 구별하여 스팸 메일로 보낸다던지, 온도를 예측한다던지, 보여주는 꽃이 어떠한 꽃인지.. 여러 분야에 대해 적용할 수 있습니다. 또한, 잘 알려진 알파고도 머신러닝의 예인데요. 무수히 많은 경우의 수를 구해 가장 최적의 답을 내놓는 알파고 또한 머신러닝의 예입니다!
어떻게 이러한 것들이 가능할까요? 간단하게 설명드리자면 앞서 말한 것과 동일하게 주어진 data를 통해 컴퓨터가 model을 만들어냅니다. 이렇게 되면 만들어진 model로 새로운 data가 들어왔을 때 정답을 내놓을 수 있겠죠?? 이게 바로 머신러닝입니다!

추가적으로, 기존에 머신러닝을 공부하신 분들은 아시겠지만 Learning 방법을 구분하는 여러가지 중 하나로 Supervised learning과 Unsupervised learning이 있지만 이 내용은 다음에 설명하도록 하겠습니다.

## 4. AI? Machine Learning? Deep Learning?  

 많이 들어본 용어들이죠? 이 3가지의 관계는 무엇일까요?
 AI란 Artificial Intelligence로 인공지능이라고 생각하면 되고 Machine Learning은 이번에 계속 포스팅을 통해 배워나갈 예정입니다. Deep Learning은 Machine Learning 포스팅이 모두 끝나면 포스팅할 계획으로 Multi-layer 인공 신경망을 통해 자가 발전하는 것입니다!

 ![ML-Relationship](https://user-images.githubusercontent.com/61397479/85913173-57ab1f80-b86d-11ea-8eb7-4e024610a1c5.PNG)  

 감을 잡으신 분들도 계시겠지만 말한 순서대로 포함이 된다고 보시면 되는데요. AI의 분야에 ML(Machine Learning)이 있고 ML의 분야 안에 Deep Learning이 있다고 생각하시면 수월합니다!  

 즉, **Deep Learning $\subset$ Machine Learning $\subset$ AI** 입니다.

## 5. Artificial Neural Network

![Neural](https://user-images.githubusercontent.com/61397479/85987539-b1a41480-ba28-11ea-9100-57318eb8a34a.png)  

인공신경망의 아이디어는 뉴런-시냅스에서 가져왔다고 합니다. 받는 신호들로부터의 계산된 합이 일정한 값 이상이면 출력이 되고 이하면 출력되지 않는 것을 가져와 오른족 그림처럼 신호를 x, 그에 따른 가중치를 w라고 했을 때 Sum 값이 어떠한 함수(추후엔 activation function이라고 하겠습니다)를 통해 출력으로 나가는 것으로 구현했다고 볼 수 있습니다.

![Artificial Nerual Net2](https://user-images.githubusercontent.com/61397479/85988179-a2719680-ba29-11ea-868e-bc2ca9ecb238.png)  

이런식으로 딥러닝이 나왔다고도 하는데 딥러닝 관련해서는 머신러닝 포스팅이 끝나고 포스팅하겠습니다!

다음 포스팅은 이어서 머신러닝의 종류와 확률의 기초 등 이번 포스팅과 더불어 기본을 다지는 시간을 가지도록 하겠습니다.
