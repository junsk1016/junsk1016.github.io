---
layout: single
title: "Optical Flow - OpenCV(C++)"
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

## Optical FLow 해보기  

#### Visual Studio 2017을 사용하였습니다.  

+ goodFeaturesToTrack, calcOpticalFlowPyrLK, calcOpticalFlowFarneback


Optical Flow 이론에 대한 내용보다는 코딩 관련에 집중하여 포스팅하겠습니다.

## 1. Optical Flow?  

#### 정의
+ 물체나 카메라의 움직임에 의해 물체가 움직일 때, 2개의 프레임 사이에서 객체의 외관상 움직임의 패턴  
+ Vector로 표현(2D), 첫 frame에서 두 번째 frame으로 이동 
+ 적용  
  - 움직임을 통한 구조 분석  
  - 비디오 압축  
  - Video stabilization (영상이 흔들렸거나 blur일 때, 깨끗한 영상으로 처리하는 기술)  

#### PyrLK(Pyramid Lucas Kanade) Optical Flow 방식  
+ 비디오 이미지에서 추적할 포인트 결정 ( cv::goodFeaturesToTrack )  
+ 비디오 이미지에서 첫  frame을 추출하고 shi-Tomasi 코너 검출 수행  
+ 이들 점에 대해 Lucas-Kanade 실행 및 반복  

#### Dense Optical Flow 방식  
+ 이전 프레임과 현재 프레임에 대해 Dense Optical Flow 수행 (cv::calcOpticalFlowFarneback)  
+ 결과에 대해 magnitude와 angle을 구하고 HSV 이미지 생성 후 BGR 이미지로 변환  


---

## 2. goodFeaturesToTrack(코너 검출하기)

