---
layout: single
title: "C - raw파일을 bmp파일로 변환하기"
categories:
  - C
classes: wide
tags:
  - github
  - blog
  - C
  - Visual studio
---

#### 이번 post는 raw파일의 이미지를 읽은 후 bmp파일로 변환해 저장하는 문제입니다.

#### 입력의 lena파일은 256*256 size의 컬러 사진파일입니다.
#### 파일 입,출력 포스팅에 있는 주석들은 생략하였습니다.

```c
#pragma warning(disable:4996)
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef unsigned short WORD;
typedef unsigned long  DWORD;
typedef unsigned long  LONG;
typedef unsigned char  BYTE;

#ifndef BI_RGB  
#define BI_RGB 0  
#endif

#define T_SIZE 3*256*256                   

void BGRtoRGB(BYTE* BGR, BYTE* RGB, unsigned int Size);

void change(BYTE* Image);

unsigned char* alloc_pic(int SIZE);


#pragma pack(1)
typedef struct tagBITMAPFILEHEADER
{
	WORD   bfType;         //BM이라는 캐리터형 저장
	DWORD  bfSize;         //파일의 전체 크기 표시
	WORD   bfReserved1;      //사용하지 않음
	WORD   bfReserved2;      //사용하지 않음
	DWORD  bfOffBits;      //실질 데이터(pixel)의 시작좌표를 OFFSET주소
}BITMAPFILEHEADER;
typedef struct tagBITMAPINFOHEADER
{
	DWORD   biSize;            //헤더 크기(최소 40바이트)
	LONG    biWidth;         //이미지 폭
	LONG    biHeight;         //이미지 높이
	WORD    biPlanes;         //현재 지원값은 1
	WORD    biBitCount;         //비트수 1, 4, 8, 16, 24, 32
	DWORD   biCompression;      //압축타입 : BI_RGB(0),BI_RLE8(1),BI_RLE4(2),BI_BITFIELDS(3)
	DWORD   biSizeImage;      //이미지 크기
	LONG    biXPelsPerMeter;   //미터당 픽셀수 x축
	LONG    biYPelsPerMeter;   //미터당 픽셀수 y축
	DWORD   biClrUsed;         //실질적으로 사용될 컬러맵의 엔트리 수
	DWORD   biClrImportant;      //주로 사용되는 컬러수
}BITMAPINFOHEADER;
typedef struct tagRGBTRIPLE
{
	BYTE rgbBlue;
	BYTE rgbGreen;
	BYTE rgbRed;
} RGBTRIPLE;
#pragma pack()  

// 그레이 RAW 파일을 그레이 비트맵 파일로 변환  
int Gray_Raw2Bmp(char* pRawName, DWORD nWidth, DWORD nHeight, char* pBmpName)
{
	BITMAPFILEHEADER   file_h;
	BITMAPINFOHEADER   info_h;
	DWORD            dwBmpSize = 0;
	DWORD            dwRawSize = 0;
	DWORD            dwLine = 0;
	long            lCount, i;
	FILE* in, *out;
	char* pData = NULL;
	RGBTRIPLE         rgbPal[256];

	in = fopen(pRawName, "rb");
	if (in == NULL)
	{
		printf("File Open Error!\n");
		return 0;
	}


	out = fopen(pBmpName, "wb");

	file_h.bfType = 0x4D42;
	file_h.bfReserved1 = 0;
	file_h.bfReserved2 = 0;
	file_h.bfOffBits = sizeof(BITMAPFILEHEADER) + sizeof(BITMAPINFOHEADER) + sizeof(rgbPal);
	info_h.biSize = sizeof(BITMAPINFOHEADER);
	info_h.biWidth = (DWORD)nWidth;
	info_h.biHeight = (DWORD)nHeight;
	info_h.biPlanes = 1;
	info_h.biBitCount = 8;
	info_h.biCompression = BI_RGB;
	info_h.biXPelsPerMeter = 0;
	info_h.biYPelsPerMeter = 0;
	info_h.biClrUsed = 0;
	info_h.biClrImportant = 0;




	dwLine = ((((info_h.biWidth * info_h.biBitCount) + 31) &~31) >> 3);
	dwBmpSize = dwLine * info_h.biHeight;
	info_h.biSizeImage = dwBmpSize;
	file_h.bfSize = dwBmpSize + file_h.bfOffBits + 2;

	dwRawSize = info_h.biWidth*info_h.biHeight;

	BYTE* lpImg = (BYTE*)malloc(info_h.biSizeImage * sizeof(unsigned char));
	BYTE* lpOutImg = (BYTE*)malloc(T_SIZE * sizeof(unsigned char));

	fread(lpImg, sizeof(unsigned char), info_h.biSizeImage, in);

	fwrite((char *)&file_h, 1, sizeof(BITMAPFILEHEADER), out);
	fwrite((char *)&info_h, 1, sizeof(BITMAPINFOHEADER), out);

	BGRtoRGB(lpImg, lpOutImg, T_SIZE);
	change(lpOutImg);

	fwrite(lpOutImg, sizeof(unsigned char), T_SIZE, out);

	fclose(in);
	fclose(out);

	return 1;
}

int main(int argc, char** argv)
{
	Gray_Raw2Bmp("Lena_Color.raw", 768, 256, "Copy_Lena.bmp");

	return 0;
}

void BGRtoRGB(BYTE* BGR, BYTE* RGB, unsigned int Size) // RGB로 변환 함수
{
	for (unsigned int i = 0; i < Size; i = i + 3)
	{
		RGB[i] = BGR[i + 2];
		RGB[i + 1] = BGR[i + 1];
		RGB[i + 2] = BGR[i];
	}
}

void change(BYTE* Image)    // 상하 반전 함수
{
	unsigned int i, j, ch;
	for (i = 0; i < 256 / 2; i++)

		for (j = 0; j < 768; j++)
		{
			ch = Image[i * 768 + j];
			Image[i * 768 + j] = Image[(256 - i - 1) * 768 + j];
			Image[(256 - i - 1) * 768 + j] = ch;

		}

}

unsigned char* alloc_pic(int SIZE)
{
	unsigned char* pic;

	if ((pic = (unsigned char*)calloc(SIZE, sizeof(unsigned char))) == NULL)
	{
		printf("\n malloc_picture : Picture structure \n");
		exit(1);
	}
	return pic;
}

```
