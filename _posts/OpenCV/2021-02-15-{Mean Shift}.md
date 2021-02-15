---
layout: single
title: "Mean Shift - OpenCV(C++)"
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

## Mean Shift 해보기  

#### Visual Studio 2017을 사용하였습니다.  

Mean Shift 이론에 대한 내용보다는 코딩 관련에 집중하여 포스팅하겠습니다.

##### 공부한 곳  
<https://darkpgmr.tistory.com/64>  

## 1. Mean Shift?  
### [방법론적 이론]  
#### Hill Climb 탐색 방법
+ 가장 가까운 정상을 찾는 것  
+ `Mean Shift`, `Gradient Descent Search`, `Blind Search`, `Greedy Search` 등  

#### Mean Shift
+ 어떤 데이터 분포의 peak 또는 무게 중심을 찾는 한 방법  
+ 현재 자신의 주변에서 가장 데이터가 밀집된 방향으로 이동  
  - 언젠가는 분포 중심을 찾음  
+ 알고리즘  
  - 탐색 반경 : r  
  - 현재 위치에서 반경 r 이내에 들어오는 데이터 구함  
  - 이들의 무게 중심의 좌표로 현재 위치 이동  
  - 위 과정을 위치 변화가 없을 때까지 반복(수렴할 때까지)  
+ 단점  
  - local minimum에 빠지기 쉬움  
  - 탐색 반경의 설정 필요  
    + 너무 크게 잡으면 정확한 피크(peak)를 찾지 못함  
    + 너무 작게 잡으면 local minimum에 쉽게 빠짐  
  - 탐색 윈도우의 크기를 정하는 것이 쉽지 않음  
+ 활용  
  - Object Tracking, Image Segmentation, Data Clustering, Image Smoothing  

### [Mean Shift를 이용한 영상 추적 방법]
+ Comaniciu, Ramesh, Meer, "Real-time tracking of non-rigid objects using mean shift", CVPR 2000  
#### 아이디어  
+ 추적하고자 하는 대상 물체에 대한 색상 히스토그램(histogram)과 현재 입력 영상의 히스토그램을 비교해서 가장 유사한 히스토그램을 갖는 윈도우 영역을 찾는 것  
+ 제한점? 모든 가능한 윈도우 위치에 대해 각 히스토그램을 구하고 비교를 하기 때문에 오랜 시간 소요  
#### Histogram Backprojection 기법과 Mean Shift을 결합한 방법 제안  
+ 영상에서 추적할 대상 정하기  
  - 해당 윈도우 영역에 대하여 히스토그램 구한 후 객체 모델로 저장  
+ Histogram Backprojection을 이용해 입력 영상의 픽셀 값들을 확률 값으로 변경  
+ 구한 확률 값 분포에 대해 Mean Shift 적용하여 물체의 위치 찾기  
  - 물체의 크기 변화까지 따라가고 싶으면 찾아진 위치에서 윈도우의 크기를 조절해 가면서 저장된 모델과 가장 히스토그램 유사도가 큰 스케일(scale) 선택  

##### Histogram Backprojection ?
+ 입력 이미지와 같은 크기이지만 하나의 채널만 가지는 이미지 생성하는 방법  
+ 이 이미지의 픽셀은 특정 오브젝트에 속할 확률을 의미  
+ 관심 오브젝트 영역에 속한 픽셀이 나머지 부분보다 더 흰색으로 표현  

#### Mean Shift 적용  
+ Histogram Backprojection을 통해 얻은 값을 일종의 확률값으로 생각 후 Mean shift에 적용  

## 2. 코드에 사용된 함수    
### 마우스 이벤트 제어  

#### cv::setMouseCallback(const string& winname, MouseCallback onMouse, void*userdata = 0)  
사용자가 정의한 마우스 콜백 함수를 시스템에 등록하는 함수    
+ string& winname : 이벤트 발생을 체크할 윈도우 이름    
+ MouseCallback onMouse :  마우스 이벤트를 처리하는 콜백 함수 이름(함수 포인터)  
+ void* userdata : 이벤트 처리 함수로 전달할 추가적인 사용자 정의 인수  

