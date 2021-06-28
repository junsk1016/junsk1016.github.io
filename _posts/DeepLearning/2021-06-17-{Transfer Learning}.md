---
layout: single
title: "Transfer Learning이란?"
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

참고 사이트 : https://cs231n.github.io/transfer-learning/  

## Transfer Learning    

실제로, 충분한 크기의 데이터 세트를 갖는 것은 상대적으로 드물기 때문에 (무작위 초기화를 사용하여) 전체 Convolution Network를 처음부터 훈련하는 사람은 거의 없다. 대신, 매우 큰 데이터 세트 (예 : 1000 개의 카테고리가 있는 120만 개의 이미지가 포함된 ImageNet)에서 ConvNet을 pre-training한 다음 사용하고자 하는 task를 위한 initialization 또는 fixed feature extractor로 ConvNet을 사용하는 것이 일반적이다. 세 가지 주요 Transfer Learning 시나리오는 아래와 같다.  

#### ConvNet as fixed feature extractor.  
ImageNet에서 pre-training된 ConvNet을 가져와 마지막 fully-connected layer(이 layer의 출력은 ImageNet과 같은 다른 작업에 대한 1000 개의 클래스 점수임)를 제거한 다음 나머지 ConvNet을 새 데이터 세트의 fixed feature extractor로 취급한다. AlexNet에서 이것은 classfier 직전에 hidden layer의 activation를 포함하는 모든 이미지에 대해 4096-D 벡터를 계산한다. 이러한 기능을 **CNN codes** 라고 한다. 이러한 코드가 ImageNet에서 ConvNet을 학습하는 동안에도 threshold 값이 설정 되어있다면 ReLUd(즉, 0으로 임계 값이 지정됨)는 성능에 중요하다. 모든 이미지에 대해 4096-D 코드를 추출한 후에는 새 데이터 세트에 대해 linear classifier(예 : Linear SVM 또는 Softmax classifier)를 학습시킨다.  

#### Fine-tuning the ConvNet.  
두 번째 전략은 새 데이터 세트에서 ConvNet 위에 classifier를 교체하고 재학습할 뿐만 아니라 backpropagation을 계속하여 pre-trained network의 weight를 미세 조정하는 것이다. ConvNet의 1. 모든 layer를 미세 조정(fine-tuning)하거나 2. overfitting 문제로 인해서 이전 layer 중 일부를 고정하고 네트워크의 일부 higher-level 부분만 미세 조정할 수 있다. 이는 ConvNet의 초기 기능이 많은 작업에 유용해야하는 보다 일반적인 기능(예 : edge detectors or color blob detectors)을 포함하고 있지만 ConvNet의 이후 layer는 원래 데이터 세트에 포함되어 있는 class의 세부 사항에 점점 더 구체적이 된다는 관찰에 의해 동기가 부여되었다. 예를 들어 많은 개 품종을 포함하는 ImageNet의 경우 ConvNet의 표현력의 상당 부분이 개 품종 간 구별에 특화된 기능에 사용될 수 있다.  

#### Pretrained models.  
최신 ConvNet은 ImageNet의 여러 GPU에서 학습하는 데 2-3 주가 걸리기 때문에 사람들이 네트워크를 미세 조정에 사용할 수 있는 다른 사람들의 편의을 위해 최종 ConvNet checkpoints를 릴리스하는 것을 보는 것이 일반적이다. 예를 들어, Caffe 라이브러리에는 사람들이 네트워크 가중치를 공유하는 Model Zoo가 있다.  


### When and how to fine-tune?  
새 데이터 세트에서 수행해야하는 trasfer learning type을 어떻게 결정할까? 이것은 여러 요소를 고려해야 하지만 가장 중요한 두 가지 요소는 새 데이터 세트의 크기(작거나 큰)와 원본 데이터 세트와의 유사성이다. ConvNet 기능은 초기 layer에서는 더 일반적이고 이후 layer에서는 더 원본 데이터 세트에 특화되어 있다는 점을 염두에 두고 다음은, 4 가지 주요 시나리오를 탐색하기 위한 몇 가지 일반적인 common rule이다.  

+ **1. 새 데이터 세트가 작고 원래 데이터 세트와 유사한 경우.** 데이터가 작아 overfitting 문제로 인해 ConvNet을 미세 조정하는 것은 좋지 않다. 또한, 데이터가 원본 데이터와 유사하기 때문에 ConvNet의 higher-level feature도 이 데이터 세트와 관련이있을 것으로 예상한다. 따라서 가장 좋은 아이디어는 CNN code에서 linear classifier를 학습하는 것이다.  

+ **2. 새 데이터 세트가 크고 원래 데이터 세트와 유사한 경우.** 데이터가 더 많기 때문에 전체 네트워크를 통해 미세 조정을 시도해도 overfitting하지 않을 것이라는 확신을 가질 수 있습니다.  

+ **3. 새 데이터 세트가 작고 원래 데이터 세트와 매우 다른 경우.** 데이터가 작기 때문에 linear classfier만 학습하는 것이 가장 좋다. 데이터 세트가 매우 다르기 때문에 더 많은 데이터 세트별 feature를 포함하는 네트워크의 top에서 classifier를 학습시키는 것이 최선이 아닐 수 있다. 대신 네트워크 어딘가의 activation에서 SVM classfifer를 학습시키는 것이 더 효과적 일 수 있습니다.  

+ **4. 새 데이터 세트가 크고 원래 데이터 세트와 매우 다른 경우.** 데이터 세트가 매우 크기 때문에 ConvNet을 처음부터 학습시킬 여유가 있을 것으로 예상할 수 있다. 그러나 실제로는 pre-trained된 모델의 가중치로 초기화하는 것이 여전히 유용할 수 있다. 이 경우 전체 네트워크를 통해 미세 조정할 수 있는 충분한 데이터와 신뢰도를 갖게된다.  

### Practical advice.
Transfer Learning을 수행 할 때 유의해야 할 몇 가지 추가 사항.  

+ **pre-trained model의 제약.** pre-trained 네트워크를 사용하려는 경우 새 데이터 세트에 사용할 수있는 아키텍처 측면에서 약간 제한될 수 있다. 예를 들어 pre-trained 네트워크에서 임의로 Conv 레이어를 제거할 수 없다. 그러나 일부 변경 사항은 간단하다. parameter sharing로 인해 다양한 공간 크기의 이미지에 대해 pre-trained 네트워크를 쉽게 실행할 수 있다. Conv/Pool layer의 경우 forward function가 input volume spatial size와 무관하기 때문이다 (stride가 "적합"되는 경우임). FC layer의 경우 FC layer를 convolution layer로 변환할 수 있기 때문에 여전히 적용된다. 예를 들어 AlexNet에서 첫 번째 FC layer 이전의 최종 pooling volume은 [6x6x512] 크기입니다. 따라서 이 volume을 보는 FC layer은 receptive field size가 6x6이고 padding이 0인 convolution layer를 갖는 것과 동일하다.  

+ **Learning rate.** 새 데이터 세트의 class score를 계산하는 새로운 linear classfier의 가중치(무작위로 초기화)에 비해서 미세 조정중인 ConvNet 가중치는 더 작은 learning rate를 사용하는 것이 일반적이다. 그 이유는 ConvNet 가중치가 상대적으로 좋을 것으로 예상하기 때문에 너무 빨리 그리고 너무 많이 왜곡하는 것을 원하지 않기 때문이다 (특히 그 위에 있는 새로운 Linear Classfifer가 무작위 초기화에서 학습되는 동안).
