---
layout: single
title: "Circle Detection & Line Detection - OpenCV(C++)"
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

## Hough 변환을 이용한 Circle & Line Detection을 해보기  

#### Visual Studio 2017을 사용하였습니다.  

+ HoughLinesP, line, HoughCircles, circle, threshold  

Hough 변환 이론에 대한 내용보다는 코딩 관련에 집중하여 포스팅하겠습니다.  

1. HoughLinesP(확률을 적용한 Hough line detection )  

### 함수 원형  
HoughLinesP(src, dst, rho, theta, threshold, min_line_length, max_line_gap)  
+ src : 입력할 이미지 변수, Edge detect 된 이미지를 입력해야 함  
+ dst : Hough 변환 line detection 정보를 저장할 Array 
+ rho : 계산할 픽셀(매개 변수)의 해상도  
+ theta : 계산할 각도(라디안, 매개변수)의 해상도, 선 회전 각도  
  - 모든 방향에서 직선을 검출하려면 PI/180 을 사용하면 된다.  
+ threshold : 변환된 그래프에서 라인을 검출하기 위한 최소 교차 수  
+ min_line_length : 검출할 직선의 최소 길이, 단위는 픽셀  
+ max_line_gap : 검출할 선위의 점들 사이의 최대 거리, 점 사이의 거리가 이 값보다 크면 다른 선으로 간주  

---

2. HoughCircles(Hough Circle detection)

### 함수 원형  
HoughCircles( src, dst, method, dp, min_dist, parameter1, parameter2, min_Radius, max_Radius)  
+ Src : 입력할 Grayscale 이미지  
+ Dst : 검출 정보를 저장할 Array, 원의 중심 정보를 저장  
+ method : 원을 검출하는 방법, HOUGH_GRADIENT  
+ dp : 이미지 해상도(1 : 원본 해상도, 2 : 절반 해상도)  
+ min_dist : 검출할 원의 최소 거리  
+ parameter1 : Canny edge detection 에서의 높은 threshold 값  
+ parameter2 : 원 검출을 위한 정보  
+ Min_Radius : 검출될 원의 최소 반지름(모든 원 검출하려면 0)  
+ Max_Radius : 검출될 원의 최대 반지름(모든 원 검출하려면 0, 원의 중심만 반환하려면 음수 값)  

---

3. line(선 그리기)

### 함수 원형  
void line(Mat& img, Point pt1, Point pt2, const Scalar& color, int thickness = 1, int lineType = 8, int shift = 0)  
+ Mat& img : 그림 대상 행렬  
+ Point pt1, pt2 : 시작 좌표와 종료 좌표  
+ Rect rect : 그릴 영역을 나타내는 사각형 자료형  
+ Scalar color : 선의 색상  
+ int thickness : 선의 두께, FILLED(-1)일 경우 지정된 색으로 내부를 채움  
+ int lineType :
  - LINE_4 : 값 → 4 : 4-방향 연결선(4-connected line)
  - LINE_8 : 값 → 8 : 8-방향 연결선(8-connected line)
  - LINE_AA : 값→16 : 계단현상 감소(Anti-aliasing)
+ int shift : 입력 좌표(pt1, pt2)에 대해서 오른쪽 비트 시프트(>>) 연산한 결과를 좌표로 지정해서 직선을 그림

---

4. circle(원 그리기)

### 함수 원형  
void circle(Mat& img, Point center, int radius, const Scalar& color, int thickness = 1, int lineType = 8, int shift = 0)  
+ Mat& img : 원을 그릴 대상 행렬  
+ Point center : 원의 중심 좌표  
+ int radius : 원의 반지름  
+ Scalar& color : 선의 색상  
+ int thickness : 선의 두께  
+ int lineType : 선의 형태, cv::line() 함수의 인자와 동일  
+ int shift : 좌표에 대한 비트 시프트 연산

---

5. Threshold(Threshold 처리하기)  

### 함수 원형  
cv::threshold( src, dst, threshold_value, Max_value, threshold_type)  
+ src : 입력할 이미지 변수 (grayscale 이미지)  
+ dst : 필터가 적용되어 저장될 이미지 변수  
+ threshold_value : 임계 값 (0~255), 이진화 시킬 기준 값을 입력  
+ Max_value :  임계 값 이상의 픽셀들에  적용할 값  
+ threshold_type : 이진화 하는 방법  
|**Threshold_type**|**Threshold 값 이상**|**Threshold 값 이하**|
|:---:|:---:|:---:|
|THRESH_BINARY|Max_value|0|
|THRESH_BINARY_INV|0|Max_value|
|THRESH_MASK|흑색 이미지로 변환||
|THRESH_OTSU|Otsu 알고리즘 사용||
|THRESH_TOZERO|원본 값|0|
|THRESH_TOZERO_INV|0|원본 값|
|THRESH_TRIANGLE|Triangle 알고리즘 사용||
|THRESH_TRUNC|Threshold 값|원본 값|  

6. 코드  

