---
layout: single
title: "C - Mask Filter 적용해보기"
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

#### 이번 post는 여러 Filter를 이미지에 적용해보는 저장하는 문제입니다.  

#### 입력의 lena파일은 512*512 size의 raw 파일입니다.  
#### 파일 입,출력 포스팅에 있는 주석들은 생략하였습니다.  

적용해본 필터는 Mean, Laplacian, Sobel, Median filter입니다. 각 필터의 특징은 아래와 같습니다.  

+ Mean Filter  
  - 평균값 필터라고도 하며, 영상을 부드럽게 하는 효과가 있다.  

+ Laplacian Filter  
   - 영상의 경계를 돋보이게 하거나 경계점들을 추출한다.  

+ Sobel Filter  
  - 영상의 경계점들을 추출한다.  
  - 가로방향의 Mask와 세로방향의 Mask를 각각 영상에 적용한 후 그 결과들을 더하여 결과 영상을 출력한다.  

+	Median Filter  
  - Median Filter는 Mask를 사용하지 않지만, 3 x 3단위로 Filtering을 하는 것은 동일하다.  
  - 위의 그림에서 T의 값은 A~I의 평균값이 아닌 중간값(순서가 중간 즉, 5번째)인 값이 된다.  

각 mask filter는 아래와 같습니다.  

<p align="center"><img src="/img/Mask_Filter_1.jpg"></p>  

