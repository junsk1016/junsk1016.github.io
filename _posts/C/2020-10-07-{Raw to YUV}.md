---
layout: single
title: "C - raw파일을 yuv422와 yuv420 Format으로 변환하기"
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

#### 이번 post는 raw파일의 이미지를 읽은 후 yuv422와 yuv420 파일로 변환해 저장하는 문제입니다.

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

unsigned char** Mem_Alloc(int width, int height);				// Memory Allocation
void Mem_Free(BYTE** arr, int height);							// Memory Free

void File_Read(BYTE** img_in, char* filename, int width, int height);
void File_Write_422(BYTE** Y, BYTE** Cb, BYTE** Cr, char* filename, int width, int height);	// YUV422로 저장하기
void File_Write_420(BYTE** Y, BYTE** Cb, BYTE** Cr, char* filename, int width, int height);	// YUV420으로 저장하기

void RGB_to_YUV422(BYTE** img_in, BYTE** Y, BYTE** Cb, BYTE** Cr, int width, int height);	// YUV422로 만들기 위한 Y, Cb, Cr 만들기
void RGB_to_YUV420(BYTE** img_in, BYTE** Y, BYTE** Cb, BYTE** Cr, int width, int height);	// YUV420으로 만들기 위한 Y, Cb, Cr 만들기

void main()
{
	BYTE **ori_pic = Mem_Alloc(WID * 3, HEI);	//RGB 채널이므로 *3

	BYTE** Y = Mem_Alloc(WID, HEI);
	BYTE** Cb = Mem_Alloc(WID / 2, HEI);
	BYTE** Cr = Mem_Alloc(WID / 2, HEI);

	BYTE** Y1 = Mem_Alloc(WID, HEI);
	BYTE** Cb1 = Mem_Alloc(WID / 2, HEI / 2);
	BYTE** Cr1 = Mem_Alloc(WID / 2, HEI / 2);

	File_Read(ori_pic, "Lena_Color.raw", WID * 3, HEI);			// Lena RGB 읽기
	RGB_to_YUV422(ori_pic, Y, Cb, Cr, WID, HEI);				// YUV422를 위한 Y, Cb, Cr 구하기
	File_Write_422(Y ,Cb, Cr, "Lena_YUV422.yuv", WID, HEI);		// YUV422 Format에 맞춰 write

	RGB_to_YUV420(ori_pic, Y1, Cb1, Cr1, WID, HEI);				// YUV420를 위한 Y, Cb, Cr 구하기
	File_Write_420(Y1, Cb1, Cr1, "Lena_YUV420.yuv", WID, HEI);	// YUV420 Format에 맞춰 write

	Mem_Free(Y, HEI);
	Mem_Free(Cb, HEI);
	Mem_Free(Cr, HEI);

	Mem_Free(Y1, HEI);
	Mem_Free(Cb1, HEI / 2);
	Mem_Free(Cr1, HEI / 2);
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

void File_Read(BYTE** img_in, char* filename, int width, int height)
{
	FILE *fp_in;

	fp_in = fopen(filename, "rb");
	if (!fp_in)
	{
		printf("ERROR :: File Can't Read\n");
		exit(1);
	}
	for (int i = 0; i < height; i++)
	{
		fread(img_in[i], sizeof(BYTE), width, fp_in);
	}

	fclose(fp_in);

}
void File_Write_422(BYTE** Y, BYTE** Cb, BYTE** Cr, char* filename, int width, int height)
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
		fwrite((BYTE*)Y[i], sizeof(BYTE), width, fp_out);
	}
	for (i = 0; i < height; i++)
	{
		fwrite((BYTE*)Cb[i], sizeof(BYTE), width / 2, fp_out);
	}
	for (i = 0; i < height; i++)
	{
		fwrite((BYTE*)Cr[i], sizeof(BYTE), width / 2, fp_out);
	}
	fclose(fp_out);
}
void File_Write_420(BYTE** Y, BYTE** Cb, BYTE** Cr, char* filename, int width, int height)
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
		fwrite((BYTE*)Y[i], sizeof(BYTE), width, fp_out);
	}
	for (i = 0; i < height / 2; i++)
	{
		fwrite((BYTE*)Cb[i], sizeof(BYTE), width / 2, fp_out);
	}
	for (i = 0; i < height / 2; i++)
	{
		fwrite((BYTE*)Cr[i], sizeof(BYTE), width / 2, fp_out);
	}	fclose(fp_out);
}

void RGB_to_YUV422(BYTE** img_in, BYTE** Y, BYTE** Cb, BYTE** Cr, int width, int height)
{
	int i = 0, j = 0, k = 0;
	BYTE** R = Mem_Alloc(width, height);
	BYTE** G = Mem_Alloc(width, height);
	BYTE** B = Mem_Alloc(width, height);

	for (i = 0; i < height; i++)
	{
		for (j = 0, k = 0; j < width; j++, k += 3)
		{
			R[i][j] = img_in[i][k];
			G[i][j] = img_in[i][k + 1];
			B[i][j] = img_in[i][k + 2];

		}
	}

	for (i = 0; i < height; i++)
	{
		for (j = 0; j < width; j++)
		{
			Y[i][j] = ((0.257)*(double)R[i][j] + (0.504)*(double)G[i][j] + (0.098)*(double)B[i][j] + (double)16);
			if (j % 2 == 0)
			{
				Cb[i][j / 2] = ((-0.148)*(double)R[i][j] + (-0.291)*(double)G[i][j] + (0.439)*(double)B[i][j] + (double)128);
				Cr[i][j / 2] = ((0.439)*(double)R[i][j] + (-0.368)*(double)G[i][j] + (-0.071)*(double)B[i][j] + (double)128);
			}

		}
	}

	Mem_Free(R, HEI);
	Mem_Free(G, HEI);
	Mem_Free(B, HEI);
}

void RGB_to_YUV420(BYTE** img_in, BYTE** Y, BYTE** Cb, BYTE** Cr, int width, int height)
{
	int i = 0, j = 0, k = 0;
	BYTE** R = Mem_Alloc(width, height);
	BYTE** G = Mem_Alloc(width, height);
	BYTE** B = Mem_Alloc(width, height);

	for (i = 0; i < height; i++)
	{
		for (j = 0, k = 0; j < width; j++, k += 3)
		{
			R[i][j] = img_in[i][k];
			G[i][j] = img_in[i][k + 1];
			B[i][j] = img_in[i][k + 2];

		}
	}
	for (i = 0; i < HEI; i++)
	{
		for (j = 0; j < WID; j++)
		{
			Y[i][j] = ((0.257)*(double)R[i][j] + (0.504)*(double)G[i][j] + (0.098)*(double)B[i][j] + (double)16);
			if (i % 2 == 0)
			{
				if (j % 2 == 0)
				{
					Cb[i / 2][j / 2] = ((-0.148)*(double)R[i][j] + (-0.291)*(double)G[i][j] + (0.439)*(double)B[i][j] + (double)128);
					Cr[i / 2][j / 2] = ((0.439)*(double)R[i][j] + (-0.368)*(double)G[i][j] + (-0.071)*(double)B[i][j] + (double)128);
				}
			}
		}
	}

	Mem_Free(R, HEI);
	Mem_Free(G, HEI);
	Mem_Free(B, HEI);
}
```
