---
layout: single
title: "Object Detection task 종류 용어 정리"
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

## 딥러닝에서 Object Detection task 종류 용어 정리하기    

## Classification  
+ 입력으로 주어지는 이미지 안의 객체 object 종류를 구분하는 것  

## Localization  
+ 주어진 이미지에서 Object의 위치를 찾아내 위치 정보를 출력하는 것  
+ Bounding Box를 주로 사용하며, top left나 bottom left 좌표를 출력  

## Object Detection  
+ Classification이나 Localization을 동시에 수행하는 것  
+ 단일 object나 multi object detection이 있음  
+ 단순히 localization을 진행하는 것을 일컫기도 함  

## Object Segmentation  
+ Detection을 통해 검출된 object의 형상을 따라 object의 영역을 표시하는 것  
+ 보통 각 pixel을 classification하여 수행  
+ 단순히 전경과 배경을 구분하는 용도로 사용하기도 함  
+ 여기서 아래와 같이 분류하기도 함  
  - Image Segmentation  
    + 이미지의 영역을 분할하여 분할된 영역을 적당한 알고리즘을 사용해 합쳐 object segmentation을 수행  
  - Sementic Segmentation  
    + Object Segmentation을 하되, 같은 class의 object는 같은 영역(색)으로 표시  
  - Instance Segmentation  
    + Object Segmentation을 하되, 같은 class의 object라도 다른 영역(색)으로 표시(=서로 다른 instance를 구분하는 것)    
