---
layout: single
title: "Openpose를 Windows에서 빌드하여 돌려보기"
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

###### 이번 포스팅은 Openpose를 빌드하고 돌려보는 것 포스팅하겠습니다.

## 1. Openpose?
 + 인체 관절을 추출하는 Human Pose Estimation  
 + 논문 제목
  - Real-time multi-person keypoint detection library for body, face, hands, and foot estimation
 + Git 주소
https://github.com/CMU-Perceptual-Computing-Lab/openpose  


## 2. 빌드 환경

+ Windows 10 Pro  
+ GeForce RTX 2060 SUPER  
+ CUDA 10.2

## 3. 가상환경 만들기/접속  

```
conda create -n openpose
conda activate openpose
```  

## 4. openpose 설치    
https://github.com/CMU-Perceptual-Computing-Lab/openpose/blob/master/doc/installation/0_index.md#windows-portable-demo  
위 링크에서 `Windows Portable Demo`를 찾습니다. 그 방법대로 진행하면 됩니다.
https://github.com/CMU-Perceptual-Computing-Lab/openpose/releases 들어가서 gpu 버전으로 설치 후 압축 풀기
 - 저의 경우 압축을 푼 openpose를 꺼내놓았습니다.
```
cd openpose
```

## 5. weight 받기
다른 사람이 블로그에 weight를 친절히 올려놓아서 들어가서 설치합니다.  
http://daddynkidsmakers.blogspot.com/2020/07/openpose.html  

```
위 링크 들어가서 다운받아야 할 것
 - BODY_25 - pose_iter_584000.caffemodel
 - COCO - pose_iter_440000.caffemodel
 - Hand - pose_iter_102000.caffemodel
```

## 6. 다운받은 weight 적용시키기  
4번에서 다운받은 openpose에 들어가서 /models 에 들어갑니다. 그 후 hand 폴더에 Hand weight 받은거를 넣고,
pose 폴더에서 body_25 폴더에는 BODY_25 weight를, coco 폴더에는 COCO weight를 넣습니다.

## 7. demo 돌리기

```
:: Windows - Portable Demo
bin\OpenPoseDemo.exe
bin\OpenPoseDemo.exe --camera 0
bin\OpenPoseDemo.exe --camera 1

hand를 사용할거면 뒤에 --hand를 넣으시면 되고 face까지 하고 싶으면 face weight를 다운 받아 폴더에 넣고 돌리면 됩니다.
```
