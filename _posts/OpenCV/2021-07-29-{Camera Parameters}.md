---
layout: single
title: "Camera Parameters - Extrinsics & Intrinsics"
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

## Camera parameters 공부하기    

<https://junsk1016.github.io/opencv/Calibration-%EC%9D%B4%EB%A1%A0/>  

가볍게 공부하고 싶다면 위 링크를 참고하시길 바랍니다. 본 포스팅은 조금 디테일하게 다루었습니다.  

공부는 Cyrill Stachniss 교수님의 강의를 참고하였습니다.  

# Camera Parameters  
### Goal : Describe How a 3D Point is Mapped to a 2D Pixel Coordinate  
#### Coordinate System  
+ 1. World/Object coordinate system : $S_{o} = \begin{bmatrix} X & Y & Z \end{bmatrix}^{T}$  
+ 2. Camera coordinate system : $S_{k} = \begin{bmatrix} ^{k}\textrm{X} & ^{k}\textrm{Y} & ^{k}\textrm{Z} \end{bmatrix}^{T}$  
+ 3. Image (plane) coordinate system : $S_{c} = \begin{bmatrix} ^{c}\textrm{x} & ^{c}\textrm{y} \end{bmatrix}^{T}$  
+ 4. Sensor coordinate system : $S_{s} = \begin{bmatrix} ^{s}\textrm{x} & ^{s}\textrm{y} \end{bmatrix}^{T}$  

Coordinate system은 위와 같이 4개로 표현할 수 있으며 최종적으로 sensor system의 좌표를 표현하면 아래와 같습니다.  

$\begin{bmatrix} ^{s}\textrm{x}\\ ^{s}\textrm{y}\\ 1\end{bmatrix}$ = $^{s}\textrm{H}_{c}$ $^{c}\textrm{P}_{k}$ $^{k}\textrm{H}_{o}$ $\begin{bmatrix} X \\ Y \\ Z \\ 1 \end{bmatrix}$  

+ $\begin{bmatrix} ^{s}\textrm{x}\\ ^{s}\textrm{y}\\ 1\end{bmatrix}$ : in the sensor system  
+ $^{s}\textrm{H}_{c}$ : image plane to sensor  
+ $^{c}\textrm{P}_{k}$ : camera to image  
+ $^{k}\textrm{H}_{o}$ : object to camera  
+ $\begin{bmatrix} X \\ Y \\ Z \\ 1 \end{bmatrix}$ : in the object system  

### World to the Sensor 로의 그림  

<p align="center"><img src="/img/Camera-Parameters-FIg1.jpg"></p>  

+ Extrinsic : 카메라 외부에서 일어나는 것  
  - 카메라는 어디에?, 카메라를 어디로 이동? 등  

+ Intrinsic : 카메라 내부에서 발생  
  - Sensor가 prediction centor 뒤에 장착되는 방식 등  

이제 하나씩 알아보겠습니다.  

## 1. Extrinsic Parameter ( [o] -> [k] )  

우리가 변경할 수 있는 것은 Translate 와 Rotate입니다. 이것들은 총 6개의 파라미터(3개의 position과 3개의 heading)로 구성이 되어있고 이 6개로 내 카메라의 위치와 방향을 의미하는 Pose(position and heading)을 설명하여야 합니다.  

먼저 notation을 정의하여야 합니다.  World 좌표를 $\textbf{\textit{X}}_{p}$ = $\begin{bmatrix} \textit{X}_{p} & \textit{Y}_{p} & \textit{Z}_{p} \end{bmatrix}^{T}$ 로 정의하고 Projection 좌표를 $\textbf{\textit{X}}_{O}$ = $\begin{bmatrix} \textit{X}_{O} & \textit{Y}_{O} & \textit{Z}_{O} \end{bmatrix}^{T}$ 로 정의하겠습니다. 여기서 Projection 좌표는 카메라 좌표계의 원점이라고 보시면 됩니다.  

그렇다면 World coordinate와 Origin coordinate 사이의 translation은 아래와 같이 표현됩니다. 식의 유도는 하지 않겠습니다.

$^{k}\textbf{\textit{X}}_{p}$ = $R$ ( $\textbf{\textit{X}}_{p}$ $-$ $\textbf{\textit{X}}_{O}$ )  

위 식은 Euclidian coordinate를 표현한 것이고 Homogeneous coordinate로 표현하면 아래와 같습니다.  

