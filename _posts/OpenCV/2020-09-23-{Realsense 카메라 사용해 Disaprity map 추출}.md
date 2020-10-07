---
layout: single
title: "Realsense D435를 이용해 Disparity Map 추출하기 - OpenCV(C++)"
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

## Realsense D435를 이용해 Disparity Map 추출하기

#### Visual Studio 2017을 사용하였습니다.

+ Realsense SDK 2.0, OpenCV 사용

```c++
#include <opencv2/opencv.hpp>
#include <opencv2/opencv_modules.hpp>
#include <iostream>
#include <librealsense2/rs.hpp>

using namespace cv;
using namespace std;

int main()
{
	//Contruct a pipeline which abstracts the device
	rs2::pipeline pipe;

	//Create a configuration for configuring the pipeline with a non default profile
	rs2::config cfg;

	//Add desired streams to configuration
	cfg.enable_stream(RS2_STREAM_INFRARED, 1, 640, 480, RS2_FORMAT_Y8, 30);
	cfg.enable_stream(RS2_STREAM_INFRARED, 2, 640, 480, RS2_FORMAT_Y8, 30);
	//cfg.enable_stream(RS2_STREAM_DEPTH, 640, 480, RS2_FORMAT_Z16, 30);


	//Instruct pipeline to start streaming with the requested configuration
	pipe.start(cfg);

	// Camera warmup - dropping several first frames to let auto-exposure stabilize
	rs2::frameset frames;


	int ndisparities = 96;   /**< Range of disparity */
	int SADWindowSize = 7;
	Ptr<StereoBM> sbm = StereoBM::create(ndisparities, SADWindowSize);

	while (1)
	{
		//Wait for all configured streams to produce a frame
		frames = pipe.wait_for_frames();

		//Get each frame
		rs2::frame ir_frame1 = frames.get_infrared_frame(1);
		rs2::frame ir_frame2 = frames.get_infrared_frame(2);

		// Creating OpenCV Matrix from a color image
		Mat ir1(Size(640, 480), CV_8UC1, (void*)ir_frame1.get_data(), Mat::AUTO_STEP);
		Mat ir2(Size(640, 480), CV_8UC1, (void*)ir_frame2.get_data(), Mat::AUTO_STEP);
		Mat disp;

		sbm->compute(ir1, ir2, disp);

		imshow("Display Image1", ir1);
		imshow("Display Image2", ir2);
		disp.convertTo(disp, CV_8U);
		imshow("Disparity Map", disp);

		int key = waitKey(1);

		if (key == 27)
			break;
	}
	return 0;
}
```
