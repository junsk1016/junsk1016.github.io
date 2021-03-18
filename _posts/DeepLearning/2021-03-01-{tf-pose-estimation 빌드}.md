---
layout: single
title: "TF-pose-estimation 빌드하여 돌려보기 - Machine Learning"
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

###### 이번 포스팅은 TF-pose-estimation을 빌드하고 돌려보는 것 포스팅하겠습니다.

## 1. Tf-pose-estimation?
 + Openpose를 tensorflow로 바꾼 것  
 + 처음 올라온 것은 NOT Found가 떠서 다른 사람이 tensorflow2로 변형한 것을 빌드하였습니다.

### https://github.com/gsethi2409/tf-pose-estimation
위 링크에서 git clone을 하였습니다.  

## 2. 빌드 환경

+ Ubuntu 18.04
+ GeForce GTX 1080
+ CUDA 10.1
+ Cudnn 7.6
+ NVIDIA Driver-418
+ Tensorflow-gpu 2.1.0

## 3. 가상환경 만들기/접속  

```
conda create -n tfpose python=3.6.8
conda activate tfpose
```  

## 4. tensowflow-gpu 설치  

```
conda install tensorflow-gpu=2.1.0
```

## 5. opencv 설치
이 부분은 제가 좀 복잡하게 해서 결과론적으로는 opencv-python을 설치하면 되는데 제가 했던 것까지 다 보여드릴게요.

```
conda install opencv
conda install --channel httpas://conda.anaconda.org/monpo opencv3
conda uninstall opencv3
conda install opencv-python
```

## 6. git clone 및 requirement 설치

```
git clone https://github.com/gsethi2409/tf-pose-estimation.git
cd ./tf-pose-estimation
pip install -r requirements.txt
pip install tf_slim
cd ./tf_pose/pafprocess
sudo apt install swig
siwg -python -c++ pafprocess.i && python3 setup.py build_ext --inplace
```

## 7. 파일 수정

tf_pose 폴더에 있는 estimator.py에서 15번째 줄에 추가
`tf.compat.v1.disable_eager_execution()`

## 8. 데모 돌리기
경로는 tf-pose-estimation에 가있어야 합니다. run.py를 돌려야 하니까요.

이미지
`python run.py --model=mobilenet_thin --resize=432x368 --image./imnages/p2.jpg`

비디오
`python run_video.py --model=mobilenet_thin --resize=432x368 --video=test.mp4`

웹캠
`python run_webcam.py`

뒤에 붙는 것들은 조정할 수 있으니까 바꿔가면서 하셔도 되고 웹캠처럼 파일만 바로 돌리면 default로 돌아갑니다.