```c
#pragma warning(disable:4996)

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define HEI 512
#define WID 512
#define BYTE unsigned char
#define mask_size  3

int sobel_ver[3][3] = { { -1,-2,-1 },{ 0,0,0 },{ 1,2,1 } };
int sobel_hor[3][3] = { { -1,0,1 },{ -2,0,2 },{ -1,0,1 } };

void main()
{
	int i, j;
	int k, z = 0;

	FILE *fin = fopen("Lena_512.raw", "rb");
	FILE *fout1 = fopen("Mean_Lena.raw", "wb");
	FILE *fout2 = fopen("Median_Lena.raw", "wb");
	FILE *fout3 = fopen("Laplacian_1_Lena.raw", "wb");
	FILE *fout4 = fopen("Laplacian_2_Lena.raw", "wb");
	FILE *fout5 = fopen("Sobel_Ver_Lena.raw", "wb");
	FILE *fout6 = fopen("Sobel_Hor_Lena.raw", "wb");

	BYTE **ori_pic = (BYTE**)malloc(HEI * sizeof(BYTE*));
	for (i = 0; i < HEI; i++)
	{
		ori_pic[i] = (BYTE*)malloc(WID * sizeof(BYTE));
	}

	for (i = 0; i < HEI; i++)
	{
		fread(ori_pic[i], sizeof(BYTE), WID, fin);
	}
	//////////////////////////////////////////////////////////////////
	BYTE **modi_Mean = (BYTE**)malloc(HEI * sizeof(BYTE*));
	for (i = 0; i < HEI; i++)
	{
		modi_Mean[i] = (BYTE*)malloc(WID * sizeof(BYTE));
	}
	BYTE **modi_Median = (BYTE**)malloc(HEI * sizeof(BYTE*));
	for (i = 0; i < HEI; i++)
	{
		modi_Median[i] = (BYTE*)malloc(WID * sizeof(BYTE));
	}
	BYTE **modi_Laplacian_1 = (BYTE**)malloc(HEI * sizeof(BYTE*));
	for (i = 0; i < HEI; i++)
	{
		modi_Laplacian_1[i] = (BYTE*)malloc(WID * sizeof(BYTE));
	}
	BYTE **modi_Laplacian_2 = (BYTE**)malloc(HEI * sizeof(BYTE*));
	for (i = 0; i < HEI; i++)
	{
		modi_Laplacian_2[i] = (BYTE*)malloc(WID * sizeof(BYTE));
	}
	BYTE **modi_Sobel_Ver = (BYTE**)malloc(HEI * sizeof(BYTE*));
	for (i = 0; i < HEI; i++)
	{
		modi_Sobel_Ver[i] = (BYTE*)malloc(WID * sizeof(BYTE));
	}
	BYTE **modi_Sobel_Hor = (BYTE**)malloc(HEI * sizeof(BYTE*));
	for (i = 0; i < HEI; i++)
	{
		modi_Sobel_Hor[i] = (BYTE*)malloc(WID * sizeof(BYTE));
	}

	/////////////////////////////////////////////////////////////////////// MEAN MASK
	const int half_mask_size = mask_size / 2;

	for (i = half_mask_size; i < HEI - half_mask_size; i++)
	{
		for (j = half_mask_size; j < WID - half_mask_size; j++)
		{
			double temp = 0;

			for (int my = -half_mask_size; my <= half_mask_size; my++)
			{
				for (int mx = -half_mask_size; mx <= half_mask_size; mx++)
				{
					temp += ori_pic[i + my][j + mx];
				}
			}
			temp /= (mask_size * mask_size);
			modi_Mean[i][j] = (BYTE)(temp + 0.5);
		}
	}

	/////////////////////////////////////////////////////////////////////// MEDIAN MASK
	int num[mask_size*mask_size];

	for (i = half_mask_size; i < HEI - half_mask_size; i++)
	{
		for (j = half_mask_size; j < WID - half_mask_size; j++)
		{
			z = 0;

			for (int my = -half_mask_size; my <= half_mask_size; my++)
			{
				for (int mx = -half_mask_size; mx <= half_mask_size; mx++)
				{
					num[z] = ori_pic[i + my][j + mx];
					z++;
				}
			}
			int temp = 0;
			for (z = 0; z < mask_size*mask_size; z++)
			{
				for (int l = 0; l < mask_size*mask_size - 1; l++)
				{

					if (num[l] > num[l + 1]) //오름차순 배열
					{
						temp = num[l];
						num[l] = num[l + 1];

						num[l + 1] = temp;
					}
				}
			}
			k = num[mask_size*mask_size / 2];
			modi_Median[i][j] = (BYTE)k;
			for (z = 0; z < mask_size*mask_size; z++)
			{
				num[z] = 0;
			}
		}
	}

	/////////////////////////////////////////////////////////////////////// Laplacian MASK_경계 돋보이게
	for (i = half_mask_size; i < HEI - half_mask_size; i++)
	{
		for (j = half_mask_size; j < WID - half_mask_size; j++)
		{
			int temp = 0;

			for (int my = -half_mask_size; my <= half_mask_size; my++)
			{
				for (int mx = -half_mask_size; mx <= half_mask_size; mx++)
				{
					if (mx == 0 && my == 0)
						temp += 9 * ori_pic[i + my][j + my];
					else
						temp += (-1) * ori_pic[i + my][j + mx];
				}
			}
			if (temp >= 0)
				if (temp < 255)
					modi_Laplacian_1[i][j] = (BYTE)temp;
				else
					modi_Laplacian_1[i][j] = 255;
			else
				modi_Laplacian_1[i][j] = 0;
		}
	}

	/////////////////////////////////////////////////////////////////////// Laplacian MASK_경계점 추출
	for (i = half_mask_size; i < HEI - half_mask_size; i++)
	{
		for (j = half_mask_size; j < WID - half_mask_size; j++)
		{
			int temp = 0;

			for (int my = -half_mask_size; my <= half_mask_size; my++)
			{
				for (int mx = -half_mask_size; mx <= half_mask_size; mx++)
				{
					if (mx == 0 && my == 0)
						temp += 8 * ori_pic[i + my][j + my];
					else
						temp += (-1) * ori_pic[i + my][j + mx];
				}
			}
			if (temp >= 0)
				modi_Laplacian_2[i][j] = (BYTE)temp;
			else
				modi_Laplacian_2[i][j] = 0;
		}
	}

	/////////////////////////////////////////////////////////////////////// Sobel MASK_세로 방향의 경계점 추출
	for (i = half_mask_size; i < HEI - half_mask_size; i++)
	{
		for (j = half_mask_size; j < WID - half_mask_size; j++)
		{
			int temp = 0;
			int o = 0, p = 0;

			for (int my = -half_mask_size; my <= half_mask_size; my++, o++)
			{
				for (int mx = -half_mask_size; mx <= half_mask_size; mx++, p++)
				{
					temp += ori_pic[i + my][j + mx] * sobel_ver[o][p];



					//if ((mx == -half_mask_size || mx == half_mask_size) && my == -half_mask_size)
					//	temp += -1 * ori_pic[i + my][j + my];
					//else if(mx == 0 && my == -half_mask_size)
					//	temp += -2 * ori_pic[i + my][j + my];
					//else if((mx == -half_mask_size || mx == half_mask_size) && my == half_mask_size)
					//	temp += ori_pic[i + my][j + my];
					//else if (mx == 0 && my == half_mask_size)
					//	temp += 2 * ori_pic[i + my][j + my];
				}
			}
			o = 0; p = 0;

			if (temp >= 0)
				modi_Sobel_Ver[i][j] = (BYTE)temp;
			else
				modi_Sobel_Ver[i][j] = 0;
		}
	}
	/////////////////////////////////////////////////////////////////////// Sobel MASK_가로 방향의 경계점 추출
	for (i = half_mask_size; i < HEI - half_mask_size; i++)
	{
		for (j = half_mask_size; j < WID - half_mask_size; j++)
		{
			int temp = 0;
			int o = 0, p = 0;

			for (int my = -half_mask_size; my <= half_mask_size; my++, o++)
			{
				for (int mx = -half_mask_size; mx <= half_mask_size; mx++, p++)
				{
					temp += ori_pic[i + my][j + mx] * sobel_hor[o][p];
					//if(mx == -half_mask_size && (my == -half_mask_size || my == half_mask_size))
					//	temp += -1 * ori_pic[i + my][j + my];
					//else if (mx == -half_mask_size && my == 0)
					//	temp += -2 * ori_pic[i + my][j + my];
					//else if (mx == half_mask_size && (my == -half_mask_size || my == half_mask_size))
					//	temp += 1 * ori_pic[i + my][j + my];
					//else if (mx == -half_mask_size && my == 0)
					//	temp += 2 * ori_pic[i + my][j + my];

				}
			}
			o = 0; p = 0;
			if (temp >= 0)
				modi_Sobel_Hor[i][j] = (BYTE)temp;
			else
				modi_Sobel_Hor[i][j] = 0;
		}
	}

	//////////////////////
	// 가로 세로 1픽셀씩 못 넣으므로 ori_pic 에서 복사
	for (j = 0; j < WID; j++)
	{
		modi_Mean[0][j] = ori_pic[0][j];
		modi_Mean[HEI - 1][j] = ori_pic[HEI - 1][j];

		modi_Median[0][j] = ori_pic[0][j];
		modi_Median[HEI - 1][j] = ori_pic[HEI - 1][j];

		modi_Laplacian_1[0][j] = ori_pic[0][j];
		modi_Laplacian_1[HEI - 1][j] = ori_pic[HEI - 1][j];

		modi_Laplacian_2[0][j] = ori_pic[0][j];
		modi_Laplacian_2[HEI - 1][j] = ori_pic[HEI - 1][j];

		modi_Sobel_Ver[0][j] = ori_pic[0][j];
		modi_Sobel_Ver[HEI - 1][j] = ori_pic[HEI - 1][j];

		modi_Sobel_Hor[0][j] = ori_pic[0][j];
		modi_Sobel_Hor[HEI - 1][j] = ori_pic[HEI - 1][j];
	}
	for (i = 1; i < HEI - 1; i++)
	{
		modi_Mean[i][0] = ori_pic[i][0];
		modi_Mean[i][HEI - 1] = ori_pic[i][HEI - 1];

		modi_Median[i][0] = ori_pic[i][0];
		modi_Median[i][HEI - 1] = ori_pic[i][HEI - 1];

		modi_Laplacian_1[i][0] = ori_pic[i][0];
		modi_Laplacian_1[i][HEI - 1] = ori_pic[i][HEI - 1];

		modi_Laplacian_2[i][0] = ori_pic[i][0];
		modi_Laplacian_2[i][HEI - 1] = ori_pic[i][HEI - 1];

		modi_Sobel_Ver[i][0] = ori_pic[i][0];
		modi_Sobel_Ver[i][HEI - 1] = ori_pic[i][HEI - 1];

		modi_Sobel_Hor[i][0] = ori_pic[i][0];
		modi_Sobel_Hor[i][HEI - 1] = ori_pic[i][HEI - 1];
	}
	////////////////////////////////////////////////////////////
	for (i = 0; i < HEI; i++)
	{
		fwrite(modi_Mean[i], WID, sizeof(BYTE), fout1);
		fwrite(modi_Median[i], WID, sizeof(BYTE), fout2);
		fwrite(modi_Laplacian_1[i], WID, sizeof(BYTE), fout3);
		fwrite(modi_Laplacian_2[i], WID, sizeof(BYTE), fout4);
		fwrite(modi_Sobel_Ver[i], WID, sizeof(BYTE), fout5);
		fwrite(modi_Sobel_Hor[i], WID, sizeof(BYTE), fout6);
	}

	///////////////////////////////////////////////////////////////////
	for (i = 0; i < HEI; i++)
	{
		free(ori_pic[i]);
		free(modi_Mean[i]);
		free(modi_Median[i]);
		free(modi_Laplacian_1[i]);
		free(modi_Laplacian_2[i]);
		free(modi_Sobel_Ver[i]);
		free(modi_Sobel_Hor[i]);
	}

	free(ori_pic);
	free(modi_Mean);
	free(modi_Median);
	free(modi_Laplacian_1);
	free(modi_Laplacian_2);
	free(modi_Sobel_Ver);
	free(modi_Sobel_Hor);

	fclose(fin);
	fclose(fout1);
	fclose(fout2);
	fclose(fout3);
	fclose(fout4);
	fclose(fout5);
	fclose(fout6);
}
```
