---
layout: single
title: "C - Window search로 Disparity Estimation 구현해보기"
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

#### 이번 post는 Disparity Estimation을 구현해보는 저장하는 문제입니다.  

#### 파일 입,출력 포스팅에 있는 주석들은 생략하였습니다.  

Disparity Estimation은 좌영상과 우영상의 disparity를 표현하는 방법입니다. 보통 스테레오 카메라로 촬영된 이미지에 대해 수행합니다.

코드에 대한 가정은 아래와 같습니다.  
- 높이는 맞춰져 있으므로 가로 방향의 Disparity를 찾는다.  
- 찾는 방식은 이전 포스트의 Motino Estimation과 같지만 가로방향 뿐이라는 점  

결과 예시 이미지는 돌려보시라고 첨부하지 않았습니다..! :-)  
block search와 window search의 차이와 block size를 어떻게 하느냐에 따라 결과가 달라지게 됩니다.  

해당 코드는 window search에 대한 코드입니다!!  

```c
#pragma warning(disable:4996)

#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <math.h>

typedef unsigned char BYTE;
#define HEI 375
#define WID_COLOR 1350
#define WID 450
#define WindowSize_length 3
#define EXPAND 550
#define EXPAND_length 50

void main(void)
{

	int i = 0, j = 0, k = 0, v = 0, o = 0, p = 0, z = 0;
	BYTE c = 0;
	int e = 0, r = 0;
	int h = 0, l = 0;
	int result = 0;
	int min = 99999;

	BYTE** ori_pic_1_color = (BYTE**)malloc(HEI * sizeof(BYTE*));      // teddy_left 복사
	for (i = 0; i < HEI; i++)
	{
		ori_pic_1_color[i] = (BYTE*)malloc(WID_COLOR * sizeof(BYTE));
	}

	BYTE** ori_pic_2_color = (BYTE**)malloc(HEI * sizeof(BYTE*));      // teddy_right 복사
	for (i = 0; i < HEI; i++)
	{
		ori_pic_2_color[i] = (BYTE*)malloc(WID_COLOR * sizeof(BYTE));
	}

	BYTE** ori_pic_1 = (BYTE**)malloc(HEI * sizeof(BYTE*));      // teddy_left RGB to Gray
	for (i = 0; i < HEI; i++)
	{
		ori_pic_1[i] = (BYTE*)malloc(WID * sizeof(BYTE));
	}

	BYTE** ori_pic_2 = (BYTE**)malloc(HEI * sizeof(BYTE*));      // teddy_right RGB to Gray
	for (i = 0; i < HEI; i++)
	{
		ori_pic_2[i] = (BYTE*)malloc(WID * sizeof(BYTE));
	}


	printf("2차원 더블 포인터 생성 완료! \n");

	FILE* fin_left = fopen("teddy_left.raw", "rb");
	FILE* fin_right = fopen("teddy_right.raw", "rb");
	FILE* fout_window = fopen("Window_result.raw", "wb");

	for (i = 0; i < HEI; i++)
	{
		fread(ori_pic_1_color[i], WID_COLOR, sizeof(BYTE), fin_left);
		fread(ori_pic_2_color[i], WID_COLOR, sizeof(BYTE), fin_right);
	}
	printf("컬러 복사 완료! \n");

	for (i = 0; i < HEI; i++)
	{
		for (j = 0; j < WID; j++)
		{
			ori_pic_1[i][j] = (ori_pic_1_color[i][3 * j] + ori_pic_1_color[i][3 * j + 1] + ori_pic_1_color[i][3 * j + 2]) / 3;
			ori_pic_2[i][j] = (ori_pic_2_color[i][3 * j] + ori_pic_2_color[i][3 * j + 1] + ori_pic_2_color[i][3 * j + 2]) / 3;
		}
	}
	printf("RGB to Gray 변환 완료 \n");

	FILE * fout_ori = fopen("ori 확인.raw", "wb");

	for (i = 0; i < HEI; i++)
	{
		fwrite(ori_pic_1[i], sizeof(BYTE), WID, fout_ori);
	}

	//int count = 0;
	//for (i = 0; i < HEI; i++)
	//{
	//	for (j = 0; j < WID; j++)
	//	{
	//		printf("%d ", ori_pic_1[i][j]);
	//		if (ori_pic_1[i][j]>=0)
	//			count++;
	//	}
	//}
	//printf("\n %d \n", count);
	/////////////////////////////////////////////////////////////////////////////////////////////////////
	BYTE** Padding_Window_left = (BYTE**)malloc((HEI + ((WindowSize_length / 2 )* 2)) * sizeof(BYTE*));
	for (i = 0; i < (HEI + (WindowSize_length / 2 * 2)); i++)
	{
		Padding_Window_left[i] = (BYTE*)malloc(EXPAND * sizeof(BYTE));
	}

	BYTE** Padding_Window_right = (BYTE**)malloc((HEI + ((WindowSize_length / 2) * 2)) * sizeof(BYTE*));

	for (i = 0; i < HEI + ((WindowSize_length / 2) * 2); i++)
	{
		Padding_Window_right[i] = (BYTE*)malloc(EXPAND * sizeof(BYTE));
	}


	printf("Padding 할당 완료! \n");
	/////////////////////////////////////////////////////////////////////////////////////////// Padding 시작

	for (i = 0; i < HEI; i++)
	{
		for (j = 0; j < WID; j++)
		{

			Padding_Window_left[(WindowSize_length / 2) + i][EXPAND_length + j] = ori_pic_1[i][j];
			Padding_Window_right[(WindowSize_length / 2) + i][EXPAND_length + j] = ori_pic_2[i][j];
		}
	}

	/////////////////////////////////////////////////////////////////////////////////////////////////////
	for (i = 0; i < (WindowSize_length / 2); i++)
	{
		for (j = 0; j < EXPAND_length; j++)
		{
			Padding_Window_left[i][j] = ori_pic_1[0][0];
			Padding_Window_right[i][j] = ori_pic_2[0][0];  //  좌측 상단

			Padding_Window_left[i][(EXPAND_length + WID) + j] = ori_pic_1[0][449];
			Padding_Window_right[i][(EXPAND_length + WID) + j] = ori_pic_2[0][449];   //  우측 상단

			Padding_Window_left[((WindowSize_length / 2) + HEI) + i][j] = ori_pic_1[374][0];
			Padding_Window_right[((WindowSize_length / 2) + HEI) + i][j] = ori_pic_2[374][0];   //  좌측 하단

			Padding_Window_left[((WindowSize_length / 2) + HEI) + i][(EXPAND_length + WID) + j] = ori_pic_1[374][449];
			Padding_Window_right[((WindowSize_length / 2) + HEI) + i][(EXPAND_length + WID) + j] = ori_pic_2[374][449];  // 우측 하단
		}
	}
	for (i = 0; i < (WindowSize_length / 2); i++)
	{
		for (j = 0; j < WID; j++)
		{
			Padding_Window_left[i][EXPAND_length + j] = ori_pic_1[0][j];
			Padding_Window_right[i][EXPAND_length + j] = ori_pic_2[0][j];   // 위쪽

			Padding_Window_left[((WindowSize_length / 2) + HEI) + i][EXPAND_length + j] = ori_pic_1[374][j];
			Padding_Window_right[((WindowSize_length / 2) + HEI) + i][EXPAND_length + j] = ori_pic_2[374][j];  // 아래쪽
		}
	}

	for (i = 0; i < HEI; i++)
	{
		for (v = 0; v < EXPAND_length; v++)
		{
			Padding_Window_left[(WindowSize_length / 2) + i][v] = ori_pic_1[i][0];
			Padding_Window_right[(WindowSize_length / 2) + i][v] = ori_pic_2[i][0];  // 좌측

			Padding_Window_left[(WindowSize_length / 2) + i][(EXPAND_length + WID) + v] = ori_pic_1[i][449];
			Padding_Window_right[(WindowSize_length / 2) + i][(EXPAND_length + WID) + v] = ori_pic_2[i][449];  // 우측
		}
	}

	printf("Window Padding 완료! \n");
	/////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

	//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////padding 끝
	FILE * Window_left = fopen("Window_left패딩.raw", "wb");
	for (i = 0; i < (HEI + ((WindowSize_length / 2) * 2)); i++)
	{
		fwrite((void*)Padding_Window_left[i], sizeof(BYTE), EXPAND, Window_left);
	}

	FILE * Window_right = fopen("Window_right패딩.raw", "wb");
	for (i = 0; i < (HEI + ((WindowSize_length / 2) * 2)); i++)
	{
		fwrite((void*)Padding_Window_right[i], sizeof(BYTE), EXPAND, Window_right);
	}

	printf("패딩 확인용 저장 완료! \n");

	char** Window_Motion_Vector = (char**)malloc(HEI * sizeof(char*));
	for (i = 0; i < HEI; i++)
	{
		Window_Motion_Vector[i] = (char*)malloc(WID * sizeof(char));
	}


	//for (i = 0; i < HEI / BlockSize_length; i++)
	//{
	//	for (j = 0; j < WID / BlockSize_length; j++)
	//	{
	//		Block_Motion_Vector[i][j] = 0;
	//	}
	//}

	//for (i = 0; i < HEI; i++)
	//{
	//	for (j = 0; j < WID; j++)
	//	{
	//		Window_Motion_Vector[i][j] = 0;
	//	}
	//}
	printf("Window Motion Vector 생성 완료! \n");

	for (i = 0, e = 0; i < HEI; i++, e++)
	{
		for (j = 0, r = 0; j < WID; j++, r++)
		{
			min = 99999;

			for (o = -EXPAND_length; o < EXPAND_length; o++)
			{
				result = 0;

				for (k = -(WindowSize_length/2); k <= (WindowSize_length/2); k++)
				{
					for (v = -(WindowSize_length / 2); v <= (WindowSize_length / 2); v++)
					{
						result += abs((int)Padding_Window_right[(WindowSize_length / 2) + i + k][(j + EXPAND_length) + v] - (int)Padding_Window_left[(WindowSize_length / 2) + i + k][(j + EXPAND_length) + o + v]);


					}
				}
				if (min > result)
				{
					min = result;
					h = abs(o);

				}

			}
			//printf("%d ", h);
			Window_Motion_Vector[e][r] = (char)(256*h/EXPAND_length);

		}
	}

	printf("Window_Motion_Vector 저장 완료! \n");


	BYTE ** modi_pic_1 = (BYTE**)malloc(HEI * sizeof(BYTE*));
	for (i = 0; i < HEI; i++)
	{
		modi_pic_1[i] = (BYTE*)malloc(WID * sizeof(BYTE));
	}

	for (i = 0, e = 0; i < HEI; i++, e++)
	{
		for (j = 0, r = 0; j < WID; j++, r++)
		{
			c = (BYTE)Window_Motion_Vector[e][r];

			modi_pic_1[i][j] = c;

		}
	}

	printf("DE 완료! \n");

	for (i = 0; i < HEI; i++)
	{
		fwrite(modi_pic_1[i], sizeof(BYTE), WID, fout_window);
	}

	printf("파일 저장 완료! \n");

	fclose(fin_left);
	fclose(fin_right);
	fclose(fout_window);

	for (i = 0; i < HEI; i++)
	{
		free(ori_pic_1[i]);
		free(ori_pic_1_color[i]);
		free(ori_pic_2[i]);
		free(ori_pic_2_color[i]);
		free(modi_pic_1[i]);
	}
	free(ori_pic_1);
	free(ori_pic_1_color);
	free(ori_pic_2);
	free(ori_pic_2_color);
	free(modi_pic_1);

}

```