$\begin{bmatrix} ^{k}\textbf{\textit{X}}_{p}\\ 1 \end{bmatrix} = \begin{bmatrix} R & 0\\ 0^{T} & 1 \end{bmatrix} \begin{bmatrix} I_{3} & -\textbf{\textit{X}}_{O} \\ 0^{T} & 1 \end{bmatrix} \begin{bmatrix} \textbf{\textit{X}}_{p}\\ 1 \end{bmatrix} = \begin{bmatrix} R & -R\textbf{\textit{X}}_{O} \\ 0_{T} & 1 \end{bmatrix} \begin{bmatrix} \textbf{\textit{X}}_{p} \\ 1 \end{bmatrix}$  

따라서 $^{k}\textbf{\textit{X}}_{p}$ = $^{k}H$ $\textbf{\textit{X}}_{p}$ 이며, 3D 좌표가 카메라 좌표의 3D 좌표로 표현이 가능하다.  

## 2. Intrinsic Parameters ([k]-[c]=[s]=[s])  
카메라 내부에서 발생하는 파라미터이며, World에서 카메라를 이동,회전해도 영향을 받지 않습니다. 즉, Camera 좌표에서 Sensor 좌표로 projection되는 과정이라고 생각하시면 됩니다.  
+ Invertile transformation : image plane to sensor, model deviations  
+ Not invertible transformation : central projection  

먼저 Mapping을 3 step로 나누어 관찰합니다.  
1. Ideal perspective projection to the image plane  
2. Mapping to the sensor coordinate system("where the pixel are")  
3. Compensation for the fact that the two previous mappings are idealized  

첫 가정은 Ideal Perspective Projection입니다.  
+ Lens 왜곡 없음, 모든 ray는 직선이며 카메라의 투영 중심을 통과  
+ 핀홀은 카메라의 원점의 위치로 정의  
+ 주점의 초점은 광축에 놓이며, 점과 이미지 평면까지의 거리는 일정(상수)  

### Image Coordinate System ([k]-[c])
우선 저희가 생각의 변환이 필요합니다. 그림을 참고하여 보면 실제로는 좌측의 그림이지만 Image Sensor가 180도 뒤집어져 있어서 우리가 보기에 불편하므로 우측 그림처럼 보고 Image가 뒤집어지지 않도록 한다고 합니다.  

<p align="center"><img src="/img/Camera-Parameters-FIg2.jpg"></p>  
[출처 : Cyrill Stachniss 강의]  

(추가적으로 카메라 좌표계의 원점과 Image plane의 Z축 차이를 camera constant c라고 합니다.)  

따라서 Ideal Perspective Projection 은 아래와 같이 표현할 수 있습니다.  
+ $^{c}x_{\bar{p}}$ := $^{k}\textbf{\textit{X}}_{p}$ = $c\frac{^{k}\textbf{\textit{X}}_{p}}{^{k}\textbf{\textit{Z}}_{p}}$  

+ $^{c}y_{\bar{p}}$ := $^{k}\textbf{\textit{Y}}_{p}$ = $c\frac{^{k}\textbf{\textit{Y}}_{p}}{^{k}\textbf{\textit{Z}}_{p}}$  

+ $c$ = $^{k}\textbf{\textit{Z}}_{p}$ = $\frac{^{k}\textbf{\textit{Z}}_{p}}{^{k}\textbf{\textit{Z}}_{p}}$  

여기서 P바는 카메라 좌표계라고 보시면 됩니다. 단순히 3D 좌표의 Z 좌표를 구하고 Camera Constant를 얻을 수 있으며, 3D coordinate를 Image plane으로 가져올 수 있습니다.  

Extrinsic과 마찬가지로 Homogeneous coordinate로 표현하면 아래와 같습니다.  

$^{c}\textbf{x}_{\bar{p}} = \begin{bmatrix} ^{c}u_{\bar{p}} \\ ^{c}v_{\bar{p}} \\ ^{c}w_{\bar{p}} \end{bmatrix} = \begin{bmatrix} c & 0 & 0 & 0\\ 0 & c & 0 & 0 \\ 0 & 0 & 1 & 0 \end{bmatrix} \begin{bmatrix} ^{k}\textbf{\textit{X}}_{p} \\ ^{k}\textbf{\textit{Y}}_{p} \\ ^{k}\textbf{\textit{Z}}_{p}\\ 1 \end{bmatrix} = \begin{bmatrix} c ^{k}\textbf{\textit{X}}_{p}\\ c ^{k}\textbf{\textit{Y}}_{p}\\ ^{k}\textbf{\textit{Z}}_{p} \end{bmatrix}$  이며 정리하면 아래와 같습니다.  