#### Typedef void(*MouseCallback) (int event, int x, int y, int flags, void* userdata)  
발생한 마우스 이벤트에 대해서 처리 및 제어를 구현하는 콜백 함수  
setMouseCallback() 함수의 두 번째 인수(함수 포인터)의 구현 부이기 때문에 함수명이 인수명과 같아야 함  
typedef를 통해서 함수포인터로 정의되어 있어 인수의 구조(인수의 데이터 타입, 인수의 순서)를 유지해야 함  
+ Int event : 발생한 마우스 이벤트의 종류  
+ Int x, int y : 이벤트 발생 시 마우스 포인터의 x, y 좌표  
+ Int flags : 마우스 버튼과 동시에 특수 키(shift, alt, ctrl) 키가 눌러졌는지 여부 확인  
+ Void *userdata : 콜백 함수로 전달되는 추가적인 사용자 정의 인수  

<center>![Flag](/img/Flag.PNG)</center>  

<center>![마우스](/img/마우스.PNG)</center>  
---

## 코드  

```c++
#include <opencv2/opencv.hpp>
#include <iostream>

using namespace std;

// 아래 줄을 주석처리하면 비디오 영상에 대해 동작
//#define WEBCAM

bool mouse_is_pressing = false;
int start_x, start_y, end_x, end_y;	// ROI 좌표
int step = 0; // mouse_callback
cv::Mat img_color;
cv::Rect roi;

void swap(int* v1, int* v2) {	// v1과 v2를 바꾸기
	int temp = *v1;
	*v1 = *v2;
	*v2 = temp;
}

void mouse_callback(int event, int x, int y, int flags, void* userdata)
{
	cv::Mat img_result = img_color.clone();

	if (event == cv::EVENT_LBUTTONDOWN) {	// 왼쪽 버튼 누름
		step = 1;

		mouse_is_pressing = true;	// 마우스 눌렀다고 정의
		start_x = x;
		start_y = y;
	}
	else if (event == cv::EVENT_MOUSEMOVE) { // 마우스 움직임
		if (mouse_is_pressing) {// 마우스 누른 상태

			end_x = x;
			end_y = y;

			step = 2;
		}
	}
	else if (event == cv::EVENT_LBUTTONUP) { // 왼쪽 버튼 떼기

		mouse_is_pressing = false; // 마우스 땠다고 정의

		end_x = x;
		end_y = y;

		step = 3;
	}
}


int main()
{
	cv::Mat img_hsv, img_mask, img_ROI;
	cv::Mat objectHistogram;

	int channels[] = { 0 };
	int hsize[] = { 64 };			// 히스토그램 size
	float range1[] = { 0, 180 };	// 히스토그램 range
	const float* histRange[] = { range1 };

#ifdef WEBCAM // WEBCAM일 때 연결
	cv::VideoCapture cap(0);
#else		  // WEBCAM이 아닐 때 저장된 영상 로드
	cv::VideoCapture cap("Basketball.mp4");
#endif

	if (!cap.isOpened()) {
		cerr << "video 에러 - 카메라 또는 영상을 열 수 없습니다.\n";
		return -1;
	}

	cv::namedWindow("Frame", 1);
	cv::setMouseCallback("Frame", mouse_callback);	// Frame window에서 마우스 동작을 인식시킬 것임

#ifndef WEBCAM	// WEBCAM이 아닐 때 ( 동영상이면 첫 프레임을 받은 담에 ROI 설정할 때까지 정지. 웹캠의 경우 받아오다가 마우스 이벤트 들어오면 ROI 설정 후 동작 )
	cv::Mat img_sceen;	//
	cap.read(img_sceen);
#endif

	while (1)
	{

#ifdef WEBCAM	// WEBCAM일 때는 영상을 일단 받음
		cap.read(img_color);
#else			// WEBCAM이 아닐 때
		if (step == 4)	// step이 4면 영상을 img_color에 계속 받아옴
			cap.read(img_color);
		else            // step이 4가 아니면(ROI 설정 전) img_screen를 복사해 img_color에 저장
			img_sceen.copyTo(img_color);
#endif
		// 에러 확인
		if (img_color.empty()) {
			cerr << "빈 영상이 캡쳐되었습니다.\n";
			break;
		}

		switch (step)
		{
		case 1:// step이 1일 때 (첫 좌클릭) 원 그리기
			cv::circle(img_color, cv::Point(start_x, start_y), 10, cv::Scalar(0, 255, 0)/*초록색*/, -1);
			break;

		case 2:// step이 2일 때 (마우스 이동) 사각형 그리기
			cv::rectangle(img_color, cv::Point(start_x, start_y), cv::Point(end_x, end_y), cv::Scalar(0, 255, 0)/*초록색*/, 3);
			break;

		case 3:// step이 3일 때 (마우스 떼기)
			if (start_x > end_x) {	// 시작 버튼의 좌표가 끝날 때 좌표보다 큰 경우 바꾸기
				swap(&start_x, &end_x);
				swap(&start_y, &end_y);
			}

			roi = cv::Rect(start_x, start_y, end_x - start_x, end_y - start_y);// x, y, width, height를 가지는 사각형을 표현하는 class 선언
			cv::cvtColor(img_color, img_hsv, cv::COLOR_BGR2HSV);//HSV로 변환
			img_ROI = cv::Mat(img_hsv, roi);	// ROI가 포함된 이미지

			cv::inRange(img_ROI, cv::Scalar(0., 60., 60.), cv::Scalar(180., 255., 255.), img_mask); // inRange로 만들어진 img_mask이고 histogram을 그릴 영역임
			// 그 범위안에 들어가게되면 1로 만들어주고 나머지는 0으로 만들어 흑백사진을 만듦
			/*	src	first input array.
				lowerb	inclusive lower boundary array or a scalar.
				upperb	inclusive upper boundary array or a scalar.
				dst	output array of the same size as src and CV_8U type.
			*/
			imshow("ROI", img_ROI);	// ROI만 보기

			cv::calcHist(&img_ROI, 1, channels, img_mask, objectHistogram, 1, hsize, histRange); // ROI의 히스토그램 계산하여 objectHistogram에 저장
			cv::normalize(objectHistogram, objectHistogram, 0, 255, cv::NORM_MINMAX);

			step++;	// step 4로 이동

			break;

		case 4:
			cv::Mat bp;	// BackProject 결과를 저장할 Mat 객체 선언
			cv::cvtColor(img_color, img_hsv, cv::COLOR_BGR2HSV); // 현재 프레임에 대해 hsv로 변환
			cv::calcBackProject(&img_hsv, 1, channels, objectHistogram, bp, histRange);
			/*	const Mat* images : Source arrays
				int nimages : Number of source images
                const int* channels : The list of channels used to compute the back projection
				InputArray hist : Input histogram that can be dense or sparse
                OutputArray backProject : Destination back projection array that is a single-channel array of the same size and depth as images[0]
				const float** ranges : Array of arrays of the histogram bin boundaries in each dimension
                double scale = 1 : Optional scale factor for the output back projection
				bool uniform = true : Flag indicating whether the histogram is uniform or not (see above)
			*/

			// Tracking
			cv::meanShift(bp, roi, cv::TermCriteria(cv::TermCriteria::EPS | cv::TermCriteria::COUNT, 10, 1));
			/*
				InputArray probImage : Back projection of the object histogram
				CV_IN_OUT Rect& window : Initial search window
				TermCriteria criteria : Stop criteria for the iterative search algorithm
			*/
			cv::rectangle(img_color, roi, cv::Scalar(0, 0, 255), 2);
			break;

		}

		//if (step < 4)
		cout << step << endl;

		imshow("Frame", img_color);

		if (cv::waitKey(25) >= 0)
			break;
	}

	return 0;
}
```
