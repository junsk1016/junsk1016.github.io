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
