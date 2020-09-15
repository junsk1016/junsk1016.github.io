---
layout: single
title: "Canny Edge Detection 구현 및 Trackbar 사용 - OpenCV(C++)"
categories:
  - OpenCV
classes: wide
tags:
  - jeykll
  - github
  - Minimalmistakes
  - blog
  - OpenCV
---

## OpenCV를 이용해 Canny Edge Detection 구현해보기 + Trackbar로 Val 값 조절

#### Visual Studio 2017을 사용하였습니다.

key : blur, Canny, createTrackbar, setTrackbarPos, getTrackbarPos

### Canny Edge Detection
- Edge 찾기 알고리즘  

- 장점 : 낮은 에러율  
- 단점 : 복잡, 많은 시간  


1. Noise Reduction(노이즈 제거)  
  + 5x5 가우시안 필터를 사용해 노이즈 제거 (smoothing)  

2. Gradient 값이 높은 부분 찾기  
  + sobel 필터(가로, 세로)를 적용 -> 각 방향에서의 gradient 획득  

3. Non-maximum suppresion(최댓값이 아닌 픽셀 0 만들기)  
  + blur된 영상에서의 잘못된 검출 바로잡기  
  + 해당 픽셀(A)의 gradient가 gradient 방향에 있는 두 픽셀의 gradient보다 큰지 확인
  + A가 크면 크대로 유지, A가 작으면 0으로 픽셀 값 변경

4. Hyteresis Thresholding
  + threshold로 minVal, maxVal 값 설정
  + maxVal보다 크면 확실한 edge
  + minVAL보다 작으면 확실히 edge 아님
  + 사이의 값은 픽셀들의 연결구조를 보고 판단
  + why? noise나 다른 변화량은 edge와 연관성이 적으므로


```c++
#include <opencv2/core.hpp>
#include <opencv2/videoio.hpp>
#include <opencv2/highgui.hpp>
#include <opencv2/imgproc.hpp>
#include <iostream>

using namespace cv;
using namespace std;

int main()
{
	Mat frame;
	Mat frame_gray, detect_edge;
	Mat output;

	VideoCapture cam(1);
	if (!cam.isOpened())
	{
		cout << "웹캠을 열 수 없음.\n";
		return -1;
	}

	namedWindow("Canny", WINDOW_AUTOSIZE);

	createTrackbar("Low Threshold", "Canny", 0, 500);
	createTrackbar("High Threshold", "Canny", 0, 500);
  /* createTrackbar( “트랙바의 이름“, “트랙바가 붙어있는 윈도우 이름“,
                 트랙바 최소값. 트랙바 최대값,
                 트랙바가 실행할 때마다 호출되는 콜백함수)*/

	setTrackbarPos("Low Threshold", "Canny", 25);
	setTrackbarPos("High Threshold", "Canny", 40);
  // setTrackbarPos(“트랙바 이름“, “윈도우 이름“, 설정 초기값)

	while (true)
	{
		cam.read(frame);
		imshow("Input_frame", frame);

		cvtColor(frame, frame_gray, COLOR_BGR2GRAY);

		int Low = getTrackbarPos("Low Threshold", "Canny");
		int High = getTrackbarPos("High Threshold", "Canny");

		blur(frame_gray, detect_edge, Size(5, 5));
		Canny(detect_edge, output, Low, High);
    /* Canny(InputArray image, OutputArray edges,
       double threshold1, double threshold2,
       int apertureSize=3, bool L2gradient=false) */

		imshow("Canny", output);

		int key = waitKey(30);
		if (key == 27)
			break;

	}

	destroyAllWindows();
	return 0;
}
```
