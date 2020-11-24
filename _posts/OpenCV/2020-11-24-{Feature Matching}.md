---
layout: single
title: "Feature Matching - OpenCV(C++)"
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

## D435를 이용해 Feature Matching 해보기

#### Visual Studio 2017을 사용하였습니다.

+ Realsense SDK 2.0, OpenCV 사용

Feature Matching을 구현해보았습니다. SIFT, ORB, BRISK를 사용하였습니다.

```c++
#include <opencv2/opencv.hpp>
#include <opencv2/features2d.hpp>
#include <opencv2/videoio.hpp>
#include <opencv2/imgproc.hpp>
#include <opencv2/calib3d.hpp>
#include <opencv2/highgui.hpp>      //for imshow
#include <vector>
#include <iostream>
#include <iomanip>
#include <opencv2/features2d.hpp>
// #include <opencv2/xfeatures2d.hpp>

// using namespace cv;
using namespace std;


int main()
{
	cv::Mat frame;
	cv::Mat Refer_image;
	cv::Mat Refer_gray_image;
	cv::Mat Target_image;
	cv::Mat Target_gray_image;

	vector<cv::KeyPoint> TargetKeypoints, ReferenceKeypoints;
	cv::Mat TargetDescriptor, ReferDescriptor;

	cv::Ptr<cv::Feature2D> orb = cv::ORB::create(10);
	cv::Ptr<cv::Feature2D> sift = cv::SIFT::create(500);
	cv::Ptr<cv::Feature2D> brisk = cv::BRISK::create();

	cv::Ptr<cv::DescriptorMatcher> Matcher_ORB = cv::BFMatcher::create(cv::NORM_HAMMING);		// Brute-Force matcher create method
	cv::Ptr<cv::DescriptorMatcher> Matcher_BRISK = cv::BFMatcher::create(cv::NORM_HAMMING);		// Brute-Force matcher create method
	cv::Ptr<cv::DescriptorMatcher> Matcher_SIFT = cv::BFMatcher::create(cv::NORM_L2);			// Brute-Force matcher create method

	vector<cv::DMatch> matches;	// Class for matching keypoint descriptors.

	cv::VideoCapture cap(0); // camera 연결
	if (!cap.isOpened())
	{
		cerr << "카메라 열 수 없음." << endl;
		return -1;
	}

	cout << "R 또는 r 버튼을 누르시면 References frame으로 선택됩니다. \n";
	cout << "Reference Image를 선택한 후에 Target Image를 선택하시오. (프로그램 종료 : esc key)\n\n";
	cout << "그 후 o(ORB), b(BRISK), s(SIFT)를 선택하시오.\n";

	while (true) // 종료 시까지 영상 출력
	{
		cap.read(frame); // video 캡쳐 객체 받는 것(비디오의 한 프레임씩 읽음)
		if (frame.empty())
		{
			cerr << "캡쳐 실패." << endl;
			break;
		}

		imshow("Video", frame);		// 현재 웹캠을 통해 실시간으로 받고 있는 영상을 보여주기

		int key = cv::waitKey(10);

		if (key == 27) // esc key 누르면 종료
			break;

		if ((key == 82) || (key == 114)) // 녹화 버튼(R 또는 r)을 눌렀을 때
		{
			cout << "References image으로 선택됩니다.\n\n";
			Refer_image = frame; // 눌렀을 때의 해당 프레임을 Reference frame으로 선택
			cv::cvtColor(Refer_image, Refer_gray_image, cv::COLOR_RGB2GRAY);

			imshow("Reference Image", Refer_image);			// References image 보여주기
			imshow("Reference Gray Image", Refer_gray_image);
		}

		// 이제 타겟 이미지를 정해서 Refer frame과 feature matching을 실행할 것임

		// O or o : ORB , B or b : BRISK, S or s : SIFT를 선택할 수 있도록 설정하기

		//if ((key == 84) || (key == 116)) // Target Image(T 또는 t)을 눌렀을 때
		if((key == 79) || (key == 111))	// ORB를 이용, Target image 선정
		{
			// References image에 대해 feature 뽑기
			orb->detectAndCompute(Refer_gray_image, cv::Mat(), ReferenceKeypoints, ReferDescriptor);// detects keypoints and computes the descriptors

			// Refer image에 대해 keypoint 찾은 거 보고 싶으면 주석 해제
			cv::Mat sift_refer_result;
			cv::drawKeypoints(Refer_gray_image, ReferenceKeypoints, sift_refer_result);
			imshow("Result_sift_refer_result", sift_refer_result);
			cout << "ORB를 선택하셨습니다. 해당 이미지를 Target image으로 선택합니다.\n\n";
			Target_image = frame; // 눌렀을 때의 해당 프레임을 Target image으로 선택
			cv::cvtColor(Target_image, Target_gray_image, cv::COLOR_RGB2GRAY);

			imshow("Target Image", Target_image);	// Target image 보여주기
			imshow("Target Gray Image", Target_gray_image);

			// Target image에 대해 feature 뽑기
			orb->detectAndCompute(Target_gray_image, cv::Mat(), TargetKeypoints, TargetDescriptor);// detects keypoints and computes the descriptors

			// Refer image와 Target image의 fature를 이용하여 Feature matching 실행
			Matcher_ORB->match(TargetDescriptor, ReferDescriptor, matches);	// Find the best match for each descriptor from a query set.

			sort(matches.begin(), matches.end());
			const int match_size = matches.size();
			vector<cv::DMatch> good_matches(matches.begin(), matches.begin() + (int)(match_size * 0.5f));

			cv::Mat Result;
			cv::drawMatches(Target_gray_image, TargetKeypoints, Refer_gray_image, ReferenceKeypoints, matches, Result, cv::Scalar::all(-1), cv::Scalar(-1), vector<char>(), cv::DrawMatchesFlags::NOT_DRAW_SINGLE_POINTS);
			// Draws the found matches of keypoints from two images.

			imshow("Matching Result_ORB", Result);
		}
		else if ((key == 66) || (key == 98))	// BRISK를 이용, Target image 선정
		{
			brisk->detectAndCompute(Refer_gray_image, cv::Mat(), ReferenceKeypoints, ReferDescriptor);

			cout << "BRISK를 선택하셨습니다. 해당 이미지를 Target image으로 선택합니다.\n\n";
			Target_image = frame; // 눌렀을 때의 해당 프레임을 Target image으로 선택
			cv::cvtColor(Target_image, Target_gray_image, cv::COLOR_RGB2GRAY);
			imshow("Target Image", Target_image);	// Target image 보여주기
			imshow("Target Gray Image", Target_gray_image);

			brisk->detectAndCompute(Target_gray_image, cv::Mat(), TargetKeypoints, TargetDescriptor);

			Matcher_BRISK->match(TargetDescriptor, ReferDescriptor, matches);	// Find the best match for each descriptor from a query set.
			Matcher_BRISK->match(ReferDescriptor, TargetDescriptor, matches);

			double max_dist = 0.0, min_dist = 100.0;
			for (int i = 0; i < matches.size(); i++)
			{
				double dist = matches[i].distance;
				if (dist < min_dist)
					min_dist = dist;
				if (dist > max_dist)
					max_dist = dist;
			}

			// drawing only good matches (dist less than 2*min_dist)
			vector<cv::DMatch> good_matches;

			for (int i = 0; i < matches.size(); i++)
			{
				if (matches[i].distance <= 2 * min_dist)
				{
					good_matches.push_back(matches[i]);
				}
			}

			cv::Mat Result_BRISK;

			cv::drawMatches(Target_gray_image, TargetKeypoints, Refer_gray_image, ReferenceKeypoints, good_matches, Result_BRISK, cv::Scalar::all(-1), cv::Scalar(-1), vector<char>(), cv::DrawMatchesFlags::NOT_DRAW_SINGLE_POINTS);
			// Draws the found matches of keypoints from two images.

			imshow("Matching Result_BRISK", Result_BRISK);
		}
		else if ((key == 83) || (key == 115))	// SIFT를 이용, Target image 선정
		{
			sift->detectAndCompute(Refer_gray_image, cv::Mat(), ReferenceKeypoints, ReferDescriptor);

			// Refer image에 대해 keypoint 찾은 거 보고 싶으면 주석 해제
			/*cv::Mat sift_refer_result;
			cv::drawKeypoints(Refer_gray_image, ReferenceKeypoints, sift_refer_result);
			imshow("Result_sift_)resfer_result", sift_refer_result);*/

			cout << "SIFT를 선택하셨습니다. 해당 이미지를 Target image으로 선택합니다.\n\n";
			Target_image = frame; // 눌렀을 때의 해당 프레임을 Target image으로 선택
			cv::cvtColor(Target_image, Target_gray_image, cv::COLOR_RGB2GRAY);

			imshow("Target Image", Target_image);	// Target image 보여주기
			imshow("Target Gray Image", Target_gray_image);

			//sift->detect(Target_gray_image, TargetKeypoints);
			//sift->compute(Target_gray_image, TargetKeypoints, TargetDescriptor);   // 예전 버전의 경우 이렇게 했어야 함
			sift->detectAndCompute(Target_gray_image, cv::Mat(), TargetKeypoints, TargetDescriptor);

			// Target image에 대해 keypoint 찾은 거 보고 싶으면 주석 해제
			/*cv::Mat sift_target_result;
			cv::drawKeypoints(Target_gray_image, TargetKeypoints, sift_target_result);
			imshow("Result_sift_Target_result", sift_target_result);*/

			// Refer image와 Target image의 fature를 이용하여 Feature matching 실행

			Matcher_SIFT->match(TargetDescriptor, ReferDescriptor, matches);	// Find the best match for each descriptor from a query set.
			Matcher_SIFT->match(ReferDescriptor, TargetDescriptor, matches);

			double max_dist = 0.0, min_dist = 100.0;
			for (int i = 0; i < matches.size(); i++)
			{
				double dist = matches[i].distance;
				if (dist < min_dist)
					min_dist = dist;
				if (dist > max_dist)
					max_dist = dist;
			}

			// drawing only good matches (dist less than 2*min_dist)
			vector<cv::DMatch> good_matches;

			for (int i = 0; i < matches.size(); i++)
			{
				if (matches[i].distance <= 2 * min_dist)
				{
					good_matches.push_back(matches[i]);
				}
			}

			cv::Mat Result_SIFT;

			cv::drawMatches(Target_gray_image, TargetKeypoints, Refer_gray_image, ReferenceKeypoints, good_matches, Result_SIFT, cv::Scalar::all(-1), cv::Scalar(-1), vector<char>(), cv::DrawMatchesFlags::NOT_DRAW_SINGLE_POINTS);
			// Draws the found matches of keypoints from two images.

			imshow("Matching Result_SIFT", Result_SIFT);
		}
	}
	return 0;
}
```

## 결과 이미지  

+ ORB  
![ORB](/img/ORB.jpg)  

+ BRISK  
![BRISK](/img/BRISK.jpg)  

+ SIFT  
![SIFT](/img/SIFT.jpg)  