$^{c}\textbf{x}_{\bar{p}}$ = $^{c}\textbf{P}_{\bar{p}}$ $^{k}\textbf{X}_{\bar{p}}$ 이고, $^{c}\textbf{P}_{\bar{p}}$ = $ \begin{bmatrix} c & 0 & 0 & 0 \\ 0 & c & 0 & 0 \\ 0 & 0 & 1 & 0 \end{bmatrix}$  

그러면 Extrinsic과 합쳐서 $^{c}\textbf{x}$ = $^{c}P\textbf{X}$ 로 표현할 수 있다. ( $^{c}P = ^{c}P_{k}$ $^{k}H$ )

### Calibration Matrix  
Ideal camera에서 Calibration matrix 는 $^{c}K$ = $\begin{bmatrix} c & 0 & 0\\ 0 & c & 0\\ 0 & 0 & 1 \end{bmatrix}$  다. 따라서 Overall matrix는 아래와 같다.  

$^{c}P$ = $^{c}K$ $[R| - R\textbf{\textit{X}}_{O}]$ = $^{c}KR[I_{3} - \textbf{\textit{X}}_{O}]$. ( $[I_{3} - \textbf{\textit{X}}_{O}]$ 는 3x4 matrix)  

이것을 Image plane으로 표현하면
$\begin{bmatrix} ^{c}u^{'}\\ ^{c}v^{'}\\ ^{c}w^{'} \end{bmatrix} = \begin{bmatrix} c & 0 & 0\\ 0 & c & 0\\ 0 & 0 & 1 \end{bmatrix} \begin{bmatrix} r_{11} & r_{12} & r_{13} \\ r_{21} & r_{22} & r_{23} \\ r_{31} & r_{32} & r_{33} \end{bmatrix}\begin{bmatrix} X-X_{O}\\ Y-Y_{O}\\ Z-Z_{O} \end{bmatrix}$  이고 1/2를 곱하면 Euclidian coordinate에서 어떻게 보이는 지 알 수 있다.

Image coordin 에서 collinearity equation은 아래와 같다.  

<p align="center"><img src="/img/Camera-Parameters-FIg3.jpg"></p>  
[출처 : Cyrill Stachniss 강의]  

### Mapping to the Sensor (assuming linear errors) ([c]=[s])  

이제 image plane에서 sensor로의 mapping을 알아봅시다. 실제로 image sensor의 원점과 pricipal point는 다릅니다. 따라서 shift compensation이 필요합니다. 또한 x와 y의 scale 차이입니다. 이 차이를 m이라고 합시다. 또한 sheer compensation이라고 하는 디지털 카메라에는 적용되지 않고 아날로그 카메라에서만 적용되는 파라미터가 있습니다. 따라서 [c]에서 [s]로 가는 변환은 아래와 같이 정의될 수 있습니다.  

$^{s}H_{c}$ = $\begin{bmatrix} 1 & s & x_{H}\\ 0 & 1+m & y_{H}\\ 0 & 0 & 1 \end{bmatrix}$  . 총 4개의 파라미터를 가지고 있습니다.

+ $x_{H}, y_{H}$ : principal point  
+ $s$ : sheer compensation  
+ $m$ : scale difference  

따라서 최종적으로 $^{s}\textbf{x}$ = $^{s}H_{c}$ $^{c}K$ $R[I_{3}| - \textbf{X}_{O}]\textbf{X}$ 입니다.

종종, $^{s}H_{c}$ 은 Calibration matrix $^{c}K$ 와 결합하여 $K$ 로 사용하기도 합니다.  

$K$ = $^{s}H_{c}$ $^{c}K$ = $\begin{bmatrix} c & cs & x_{H}\\ 0 & c(1+m) & y_{H}\\ 0 & 0 & 1 \end{bmatrix}$  

그리고 이 $K$ 를 affine transformation 이라고 합니다. 총 5개의 파라미터를 가지고 있는 것을 확인할 수 있습니다.  

###  DLT : Direct Linear Transform  

먼저 저희는 linear 만 고려를 한다고 하였습니다. 여기까지를 정리한다고 하면 $\textbf{x}$ = $P\textbf{X}$ 으로의 변환에서 P = $KR[I_{3}| - \textbf{X}_{O}]$ 이고 여기서의 $K$ 는 바로 위의 affine transformation이 되겠습니다. 이것만을 고려한다고 했을 때, Affine transformation에서 5개의 파라미터, 그 뒤의 항에서 6개의 파라미터를 가지고 있으므로 총 11개의 파라미터를 가지고 접근을 합니다. 이러한 것을 DLT라고도 한다고 하는데 자세한 내용은 정확히는 잘 모르겠습니다.. 아무튼 이 P를 homogeneous projection matrix로 표현하면 $P$ = $KR[I_{3}| - \textbf{X}_{O}] $가 되겠고 파라미터는 아래와 같습니다.  
+ 6 extrinsics parameters : $R$ (3개), $\textbf{X}_{O}$ (3개)  
+ 5 intrinsics parameters : $c$ , $x_{H}$ , $y_{H}$ , $m$ , $s$  

<p align="center"><img src="/img/Camera-Parameters-FIg4.jpg"></p>  
[출처 : Cyrill Stachniss 강의]  

* P는 3x4 matrix 인데 파라미터가 12개가 아니고 11개입니다. -> Homogeneous라서 정보를 버리기 때문인 것을 알 수 있습니다. 즉 11개로 P행렬의 모든 element를 정의 가능합니다.  

### Non-linear Errors ([s]=[s])  

이전까지는 linear error만 고려(=DLT)한다고 말하였습니다. 하지만 Real World는 non-linear인 것은 모두가 아는 사실입니다. 예를 들면, 광각 렌즈의 경우 직선이 곡선으로 보이는 대표적인 것이 있으며 sensor의 평면도 생산 과정에서 평면이 아닐 수 있습니다.  

그래서 General mapping으로 아래와 같이 정의합니다.  
+ Idea는 non-linear effect를 보상하는 것  
+ 각 픽셀마다 개별적으로 shift (굴곡량이 다르기 때문에)  
+ $^{a}x$ = $^{s}x + \Delta x(\boldsymbol{x},\boldsymbol{q})$  
+ $^{a}y$ = $^{s}y + \Delta y(\boldsymbol{x},\boldsymbol{q})$
으로 정의하고 $\Delta$ 안의 $\boldsymbol{x}$ 는 on the localization in the image를 의미하며 $\boldsymbol{p}$ 는 non-linear parameter 입니다.  

이 식 또한 homogeneous coordinate로의 maapping은 아래와 같습니다.  

$^{a}\textbf{x}$ = $^{a}H_{s}(\boldsymbol{x})$ $^{s}\textbf{x}$  , $^{a}H_{s}(\boldsymbol{x})$ = $\begin{bmatrix}1 & 0 & \Delta x(\boldsymbol{x},\boldsymbol{q})\\ 0 & 1 & \Delta y(\boldsymbol{x},\boldsymbol{q})\\ 0 & 0 & 1 \end{bmatrix}$ 입니다.

이렇게 되면 전체 mapping은 아래와 같습니다.  

$^{a}\textbf{x}$ = $^{a}H_{s}(\boldsymbol{x})$ $KR[I_{3}| - \textbf{X}_{O}]\textbf{X}$ .

또 여기서 $^{a}H_{s}(\boldsymbol{x})$ $K$ 를 하나로 합쳐서  
$^{a}K_{s}(\boldsymbol{x}, \boldsymbol{p})$ 라고 정의하고 Ceneral Calibration Matrix라고 합니다.  

$^{a}K_{s}(\boldsymbol{x}, \boldsymbol{p})$ = $^{a}H_{s}(\boldsymbol{x})$ $K$ = $\begin{bmatrix}c & cs & x_{H} + \Delta x(\boldsymbol{x},\boldsymbol{q})\\ 0 & c(1+m) & y_{H} +  \Delta y(\boldsymbol{x},\boldsymbol{q})\\ 0 & 0 & 1 \end{bmatrix}$ 이고 정리하면,  

이를 최종으로 정리하면

$^{a}\textbf{x}$ = $^{a}P(\boldsymbol{x}, \boldsymbol{p})X$ , $P$ = $^{a}K(\boldsymbol{x}, \boldsymbol{p})R[I_{3}|-X_{O}]$ 입니다.

최종 식은 알았지만 아직 $\Delta x$ 와 $\Delta y$ 가 뭔지 모릅니다. 이 파라미터는 $^{a}H_s(x)$ 에 있는 파라미터이기 때문에 $^{a}H_s(x)$ 를 modeling 해야 합니다.  

Barrel Distortion 을 예로 자세히 알아봅시다. 이미지의 중심에서 멀이질수록 왜곡이 심한 특징을 가지고 있는 왜곡입니다. 식은 아래와 같습니다.  
+ $^{a}x$ = $x(1 + q_{1}r^{2} + q_{2}r^{4} )$  
+ $^{a}y$ = $y(1 + q_{1}r^{2} + q_{2}r^{4} )$  
  - $[x, y]^{T}$ : ideal pin-hole camera에서의 projection point  
  - $r$ : image의 pixel과 principal point의 거리
  - $q_{1}, q_{2}$ : general mapp]ing의 parameter  


