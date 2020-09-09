---
layout: single
title: "Harr Cascade 구현 - OpenCV(C++)"
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

## OpenCV를 이용해 Harr Cascade 구현하기

#### Visual Studio 2017을 사용하였습니다.

불러온 파일으로는 정면 얼굴을 받아 사용하였습니다.

key 함수 : CascadeClassifier, detectMultiScale

```c++
#include <opencv2/objdetect.hpp>
#include <opencv2/videoio.hpp>
#include <opencv2/highgui.hpp>
#include <opencv2/imgproc.hpp>
#include <iostream>
#include <stdio.h>

using namespace std;
using namespace cv;

int main()
{
	// 파일 불러오기
	CascadeClassifier face_cascade;
	face_cascade.load("C:\\opencv\\sources\\data\\haarcascades\\haarcascade_frontalface_alt.xml");

	// WebCAM 객체
	VideoCapture cap(0);
	Mat frame;
	// Haar Cascade 객체
	vector<Rect> faces; // 검출된 객체를 포함한 직사각형의 벡터
	Mat frame_gray;

	// 동영상 설정
	double fps = 30.0;

	int width = cap.get(CAP_PROP_FRAME_WIDTH); // 웹캠의 width 받기
	int height = cap.get(CAP_PROP_FRAME_HEIGHT); // 웹캠의 height 받기
	int fourcc = VideoWriter::fourcc('X', 'V', 'I', 'D'); // 동영상 사용시 저장할 코덱을 지정
	VideoWriter outputVideo; // Videowirter  객체 생성
	outputVideo.open("output.avi", fourcc, fps, Size(width, height), true); // 파일 설정

	// WebCAM 설정
	cap.set(CAP_PROP_FRAME_WIDTH, width);
	cap.set(CAP_PROP_FRAME_HEIGHT, height);

	if (!cap.isOpened())
	{
		printf("CAM이 열리지 않음\n");
		return -1;
	}

	while (true)
	{
		cap.read(frame);

		if (frame.empty())
		{
			printf("frame empty\n");
			break;
		}

		cvtColor(frame, frame_gray, COLOR_BGR2GRAY);
		equalizeHist(frame_gray, frame_gray);

		face_cascade.detectMultiScale(frame_gray, faces, 1.1, 2, 0 | CASCADE_SCALE_IMAGE, Size(50, 50));
		// public void detectMultiScale(Mat image, MatOfRect objects, double scaleFactor, int minNeighbors, int flags, Size minSize)
		// image : 카메라에서 가져온 프레임을 가공하여 검출하고자 하는 대상이 있는 확인하기 위한 원본 이미지
		// objects : 검출된 객체를 포함한 직사각형의 벡터
		// scaleFactor : 이미지 피라미드에서 사용되는 scale 계수 값(여러 scale로 만들어서 찾음)
		// minNeighbors : 이미지 피라미드에서 슬라이딩 윈도우가 대상을 검출한 횟수가 지정한 값 이상이면 유효
		// flags :  구 버전 OpenCV에서 필요
		// minSize : 검출하려는 이미지의 최소 사이즈로 해당 값보다 작으면 무시

		for (size_t i = 0; i < faces.size(); i++)
		{
			Point center(faces[i].x + faces[i].width / 2, faces[i].y + faces[i].height / 2);
			ellipse(frame, center, Size(faces[i].width / 2, faces[i].height / 2),
				0, 0, 360, Scalar(0, 128, 128), 4, 8, 0);
		}

	/*	cv.ellipse(img, center, axes, angle, startAngle, endAngle, color, thickness, lineType, shift)
			Img : 타원이 그려질 이미지
			Center : 중심 좌표(x, y)
			Axes : 메인 축 방향의 반지름
			Angle : 회전각
			startAngle : 호의 시작 각도
			endAngle : 호의 끝 각도
			Color : 타원의 색(B, G, R)
			Thickness : 선 굵기(디폴트 값 1), -1이면 내부 채워짐*/

		imshow("Result", frame);
		outputVideo.write(frame);
		int key = waitKey(1);
		if (key == 27)
			break;
	}
	return 0;
}
```