```c++
#include <opencv2/opencv.hpp>
#include <iostream>

// using namespace cv;
using namespace std;

int main()
{
	cv::Mat frame;

	cv::VideoCapture cap(0); // camera 연결
	if (!cap.isOpened())
	{
		cerr << "카메라 열 수 없음." << endl;
		return -1;
	}

	cout << "\n\n프로그램 종료는 esc key를 누르세요.\n";
	cout << "\n ===================================================================== \n";
	cout << "웹캠 사용은 1, 동영상 재생은 2를 누르세요.\n\n\n";
	int num;
	cin >> num;

	if (num == 1)	// 웹캠 선택
	{
		while (true) // 종료 시까지 영상 출력
		{
			cap.read(frame);
			if (frame.empty())
			{
				cerr << "캡쳐 실패." << endl;
				break;
			}

			imshow("Live", frame);

			cv::Mat gray_image;
			cv::cvtColor(frame, gray_image, cv::COLOR_BGR2GRAY); // HoughCircles 함수는 grayscale 필요

			cv::Mat result_frame;				// 결과를 그릴 frame
			frame.copyTo(result_frame);

			/*==============================HoughCircle 해보기===========================================*/

			vector<cv::Vec3f> circles;
			cv::HoughCircles(gray_image, circles, cv::HOUGH_GRADIENT, 1, 100, 100, 35, 10, 150);
			// HoughCircles(입력, 결과출력, 검출 방법, 이미지 해상도, 검출할 원 최소거리, threshold, 원 검출을 위한 정보, 최소 반지름, 최대 반지름)

			for (size_t i = 0; i < circles.size(); i++)  // 원 그리기
			{
				cv::Vec3i c = circles[i];
				cv::Point center(c[0], c[1]); // 원 중심의 x, y 좌표
				int radius = c[2];            // 원 반지름

				cv::circle(result_frame, center, radius, cv::Scalar(0, 0, 255), 2);
			}

			/*=================================HoughLinesP 해보기=============================================*/

			cv::Mat frame_canny;
			cv::Canny(line_result, frame_canny, 150, 250);   // HoughLinesP 함수는 Edge detection을 한 결과를 입력으로 받음

			vector<cv::Vec4i> linesP;
			cv::HoughLinesP(frame_canny, linesP, 1, (CV_PI / 180), 100, 50, 10);
			cv::Mat frame_lane;    // 마스크에 그려볼 용도
			cv::threshold(frame_canny, frame_lane, 150, 255, cv::THRESH_MASK); // 흑백으로 바꾸기

			for (size_t i = 0; i < linesP.size(); i++)   // 선 그리기
			{
				cv::Vec4i l = linesP[i];
				cv::line(result_frame, cv::Point(l[0], l[1]), cv::Point(l[2], l[3]), cv::Scalar(0, 255, 0), 2, 8);
				cv::line(frame_lane, cv::Point(l[0], l[1]), cv::Point(l[2], l[3]), cv::Scalar::all(255), 1, 8);
			}

			imshow("Result", result_frame);
			imshow("Result_lane", frame_lane);

			int key = cv::waitKey(10);
			if (key == 27) // esc key 누르면 종료
				break;
		}
	}

	else if (num == 2)		// 동영상 출력
	{
		cv::Mat frame;

		cv::VideoCapture cap("BlackBOX.mp4");
		if (!cap.isOpened())
		{
			cerr << "파일을 열 수 없습니다." << endl;
			return -1;
		}
		while (cap.isOpened())
		{
			cap.read(frame);
			if (frame.empty())
			{
				cerr << "\n\n파일 읽기 실패 or 동영상 재생 완료" << endl;
				return -1;
			}

			imshow("BLACK", frame);

			cv::Mat gray_image;
			cv::cvtColor(frame, gray_image, cv::COLOR_BGR2GRAY);

			cv::Mat result_frame;				// 결과를 그릴 frame
			frame.copyTo(circle_result);

			/*==============================HoughCircle 해보기===========================================*/
			vector<cv::Vec3f> circles;
			cv::HoughCircles(gray_image, circles, cv::HOUGH_GRADIENT, 1, 100, 100, 35, 10, 150);
			// HoughCircles(입력, 결과출력, 검출 방법, 이미지 해상도, 검출할 원 최소거리, threshold, 원 검출을 위한 정보, 최소 반지름, 최대 반지름)
			for (size_t i = 0; i < circles.size(); i++)
			{
				cv::Vec3i c = circles[i];
				cv::Point center(c[0], c[1]);
				int radius = c[2];

				cv::circle(result_frame, center, radius, cv::Scalar(0, 0, 255), 2);	// 원 테두리 그리기
			}

			/*===========================HoughLinesP 해보기============================================*/
			cv::Mat frame_canny;
			cv::Canny(line_result, frame_canny, 150, 250);

			vector<cv::Vec4i> linesP;
			cv::HoughLinesP(frame_canny, linesP, 1, (CV_PI / 180), 100, 50, 10);

			cv::Mat frame_lane;
			cv::threshold(frame_canny, frame_lane, 150, 255, cv::THRESH_MASK);

			for (size_t i = 0; i < linesP.size(); i++)
			{
				cv::Vec4i l = linesP[i];
				cv::line(result_frame, cv::Point(l[0], l[1]), cv::Point(l[2], l[3]), cv::Scalar(0, 255, 0), 2, 8);
				cv::line(frame_lane, cv::Point(l[0], l[1]), cv::Point(l[2], l[3]), cv::Scalar::all(255), 1, 8);
			}
			imshow("Result_Line", result_frame);
			imshow("Result_lane", frame_lane);

			int key = cv::waitKey(10);
			if (key == 27)
				break;
		}
	}
	return 0;
}
```