이제 여태까지 내려온 모든 것을 정리하면 Mapping 은 2가지 step으로 이루어진 것을 알 수 있습니다.  
+ 1. Projection of the affine camera : $^{s}\textbf{x}$ = $P\textbf{X}$  
+ 2. Consideration of non-linear effects : $^{a}\textbf{x}$ = $^{a}H_{s}(\boldsymbol{x})$ $^{s}\textbf{x}$  


## Inversion of the Mapping  
이제 카메라의 파라미터를 모두 알았으니 실제로 우리가 궁금한 2D에서 3D로의 좌표 mapping해봅시다. 물론 정보를 2D로 오면서 정보를 잃었으니 100% 정확하게 알 수는 없습니다. 2가지 step으로 나눠 차근차근 구해봅시다.  

1. Step 1 : $^{a}\textbf{x}$ $\rightarrow $ $^{s}\textbf{x}$  
2. Step 2 : $^{s}\textbf{x}$ $\rightarrow $ $\textbf{X}$  

Step 1에서의 문제는 $^{a}H_{s}(\boldsymbol{x})$ 에서 괄호 안의 $\boldsymbol{x}$ 를 알 수 없다는 점입니다. $\boldsymbol{x}$ 가 수정된 image에만 있기 때문입니다. 따라서 반복 작업으로 찾습니다. 먼저 $^{a}\textbf{x}$ = $^{a}H_{s}(\boldsymbol{x})$ $^{s}\textbf{x}$ 에서 역행렬을 곱해 $^{s}\textbf{x}$ = $[^{a}H_{s}(\boldsymbol{^{a}x})]^{-1}$ $^{a}\textbf{x}$ 에서 $\boldsymbol{^{a}x}$ 를 랜덤으로 초기화 후 iteration을 돌면서 업데이트한 후 가장 최적화된 $^{s}\textbf{x}$를 찾습니다.  

