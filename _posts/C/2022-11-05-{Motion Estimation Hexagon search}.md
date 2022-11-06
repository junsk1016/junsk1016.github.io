---
layout: single
title: "C - Full search를 사용하여 Motion Estimation 구현해보기"
categories:
  - C
classes: wide
tags:
  - github
  - blog
  - C
  - Visual studio
---

#### 제가 C언어를 공부하면서 배웠던 문제를 공유하겠습니다. ####

#### 이번 post는 motion estimation을 구현해보는 저장하는 문제입니다.  

#### 입력의 lena파일은 512*512 size의 raw 파일입니다.  
#### 파일 입,출력 포스팅에 있는 주석들은 생략하였습니다.  

Motion Estimaton을 구현해보겠습니다. 이전 frame으로 현재 frame을 표현하는 것을 의미하는데요. 방법은 Full search와 Hexagon Search 방법이 있습니다. 이론적인 부분들 다루지 않을게요.

이번 포스팅은 Hexagon search를 사용하는 방법에 대한 코드입니다..!  그 중에서 픽셀 값의 차이를 구하는 방식도 SSD와 SAD가 있는데요. 둘 다 구현은 해보았지만 이전 포스팅 full search에서 SAD로 구현해보았으니 여기서는 SSD로 구현해본 것을 공유드리겠습니다.  

```c

```
