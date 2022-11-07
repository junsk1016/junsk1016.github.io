---
layout: single
title: "C - Block search로 Disparity Estimation 구현해보기"
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

해당 코드는 block search에 대한 코드입니다!!  

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
#define BlockSize_length 3
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
	FILE* fout_block = fopen("Block_result.raw", "wb");

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


	BYTE** Padding_Block_left = (BYTE**)malloc(HEI * sizeof(BYTE*));      
	for (i = 0; i < HEI; i++)
	{
		Padding_Block_left[i] = (BYTE*)malloc(EXPAND * sizeof(BYTE));
	}

	BYTE** Padding_Block_right = (BYTE**)malloc(HEI * sizeof(BYTE*));      
	for (i = 0; i < HEI; i++)
	{
		Padding_Block_right[i] = (BYTE*)malloc(EXPAND * sizeof(BYTE));
	}



	printf("Padding 2차원 배열 생성 및 0 초기화 완료! \n");
	/////////////////////////////////////////////////////////////////////////////////////////// Padding 시작
	for (i = 0; i < HEI; i++)
	{
		for (j = 0; j < WID; j++)
		{
			Padding_Block_left[i][j + ((EXPAND - WID) / 2)] = ori_pic_1[i][j];
			Padding_Block_right[i][j + ((EXPAND - WID) / 2)] = ori_pic_2[i][j];
		}
	}

	for (i = 0; i < HEI; i++)
	{

		for (j = 0; j < (EXPAND - WID) / 2; j++)
		{
			Padding_Block_left[i][j] = ori_pic_1[i][0];
			Padding_Block_left[i][(EXPAND - WID) / 2 + WID + j] = ori_pic_1[i][449];

			Padding_Block_right[i][j] = ori_pic_2[i][0];
			Padding_Block_right[i][(EXPAND - WID) / 2 + WID + j] = ori_pic_2[i][449];
		}
	}

	printf("Block padding 완료! \n");
	/////////////////////////////////////////////////////////////////////////////////////////////////////

	char** Block_Motion_Vector = (char**)malloc(HEI / BlockSize_length * sizeof(char*));
	for (i = 0; i < HEI / BlockSize_length; i++)
	{
		Block_Motion_Vector[i] = (char*)malloc(WID / BlockSize_length * sizeof(char));
	}
	printf("Block Motion Vector 생성 및 0 초기화 완료! \n");

	for (i = 0, e = 0; i < HEI; i = i + BlockSize_length, e++)
	{
		for (j = 0, r = 0; j < WID; j = j + BlockSize_length, r++)
		{
			min = 99999;

			for (o = -EXPAND_length; o < EXPAND_length-3; o++)
			{
				result = 0;

				for (k = 0; k < BlockSize_length; k++)
				{
					for (v = 0; v < BlockSize_length; v++)
					{
						result += (int)abs((double)Padding_Block_right[i + k][(j + EXPAND_length) + v] - (double)Padding_Block_left[i + k][(j + EXPAND_length) + o + v]);


					}
				}
				if (min > result)
				{
					min = result;
					h = o;
				}

			}
			Block_Motion_Vector[e][r] = 255/50*(char)abs((double)h) + 1;

		}
	}

	printf("Block_Motion_Vector 저장 완료! \n");

	BYTE ** modi_pic_1 = (BYTE**)malloc(HEI * sizeof(BYTE*));		
	for (i = 0; i < HEI; i++)
	{
		modi_pic_1[i] = (BYTE*)malloc(WID * sizeof(BYTE));
	}

	for (i = 0, e = 0; i < HEI; i = i + BlockSize_length, e++)
	{
		for (j = 0, r = 0; j < WID; j = j + BlockSize_length, r++)
		{
			c = (BYTE)Block_Motion_Vector[e][r];


			for (o = 0; o < BlockSize_length; o++)
			{
				for (p = 0; p < BlockSize_length; p++)
				{
					modi_pic_1[i + o][j + p] = c;
				}
			}
		}
	}

	printf("DE 완료! \n");

	for (i = 0; i < HEI; i++)
	{
		fwrite(modi_pic_1[i], sizeof(BYTE), WID, fout_block);
	}

	printf("파일 저장 완료! \n");

	fclose(fin_left);
	fclose(fin_right);
	fclose(fout_block);

	for (i = 0; i < HEI; i++)
	{
		free(ori_pic_1[i]);
		free(ori_pic_1_color[i]);
		free(ori_pic_2[i]);
		free(ori_pic_2_color[i]);
		free(Padding_Block_left[i]);
	}
	free(Padding_Block_left);
	free(ori_pic_1);
	free(ori_pic_1_color);
	free(ori_pic_2);
	free(ori_pic_2_color);
}

```
