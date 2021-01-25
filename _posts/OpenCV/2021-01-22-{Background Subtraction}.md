---
layout: single
title: "Background Subtraction - OpenCV(C++)"
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

## Background Subtraction 해보기  

#### Visual Studio 2017을 사용하였습니다.  

Background Subtractor 이론에 대한 내용보다는 코딩 관련에 집중하여 포스팅하겠습니다.

## 1. Background Subtractor?  

#### 정의
+ Static cameras 사용에서 foreground mask를 generate할 때 사용하는 기술 
+ Foreground mask  
  - 장면에서 움직이는 물체에 속하는 픽셀을 포함하는 2진 이미지  
  - Current frame과 background model 사이의 차를 구하여   

#### Step  
+ Background Initialization    
+ Background Update  

---

## 코드  

```c++
#include <iostream>
#include <opencv2/imgcodecs.hpp>
#include <opencv2/imgproc.hpp>
#include <opencv2/videoio.hpp>
#include <opencv2/highgui.hpp>
#include <opencv2/video.hpp>

using namespace std;
const char* params
// { 인수 이름 | default value | 설명 }, default value는 인수를 입력하지 않았을 때 인수값 됨.
= "{ help h         |           | Print usage }"
"{ input          | vtest.avi | Path to a video or a sequence of image }"
"{ algo           | MOG2      | Background subtraction method (KNN, MOG2) }";
int main(int argc, char* argv[])
{
	cv::CommandLineParser parser(argc, argv, params);
	parser.about("This program shows how to use background subtraction methods provided by "
		" OpenCV. You can process both videos and images.\n");	// help 메시지를 출력할 때 보이는 초반부의 설명 메시지를 설정할 수 있음
	if (parser.has("help"))
	{// has는 help인수가 입력되었는지 알기 위해 사용하는 함수. has("help") or has("h") or has("?")의 반환값이 true인 것을 확인하면 됨
		//print help information
		parser.printMessage();	// 각 인수에 대한 설명을 자동으로 생성하여 출력하는 method. 각 인수별로 지정한 설명들과 default value가 설명에 포함됨
	}

	//create Background Subtractor objects
	cv::Ptr<cv::BackgroundSubtractor> pBackSub;
	if (parser.get<cv::String>("algo") == "MOG2")// 입력된 인수 중에서 Type값을 추출할 때 사용됨.
		// Path to a video or a sequence of image가 먼저 실행되고 없으면 vtest.avi 실행
		pBackSub = cv::createBackgroundSubtractorMOG2();// Creates MOG2 Background Subtractor.
	else
		pBackSub = cv::createBackgroundSubtractorKNN();	// Creates KNN Background Subtractor.

	//cv::VideoCapture capture(0);
	cv::VideoCapture capture("REC3.mp4");
	if (!capture.isOpened()) {
		//error in opening the video input
		cerr << "Unable to open: " << parser.get<cv::String>("input") << endl;	// 입력된 인수 중에서 Type값을 추출할 때 사용됨.
		// Path to a video or a sequence of image가 먼저 실행되고 없으면 vtest.avi 실행
		return 0;
	}
	cv::Mat frame, fgMask, result;	// 현재, MASK, MASK_COLOR
	cv::Mat fgMask_M;
	cv::Mat frame1[3];

	while (true) {
		capture.read(frame);
		if (frame.empty())
			break;
		//update the background model
		pBackSub->apply(frame, fgMask);// apply : Computes a foreground mask.

    // morphology
		cv::Mat mask = cv::getStructuringElement(cv::MORPH_RECT, cv::Size(3, 3), cv::Point(1, 1));
		cv::morphologyEx(fgMask, fgMask, cv::MORPH_OPEN, mask);
		threshold(fgMask, fgMask_M, 0, 1, cv::THRESH_BINARY);	// 0보다 크면 1로 변경

		split(frame, frame1);	// 각 채널로 split
		for (int i = 0; i < 3; i++)
			cv::multiply(frame1[i], fgMask_M, frame1[i]); // 각 채널에 마스크를 씌움
		merge(frame1, 3, result);	// 3 채널 하나로 merge

		//show the current frame and the fg masks
		imshow("Frame", frame);
		imshow("FG Mask", fgMask);
		imshow("Result", result);

		//get the input from the keyboard
		int key = cv::waitKey(10);
		if (key == 27)
			break;
	}
	return 0;
}
```
