---
layout: single
title: "C - yuv422와 yuv420파일을 RGB Foramt으로 변환하기"
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

#### 이번 post는 yuv422와 yuv420 파일을 RGB로 변환해 저장하는 문제입니다.

#### 입력의 lena파일은 256*256 size의 컬러 사진파일입니다.
#### 파일 입,출력 포스팅에 있는 주석들은 생략하였습니다.

```c
#pragma warning(disable:4996)
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

#define BYTE unsigned char
#define WID 256
#define HEI 256
#define CLIP(x) (x < 0 ? 0 : (x > 255 ? 255 : x))

unsigned char** Mem_Alloc(int width, int height);				// Memory Allocation
void Mem_Free(BYTE** arr, int height);							// Memory Free

void File_Read_YUV422(BYTE** Y, BYTE** Cb, BYTE** Cr, char* filename, int width, int height);	// YUV422 파일 읽어서 Y, Cb, Cr로 분해
void File_Read_YUV420(BYTE** Y, BYTE** Cb, BYTE** Cr, char* filename, int width, int height);	// YUV420 파일 읽어서 Y, Cb, Cr로 분해

void File_Write(BYTE** RGB, char* filename, int width, int height);								// RGB 저장하기

void YUV422_to_RGB(BYTE** RGB, BYTE** Y, BYTE** Cb, BYTE** Cr, int width, int height);			// RGB로 만들기 위한 R, G, B 만들기
void YUV420_to_RGB(BYTE** RGB, BYTE** Y, BYTE** Cb, BYTE** Cr, int width, int height);

void main()
{
	BYTE** Y = Mem_Alloc(WID, HEI);
	BYTE** Cb = Mem_Alloc(WID / 2, HEI);
	BYTE** Cr = Mem_Alloc(WID / 2, HEI);

	BYTE** Y1 = Mem_Alloc(WID, HEI);
	BYTE** Cb1 = Mem_Alloc(WID / 2, HEI / 2);
	BYTE** Cr1 = Mem_Alloc(WID / 2, HEI / 2);

	BYTE** RGB = Mem_Alloc(WID * 3, HEI);

	File_Read_YUV422(Y, Cb, Cr, "YUV422_Lena_Color.yuv", WID, HEI);				// Lena YUV422 읽고 각 채널로 받기
	YUV422_to_RGB(RGB, Y, Cb, Cr, WID, HEI);									// RGB로 변환
	File_Write(RGB, "Lena_YUV422_to_RGB.raw", WID, HEI);						// RGB file write


	File_Read_YUV420(Y1, Cb1, Cr1, "YUV420_Lena_Color.yuv", WID, HEI);			// Lena YUV420 읽고 각 채널로 받기
	YUV420_to_RGB(RGB, Y1, Cb1, Cr1, WID, HEI);									// RGB로 변환
	File_Write(RGB, "Lena_YUV420_to_RGB.raw", WID, HEI);						// RGB file write

	Mem_Free(Y, HEI);
	Mem_Free(Cb, HEI);
	Mem_Free(Cr, HEI);

	Mem_Free(Y1, HEI);
	Mem_Free(Cb1, HEI / 2);
	Mem_Free(Cr1, HEI / 2);

	Mem_Free(RGB, HEI);
}

unsigned char** Mem_Alloc(int width, int height)
{
	BYTE **arr;

	arr = (BYTE**)malloc(sizeof(BYTE*) * height);
	for (int i = 0; i < height; i++)
	{
		arr[i] = (BYTE*)malloc(sizeof(BYTE) * width);
	}

	return arr;

}
void Mem_Free(BYTE** arr, int height)
{
	for (int i = 0; i < height; i++)
	{
		free(arr[i]);
	}
	free(arr);

}
void File_Read_YUV422(BYTE** Y, BYTE** Cb, BYTE** Cr, char* filename, int width, int height)
{
	FILE *fp_in = fopen(filename, "rb");

	if (!fp_in)
	{
		printf("ERROR :: File Can't Read\n");
		exit(1);
	}
	for (int i = 0; i < height; i++)
	{
		fread(Y[i], sizeof(BYTE), width, fp_in);
	}
	for (int i = 0; i < height; i++)
	{
		fread(Cb[i], sizeof(BYTE), width / 2, fp_in);
	}
	for (int i = 0; i < height; i++)
	{
		fread(Cr[i], sizeof(BYTE), width / 2, fp_in);
	}

	fclose(fp_in);
}
void File_Read_YUV420(BYTE** Y, BYTE** Cb, BYTE** Cr, char* filename, int width, int height)
{
	FILE *fp_in = fopen(filename, "rb");

	if (!fp_in)
	{
		printf("ERROR :: File Can't Read\n");
		exit(1);
	}
	for (int i = 0; i < height; i++)
	{
		fread(Y[i], sizeof(BYTE), width, fp_in);
	}
	for (int i = 0; i < height / 2; i++)
	{
		fread(Cb[i], sizeof(BYTE), width / 2, fp_in);
	}
	for (int i = 0; i < height / 2; i++)
	{
		fread(Cr[i], sizeof(BYTE), width / 2, fp_in);
	}

	fclose(fp_in);
}
void File_Write(BYTE** RGB, char* filename, int width, int height)
{
	FILE *fp_out;
	int i = 0;
	fp_out = fopen(filename, "wb");

	if (!fp_out)
	{
		printf("ERROR :: File Can't Save\n", filename);
		exit(1);
	}

	for (i = 0; i < height; i++)
	{
		fwrite((BYTE*)RGB[i], sizeof(BYTE), width * 3, fp_out);
	}

	fclose(fp_out);
}
void YUV422_to_RGB(BYTE** RGB,BYTE** Y, BYTE** Cb, BYTE** Cr, int width, int height)
{
	int i = 0, j = 0, k = 0;
	int R = 0, G = 0, B = 0;

	BYTE** Cb_up = Mem_Alloc(width, height);
	BYTE** Cr_up = Mem_Alloc(width, height);

	for (i = 0; i < height; i++)								// Cb 채널 Up-sampling
	{
		for (j = 0, k = 0; j < width; j = j + 2, k++)
		{
			Cb_up[i][j] = Cb[i][k];
			Cb_up[i][j + 1] = Cb[i][k];
		}
	}
	for (i = 0; i < height; i++)
	{
		for (j = 0, k = 0; j < width; j = j + 2, k++)			// Cr 채널 Up-sampling
		{
			Cr_up[i][j] = Cr[i][k];
			Cr_up[i][j + 1] = Cr[i][k];
		}
	}

	for (i = 0; i < height; i++)
	{
		for (j = 0,k = 0; j < width; j++, k +=3)
		{
			R = ((1.164)*(double)(Y[i][j] - 16) + (1.596)*(double)(Cr_up[i][j] - 128));
			G = ((1.164)*(double)(Y[i][j] - 16) - (0.813)*(double)(Cr_up[i][j] - 128) - (0.392)*(double)(Cb_up[i][j] - 128));
			B = ((1.164)*(double)(Y[i][j] - 16) + (2.017)*(double)(Cb_up[i][j] - 128));
			RGB[i][k] = CLIP(R);
			RGB[i][k + 1] = CLIP(G);
			RGB[i][k + 2] = CLIP(B);
		}
	}

	Mem_Free(Cb_up, height);
	Mem_Free(Cr_up, height);
}

void YUV420_to_RGB(BYTE** RGB, BYTE** Y, BYTE** Cb, BYTE** Cr, int width, int height)
{
	int i = 0, j = 0, r = 0, k = 0;
	int R = 0, G = 0, B = 0;

	BYTE** Cb_up = Mem_Alloc(width, height);
	BYTE** Cr_up = Mem_Alloc(width, height);

	for (i = 0, r = 0; i < height; i += 2, k++)				// Cb 채널 Up-sampling
	{
		for (j = 0, k = 0; j < width; j += 2, k++)
		{
			Cb_up[i][j] = Cb[r][k];
			Cb_up[i][j + 1] = Cb[r][k];
			Cb_up[i + 1][j] = Cb[r][k];
			Cb_up[i + 1][j + 1] = Cb[r][k];
		}
	}
	for (i = 0, r = 0; i < height; i += 2, r++)				// Cr 채널 Up-sampling
	{
		for (j = 0, k = 0; j < width; j += 2, k++)
		{
			Cr_up[i][j] = Cr[r][k];
			Cr_up[i + 1][j] = Cr[r][k];
			Cr_up[i][j + 1] = Cr[r][k];
			Cr_up[i + 1][j + 1] = Cr[r][k];
		}
	}

	for (i = 0; i < height; i++)
	{
		for (j = 0, k = 0; j < width; j++, k += 3)
		{
			R = ((1.164)*(double)(Y[i][j] - 16) + (1.596)*(double)(Cr_up[i][j] - 128));
			G = ((1.164)*(double)(Y[i][j] - 16) - (0.813)*(double)(Cr_up[i][j] - 128) - (0.392)*(double)(Cb_up[i][j] - 128));
			B = ((1.164)*(double)(Y[i][j] - 16) + (2.017)*(double)(Cb_up[i][j] - 128));
			RGB[i][k] = CLIP(R);
			RGB[i][k + 1] = CLIP(G);
			RGB[i][k + 2] = CLIP(B);
		}
	}

	Mem_Free(Cb_up, height);
	Mem_Free(Cr_up, height);
}
```