#### 함수 원형  
goodFeaturesToTrack( InputArray image, OutputArray corners, int maxCorners, double qualityLevel, double minDistance, InputArray mask = noArray(), int blockSize = 3, bool useHarrisDetector = false, double k = 0.04 )  
+ InputArray image : Input 8-bit or floating-point 32-bit, single-channel image  
+ OutputArray corners : Output vector of detected corners  
+ int maxCorners : Maximum number of corners to return  
+ double qualityLevel : Parameter characterizing the minimal accepted quality of image corners  
+ double minDistance : Minimum possible Euclidean distance between the returned corners  
+ InputArray mask : Optional region of interest  
+ int blockSize :  Size of an average block for computing a derivative covariation matrix over each pixel neighborhood  
+ bool useHarrisDetector : Parameter indicating whether to use a Harris detector (see #cornerHarris) or #cornerMinEigenVal  
+ double k : Free parameter of the Harris detector  

---

## 3. calcOpticalFlowPyrLK(Lucas-Kanade Optical Flow)

#### 함수 원형  
calcOpticalFlowPyrLK( InputArray prevImg, InputArray nextImg, InputArray prevPts, InputOutputArray nextPts, OutputArray status, OutputArray err, Size winSize = Size(21,21), int maxLevel = 3, TermCriteria criteria = TermCriteria(TermCriteria::COUNT+TermCriteria::EPS, 30, 0.01), int flags = 0, double minEigThreshold = 1e-4 )  
+ InputArray prevImg : first 8-bit input image or pyramid constructed by buildOpticalFlowPyramid  
+ InputArray nextImg : second input image or pyramid of the same size and the same type as prevImg  
+ InputArray prevPts : vector of 2D points for which the flow needs to be found; point coordinates must be single-precision floating-point numbers   
+ InputOutputArray nextPts : output vector of 2D points (with single-precision floating-point coordinates) containing the calculated new positions of input features in the second image  
+ OutputArray status : output status vector (of unsigned chars)  
+ OutputArray err : output vector of errors  
+ Size winSize : size of the search window at each pyramid level  
+ int maxLevel : 0-based maximal pyramid level number  

---

## 4. calcOpticalFlowFarneback(Dense Optical Flow)

#### 함수 원형  
calcOpticalFlowFarneback( InputArray prev, InputArray next, InputOutputArray flow, double pyr_scale, int levels, int winsize, int iterations, int poly_n, double poly_sigma, int flags)  
+ InputArray prev : first 8-bit single-channel input image  
+ InputArray next : second input image of the same size and the same type as prev  
+ InputOutputArray flow : computed flow image that has the same size as prev and type CV_32FC2  
+ double pyr_scale : pyr_scale parameter, specifying the image scale (\<1) to build pyramids for each image; pyr_scale=0.5 means a classical pyramid, where each next layer is twice smaller than the previous one.  
+ int levels : number of pyramid layers including the initial image  
+ int winsize : averaging window size  
+ int iterations : number of iterations the algorithm does at each pyramid level  
+ int poly_n : size of the pixel neighborhood used to find polynomial expansion in each pixel  
+ double poly_sigma : standard deviation of the Gaussian that is used to smooth derivatives used as a basis for the polynomial expansion  

---

## 5. 코드  

```c++
#include <opencv2/opencv.hpp>
#include <iostream>
#include <vector>

// using namespace cv;
using namespace std;

int main()
{
	cout << "\n\n프로그램 종료는 esc key를 누르세요.\n";
	cout << "\n ===================================================================== \n";
	cout << "웹캠 사용은 1, 동영상 재생은 2를 누르세요.\n\n\n";
	int num;
	cin >> num;

	if (num == 1)	// 웹캠 선택
	{
		cv::VideoCapture cap(0); // camera 연결
		if (!cap.isOpened())
		{
			cerr << "\n카메라 열 수 없음.\n" << endl;
			return -1;
		}

		cv::Mat old_frame, old_gray;
		cap.read(old_frame);	// 초기 frame 설정

		cv::cvtColor(old_frame, old_gray, cv::COLOR_BGR2GRAY);

		vector<cv::Point2f> p0, p1;
		cv::goodFeaturesToTrack(old_gray, p0, 100, 0.3, 7, cv::Mat(), 7, false, 0.04);	// 이미지에서 코너 추출하기
		/*goodFeaturesToTrack( InputArray image, OutputArray corners,
                                     int maxCorners, double qualityLevel, double minDistance,
                                     InputArray mask = noArray(), int blockSize = 3,
                                     bool useHarrisDetector = false, double k = 0.04 );*/

		// Create a mask image for drawing purposes
		cv::Mat mask = cv::Mat::zeros(old_frame.size(), old_frame.type());

		cv::Mat D_frame, D_prvs;
		cap.read(D_frame);
		cv::cvtColor(D_frame, D_prvs, cv::COLOR_BGR2GRAY);

		while (true) // 종료 시까지 영상 출력
		{
			cv::Mat frame;

			cap.read(frame);
			if (frame.empty())
			{
				cerr << "캡쳐 실패." << endl;
				break;
			}
			imshow("Live", frame);

			/*==============================Lucas - Kanade Optical Flow 해보기===========================================*/
			cv::Mat LK_frame, LK_frame_gray;
			cv::copyTo(frame, LK_frame, cv::Mat());
			cv::cvtColor(LK_frame, LK_frame_gray, cv::COLOR_BGR2GRAY);

			vector<uchar> status;
			vector<float> err;
			cv::TermCriteria criteria = cv::TermCriteria((cv::TermCriteria::COUNT) + (cv::TermCriteria::EPS), 10, 0303);
											//TermCriteria(int _type, int _maxCount, double _epsilon);
			cv::calcOpticalFlowPyrLK(old_gray, LK_frame_gray, p0, p1, status, err, cv::Size(21, 21), 3, criteria);
			/*calcOpticalFlowPyrLK( InputArray prevImg, InputArray nextImg,
                                        InputArray prevPts, InputOutputArray nextPts,
                                        OutputArray status, OutputArray err,
                                        Size winSize = Size(21,21), int maxLevel = 3,
                                        TermCriteria criteria = TermCriteria(TermCriteria::COUNT+TermCriteria::EPS, 30, 0.01),
                                        int flags = 0, double minEigThreshold = 1e-4 );*/
			vector<cv::Point2f> good_new;
			for (uint i = 0; i < p0.size(); i++)
			{
				// Select good points
				if (status[i] == 1)
				{
					good_new.push_back(p1[i]);		// 다음 p0로 사용하기 위해 좋은거만 선택
					// draw the tracks
					line(mask, p1[i], p0[i], (0, 0, 255), 3);
					circle(LK_frame, p1[i], 5, (0, 255, 255), 2);
				}
			}

			cv::Mat LK_result_image;
			cv::add(LK_frame, mask, LK_result_image);

			imshow("Lucas-Kanade Result", LK_result_image);

			// Now update the previous frame and previous points
			old_gray = LK_frame_gray.clone();
			//p0 = good_new;
			p0 = p1;
			/*=================================Dense Optical Flow 해보기=============================================*/
			cv::Mat D_frame2, D_next;
			cv::copyTo(frame, D_frame2, cv::Mat());
			cv::cvtColor(D_frame2, D_next, cv::COLOR_BGR2GRAY);

			cv::Mat flow(D_prvs.size(), CV_32FC2);
			cv::calcOpticalFlowFarneback(D_prvs, D_next, flow, 0.5, 3, 15, 3, 5, 1.2, 0);
			/*calcOpticalFlowFarneback( InputArray prev, InputArray next, InputOutputArray flow,
                                            double pyr_scale, int levels, int winsize,
                                            int iterations, int poly_n, double poly_sigma,
                                            int flags );*/


			//visualization
			cv::Mat flow_parts[2];
			cv::split(flow, flow_parts);
			cv::Mat magnitude, angle, magn_norm;
			cv::cartToPolar(flow_parts[0], flow_parts[1], magnitude, angle, true);
			/*cartToPolar(InputArray x, InputArray y,
                              OutputArray magnitude, OutputArray angle,
                              bool angleInDegrees = false);*/
			cv::normalize(magnitude, magn_norm, 0.0f, 10.f, cv::NORM_MINMAX);
			angle *= ((1.f / 360.f) * (180.f / 255.f));

			//build hsv image
			cv::Mat _hsv[3], hsv, hsv8, D_result_image;			// HSV는 색상, 채도, 명도로 image 표현
			_hsv[0] = angle;
			_hsv[1] = cv::Mat::ones(angle.size(), CV_32F);
			_hsv[2] = magn_norm;
			merge(_hsv, 3, hsv);	// 3채널을 1채널로 합침
			hsv.convertTo(hsv8, CV_8U, 255.0);	// DataType 변경
			cvtColor(hsv8, D_result_image, cv::COLOR_HSV2BGR);
			imshow("Dense Result", D_result_image);

			D_prvs = D_next;

			int key = cv::waitKey(10);
			if (key == 27) // esc key 누르면 종료
				break;
		}
	}

	else if (num == 2)		// 동영상 출력
	{
		cv::VideoCapture cap("BlackBOX.mp4");
		if (!cap.isOpened())
		{
			cerr << "파일을 열 수 없습니다." << endl;
			return -1;
		}

		cv::Mat old_frame, old_gray;
		cap.read(old_frame);	// 초기 frame 설정

		cv::cvtColor(old_frame, old_gray, cv::COLOR_BGR2GRAY);

		vector<cv::Point2f> p0, p1;
		cv::goodFeaturesToTrack(old_gray, p0, 100, 0.3, 7, cv::Mat(), 7, false, 0.04);	// 이미지에서 코너 추출하기
		/*goodFeaturesToTrack( InputArray image, OutputArray corners,
									 int maxCorners, double qualityLevel, double minDistance,
									 InputArray mask = noArray(), int blockSize = 3,
									 bool useHarrisDetector = false, double k = 0.04 );*/

									 // Create a mask image for drawing purposes
		cv::Mat mask = cv::Mat::zeros(old_frame.size(), old_frame.type());


		cv::Mat D_frame, D_prvs;
		cap.read(D_frame);
		cv::cvtColor(D_frame, D_prvs, cv::COLOR_BGR2GRAY);

		while (true) // 종료 시까지 영상 출력
		{
			cv::Mat frame;

			cap.read(frame);
			if (frame.empty())
			{
				cerr << "캡쳐 실패." << endl;
				break;
			}
			imshow("Live", frame);

			/*==============================Lucas - Kanade Optical Flow 해보기===========================================*/
			cv::Mat LK_frame, LK_frame_gray;
			cv::copyTo(frame, LK_frame, cv::Mat());
			cv::cvtColor(LK_frame, LK_frame_gray, cv::COLOR_BGR2GRAY);

			vector<uchar> status;
			vector<float> err;
			cv::TermCriteria criteria = cv::TermCriteria((cv::TermCriteria::COUNT) + (cv::TermCriteria::EPS), 10, 0303);
			//TermCriteria(int _type, int _maxCount, double _epsilon);
			cv::calcOpticalFlowPyrLK(old_gray, LK_frame_gray, p0, p1, status, err, cv::Size(21, 21), 2, criteria);
			/*calcOpticalFlowPyrLK( InputArray prevImg, InputArray nextImg,
										InputArray prevPts, InputOutputArray nextPts,
										OutputArray status, OutputArray err,
										Size winSize = Size(21,21), int maxLevel = 3,
										TermCriteria criteria = TermCriteria(TermCriteria::COUNT+TermCriteria::EPS, 30, 0.01),
										int flags = 0, double minEigThreshold = 1e-4 );*/
			vector<cv::Point2f> good_new;
			for (uint i = 0; i < p0.size(); i++)
			{
				// Select good points
				if (status[i] == 1)
				{
					good_new.push_back(p1[i]);		// 다음 p0로 사용하기 위해 좋은거만 선택
					// draw the tracks
					line(mask, p1[i], p0[i], (0, 0, 255), 3);
					circle(LK_frame, p1[i], 5, (0, 255, 255), 2);
				}
			}

			cv::Mat LK_result_image;
			cv::add(LK_frame, mask, LK_result_image);
			// imshow("mask", mask);
			imshow("Lucas-Kanade Result", LK_result_image);

			// Now update the previous frame and previous points
			old_gray = LK_frame_gray.clone();
			p0 = good_new;

			/*=================================Dense Optical Flow 해보기=============================================*/
			cv::Mat D_frame2, D_next;
			cv::copyTo(frame, D_frame2, cv::Mat());
			cv::cvtColor(D_frame2, D_next, cv::COLOR_BGR2GRAY);

			cv::Mat flow(D_prvs.size(), CV_32FC2);
			cv::calcOpticalFlowFarneback(D_prvs, D_next, flow, 0.5, 3, 15, 3, 5, 1.2, 0);
			/*calcOpticalFlowFarneback( InputArray prev, InputArray next, InputOutputArray flow,
											double pyr_scale, int levels, int winsize,
											int iterations, int poly_n, double poly_sigma,
											int flags );*/


											//visualization
			cv::Mat flow_parts[2];
			cv::split(flow, flow_parts);
			cv::Mat magnitude, angle, magn_norm;
			cv::cartToPolar(flow_parts[0], flow_parts[1], magnitude, angle, true);
			/*cartToPolar(InputArray x, InputArray y,
							  OutputArray magnitude, OutputArray angle,
							  bool angleInDegrees = false);*/
			cv::normalize(magnitude, magn_norm, 0.0f, 10.f, cv::NORM_MINMAX);
			angle *= ((1.f / 360.f) * (180.f / 255.f));

			//build hsv image
			cv::Mat _hsv[3], hsv, hsv8, D_result_image;			// HSV는 색상, 채도, 명도로 image 표현
			_hsv[0] = angle;
			_hsv[1] = cv::Mat::ones(angle.size(), CV_32F);
			_hsv[2] = magn_norm;
			merge(_hsv, 3, hsv);	// 3채널을 1채널로 합침
			hsv.convertTo(hsv8, CV_8U, 255.0);	// DataType 변경
			cvtColor(hsv8, D_result_image, cv::COLOR_HSV2BGR);
			imshow("Dense Result", D_result_image);

			D_prvs = D_next;

			int key = cv::waitKey(10);
			if (key == 27) // esc key 누르면 종료
				break;
		}
	}
	return 0;
}
```