Step 2의 경우는 Inversion of the projective mapping 이라고 합니다. 정의되었던 식을 다시 한 번 가져와보겠습니다. 앞의 람다는 scalar vector라고 보시면 됩니다. 그리고 P는 저희가 알고 있습니다.

$\lambda \mathbf{x} = P\mathbf{X} = KR[I_{3}| -\boldsymbol{X}_{O}]\boldsymbol{X} = [KR| - KR\boldsymbol{X}_{O}]\begin{bmatrix}\boldsymbol{X} \\ 1 \end{bmatrix} = KR\boldsymbol{X} - KR\boldsymbol{X}_{O}$  

여기서 우리는 $\boldsymbol{X}$ 를 알기 원하므로 역행렬을 곱해 $\boldsymbol{X}$ = $\boldsymbol{X}_{O}$ + $\lambda (KR)^{-1}\boldsymbol{x}$ 에서 구하게 됩니다. notation을 다시 한 번 정리해보겠습니다.  

+ $\boldsymbol{X}$ : Euclidian coordinate (X,Y,Z)  
+ $\boldsymbol{X}_{O}$ : Camear Coordinate System  
+ $KR^{-1}$ : Calibration + Rotation matrix의 inverse (3x3 matrix)
+ $\boldsymbol{x}$ : vector(방향), pixel information  

* $\lambda (KR)^{-1}\boldsymbol{x}$ : 카메라 원점 $\boldsymbol{X}_{O}$ 에서 3D point $\boldsymbol{X}$ 까지의 ray 방향이므로 $\lambda$ 는 그 점이 얼마나 멀리 떨어져 있는지를 나타낸다고 보시면 됩니다. 하지만 변수로 표현된 이유는 손실된 정보로 인해서 복원해도 모르기 때문입니다.  

## Classification of Cameras  

<p align="center"><img src="/img/Camera-Parameters-FIg5.jpg"></p>  

각 카메라마다 파라미터 수는 아래와 같이 다릅니다.  

<p align="center"><img src="/img/Camera-Parameters-FIg6.jpg"></p>  
[출처 : Cyrill Stachniss 강의]  


## Calibrated Camera  
+ Intrinsis parameter를 모를 때, Camera가 Uncalibrated 라고 말합니다.  
+ Intrinsis parameter를 알 때, Camera가 Calibrated 라고 말합니다.  
+ Intrinsis parameter를 얻는 과정을 camera calibration이라고 부릅니다.  

## Summary  

<p align="center"><img src="/img/Camera-Parameters-FIg7.jpg"></p>  
