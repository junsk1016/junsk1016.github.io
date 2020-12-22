---
layout: single
title: "Feature Matching이란? - OpenCV(C++)"
categories:
  - OpenCV
classes: wide
tags:
  - jeykll
  - github
  - Minimalmistakes
  - blog
  - OpenCV
use_math: true
comments: true
---
## Feature Matching 이론 공부하기  

이전 포스팅에서는 Feature Matching을 코드로 구현해보았습니다.  
하지만 코드와 주석으로만으로 설명이 부족한 것 같아 공부한 것을 공유하고자 합니다.  

1. Feature Matching이란?  

두 개의 이미지에서 동일한 Feature point를 찾아 매칭하는 것. OpenCV에서는 Brute-Force matching 등이 있다.  

Feature Matching이 이루어지는 흐름을 생각해보면, 입력 영상이 주어지고 특징점을 검출 및 Descriptor를 생성하여 매칭을 한다고 보면 됩니다.  

2. 특징점 검출 및 Descriptor  

OpenCV에서는 예전 버전과 현재 사용 버전이 있습니다. 이전 버전에서는 detect(해당 frame에서 찾은 keypoint에 대한 정보를 저장)과 compute(Descriptor를 계산하여 저장)을 따로하였지만 최근 버전에서는 detectAndCompute라는 함수를 통해 한번에 구합니다. keypoint와 descriptor에 대해 헷갈린 점이 많았었는데 간단히 설명하자면 keypoint는 단순히 특징점의 위치에 대한 정보를 가지고 있다고 생각하시면 되고, Descriptor는 특징점 하나하나에 대해 주변 gradient는 어떤지 등 매칭을 위한 정보들을 가지고 있다고 생각하시면 됩니다!

코드에서 사용하는 방식으로는 ORB, BRISK, BRIEF, SIFT, SURF 정도가 있는 것 같습니다. 주의할 점은 방식에 따라 정보 저장의 방법이 다르기 때문에 매칭 계산 방법이 다르다는 것입니다. 앞에서 얘기한 3개(ORB, BRISK, BRIEF)는 Distance를 계산할 때 Hamming distance를 계산하고 나머지 SIFT와 SURF는 L2 distance를 사용한다는 점입니다.

3. Descriptor Matching  

이제 Feature matching을 시작합니다. 이전에 계산한 keypoint.에 대한 Descriptor 매칭된 값을 미리 선언한 matric(ORB의 경우 Hamming)으로 구해서 작은 것끼리 매칭을 합니다. Brute-Force matching의 경우 서로 다른 모든 매칭점에 대해 계산하며, Flann(Fast Library for Approximate Nearest Neighbors) based matching은 인접한 것에 대해서만 계산합니다. 각각 장단점이 존재합니다.

OpenCV에서는 match라는 함수를 통해 매칭한 결과를 3번째 인자인 matches에 저장합니다. 하지만, 경우에 따라서 많은 매칭쌍들이 저장되므로 걸러내는 과정이 필요합니다.

4. Homography란?  

Homography는 다음 포스팅에 올릴 예정이니 여기서 이론에 대해 간략히 알아보겠습니다. Homography란 컴퓨터 비전에서는 한 평면에서 다른 평면으로의 투영 매핑이라고 말할 수 있으며, 3x3 실수 행렬로 표현됩니다.  
4개의 대응되는 점의 좌표 이동 정보가 있으면 Homography 행렬을 구할 수 있습니다. 하지만 feature matching 정보로부터 구하려면 서로 대응되는게 4개보다 많습니다. 그렇기 때문에, 투시 변환 시에 에러가 최솩 되는 형태의 Homography 행렬을 구해야 합니다.  

OpenCV에서는 findHomography()를 사용합니다. 이 함수의 매개 변수 형태는 이전에 구했던 특징점들의 매칭 변수 형태와 다르기 때문에, queryIdx와 trainIdx를 Point2f형태로 변환하여 사용해야 합니다. 그렇게 되면 두 포인트에 대응하는 Homography 행렬의 목록을 만들고 그 중에 가장 잘 대응하는 Homography 행렬을 반환합니다. 이 부분에 대해서는 다음 포스팅에서 코드로 구현해볼 것이니 참고하여 주시길 바랍니다.
