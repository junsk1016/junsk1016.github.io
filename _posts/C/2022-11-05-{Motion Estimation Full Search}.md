---
layout: single
title: "C - Full search를 사용하여 Motion Estimation 구현해보기"
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

#### 이번 post는 motion estimation을 구현해보는 저장하는 문제입니다.  

#### 입력의 lena파일은 512*512 size의 raw 파일입니다.  
#### 파일 입,출력 포스팅에 있는 주석들은 생략하였습니다.  

Motion Estimaton을 구현해보겠습니다. 이전 frame으로 현재 frame을 표현하는 것을 의미하는데요. 방법은 Full search와 Hexagon Search 방법이 있습니다. 이론적인 부분들 다루지 않을게요.

이번 포스팅은 Full search를 사용하는 방법에 대한 코드입니다..!  그 중에서 픽셀 값의 차이를 구하는 방식도 SSD와 SAD가 있는데요. 둘 다 구현은 해보았지만 공유는 SAD로 구현해본것을 공유드리겠습니다.  

```c
#pragma warning(disable:4996)

#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <math.h>

#define BYTE unsigned char
#define HEI 256
#define WID 256
#define EXPAND 271	// 256 + search_size(15)
#define BLOCKSIZE_length 2  //2x2 block
#define VECTOR_length 128	//256/2
#define SEARCHSIZE_length 16



void main(void)
{
	int i, j, k, v;
	int l = 0, h = 0;
	int e = 0, r = 0;
	int o = 0, p = 0;
	int min = 99999;
	int result = 0;

	BYTE ** ori_pic_1 = (BYTE**)malloc(HEI * sizeof(BYTE*));   //frame 1 읽을 용도
	for (i = 0; i < HEI; i++)
	{
		ori_pic_1[i] = (BYTE*)malloc(WID * sizeof(BYTE));
	}
	BYTE ** ori_pic_2 = (BYTE**)malloc(HEI * sizeof(BYTE*));   //frame 2 읽을 용도
	for (i = 0; i < HEI; i++)
	{
		ori_pic_2[i] = (BYTE*)malloc(WID * sizeof(BYTE));
	}

	BYTE ** modi_pic = (BYTE**)malloc(HEI * sizeof(BYTE*));   //계산 값 저장할 용도
	for (i = 0; i < HEI; i++)
	{
		modi_pic[i] = (BYTE*)malloc(WID * sizeof(BYTE));
	}

	printf("2차원 배열 더플 포인터 생성 완료! \n");

	FILE * fin1 = fopen("frame1.raw", "rb");
	FILE * fin2 = fopen("frame2.raw", "rb");
	FILE * fout = fopen("result.raw", "wb");

	for (i = 0; i < HEI; i++) // ori_pic_1에 fin1 복사
	{
		fread(ori_pic_1[i], WID, sizeof(BYTE), fin1);
	}
	for (i = 0; i < HEI; i++) // ori_pic_2에 fin2 복사
	{
		fread(ori_pic_2[i], WID, sizeof(BYTE), fin2);
	}

	printf("스트림 생성 후 파일 읽기 완료!\n");

	BYTE scan_ori_1[HEI][WID];
	//BYTE scan_ori_2[HEI][WID];


	BYTE ** sample_1_1 = (BYTE**)malloc(EXPAND * sizeof(BYTE*));
	for (i = 0; i < EXPAND; i++)
	{
		sample_1_1[i] = (BYTE*)malloc(EXPAND * sizeof(BYTE));
	}
	//sample_1_1 는 BLOCK SIZE가 4*4일 때의 ori_pic_1 mapping

	for (i = 0; i < HEI; i++)
	{
		for (j = 0; j < WID; j++)
		{
			scan_ori_1[i][j] = ori_pic_1[i][j];
		}
	}
	BYTE padding1[EXPAND][EXPAND];

	for (i = 0; i < EXPAND; i++)
	{
		for (j = 0; j < EXPAND; j++)
		{
			padding1[i][j] = 0;
		}
	}


	for (i = 0; i < HEI; i++)
	{
		for (j = 0; j < WID; j++)
		{
			padding1[i + 6][j + 6] = scan_ori_1[i][j];  //같은 size만큼은 동일함
		}
	}
	for (i = 0; i < 6; i++)
	{
		for (j = 0; j < 6; j++)
		{
			padding1[i][j] = scan_ori_1[0][0];		// 좌측 상단

		}
	}
	// padding시 block size 때문에 1픽셀씩(총 9픽셀) 우,하단으로 늘려줘야 함
	for (i = 0; i < 9; i++)
	{
		for (j = 0; j < 9; j++)
		{
			padding1[HEI + 5 + i][WID + 5 + j] = scan_ori_1[HEI - 1][WID - 1];		// 우측 하단
		}
	}
	for (i = 0; i < 6; i++)
	{
		for (j = 0; j < 9; j++)
		{
			padding1[i][WID + 5 + j] = scan_ori_1[0][WID - 1];		// 우측 상단
		}
	}
	for (i = 0; i < 9; i++)
	{
		for (j = 0; j < 6; j++)
		{
			padding1[HEI + 5 + i][j] = scan_ori_1[HEI - 1][0];		// 좌측 하단
		}
	}

	for (i = 0; i < HEI; i++)		//상하좌우 padding
	{
		for (j = 0; j < 6; j++)
		{
			padding1[j][i + 6] = scan_ori_1[0][i];		// 위쪽
			padding1[i + 6][j] = scan_ori_1[i][0];		// 왼쪽
		}
	}
	for (i = 0; i < HEI; i++)		//상하좌우 padding  우,하단 1픽셀 증가
	{
		for (j = 0; j < 9; j++)
		{
			padding1[i + 6][WID + 5 + j] = scan_ori_1[i][WID - 1];		//오른쪽
			padding1[HEI + 5 + j][i + 6] = scan_ori_1[HEI - 1][i];		//아래쪽
		}
	}



	for (i = 0; i < EXPAND; i++)
	{
		for (j = 0; j < EXPAND; j++)
			sample_1_1[i][j] = padding1[i][j];
	}


	BYTE padding2[EXPAND][EXPAND];
	for (i = 0; i < EXPAND; i++)
	{
		for (j = 0; j < EXPAND; j++)
		{
			padding2[i][j] = 0;
		}
	}

	BYTE ** sample_1_2 = (BYTE**)malloc(EXPAND * sizeof(BYTE*));
	for (i = 0; i < EXPAND; i++)
	{
		sample_1_2[i] = (BYTE*)malloc(EXPAND * sizeof(BYTE));
	}
	//sample_1_2 는 BLOCK SIZE가 4*4일 때의 ori_pic_2 mapping

	BYTE scan_ori_2[HEI][WID];

	for (i = 0; i < HEI; i++)
	{
		for (j = 0; j < WID; j++)
		{
			scan_ori_2[i][j] = ori_pic_2[i][j];
		}
	}


	for (i = 0; i < HEI; i++)
	{
		for (j = 0; j < WID; j++)
		{
			padding2[i + 6][j + 6] = scan_ori_2[i][j];  //같은 size만큼은 동일함
		}
	}
	for (i = 0; i < 6; i++)
	{
		for (j = 0; j < 6; j++)
		{
			padding2[i][j] = scan_ori_2[0][0];		// 좌측 상단

		}
	}
	// padding시 block size 때문에 1픽셀씩(총 9픽셀) 우,하단으로 늘려줘야 함
	for (i = 0; i < 9; i++)
	{
		for (j = 0; j < 9; j++)
		{
			padding2[HEI + 5 + i][WID + 5 + j] = scan_ori_2[HEI - 1][WID - 1];		// 우측 하단
		}
	}
	for (i = 0; i < 6; i++)
	{
		for (j = 0; j < 9; j++)
		{
			padding2[i][WID + 5 + j] = scan_ori_2[0][WID - 1];		// 우측 상단
		}
	}
	for (i = 0; i < 9; i++)
	{
		for (j = 0; j < 6; j++)
		{
			padding2[HEI + 5 + i][j] = scan_ori_2[HEI - 1][0];		// 좌측 하단
		}
	}

	for (i = 0; i < HEI; i++)		//상하좌우 padding
	{
		for (j = 0; j < 6; j++)
		{
			padding2[j][i + 6] = scan_ori_2[0][i];		// 위쪽
			padding2[i + 6][j] = scan_ori_2[i][0];		// 왼쪽
		}
	}
	for (i = 0; i < HEI; i++)		//상하좌우 padding  우,하단 1픽셀 증가
	{
		for (j = 0; j < 9; j++)
		{
			padding2[i + 6][WID + 5 + j] = scan_ori_2[i][WID - 1];		//오른쪽
			padding2[HEI + 5 + j][i + 6] = scan_ori_2[HEI - 1][i];		//아래쪽
		}
	}
	//for (i = 0; i < HEI + 15; i++)
	//{
	//	for (j = 0; j < WID + 15; j++)
	//	{
	//		printf("%d ", sample_1_1[i][j]);
	//	}
	//}
	for (i = 0; i < EXPAND; i++)
	{
		for (j = 0; j < EXPAND; j++)
		{
			sample_1_2[i][j] = padding2[i][j];
		}
	}



	printf("block size가 4*4일 때 padding 완료!\n");
	/////////////////////////////////////////////////////////////////////////////////////////////////////////////// 패딩 완료

	//padding 잘 되었는지 확인하는 용도

	FILE *fout1 = fopen("Sample1.raw", "wb");
	FILE *fout2 = fopen("Sample2.raw", "wb");

	for (i = 0; i < EXPAND; i++)
	{
		fwrite(sample_1_1[i], EXPAND, sizeof(BYTE), fout1);
	}
	for (i = 0; i < EXPAND; i++)
	{
		fwrite(sample_1_2[i], EXPAND, sizeof(BYTE), fout2);
	}


	printf("sample파일 입력 완료! \n");

	//BYTE sample_1_3[HEI + 6][WID + 6];   //sample_1_3 은 sample_1_1에서 sample_1_2를 뺀 절댓값
	//for (i = 0; i < HEI + 6; i++)
	//{
	//	for (j = 0; j < WID + 6; j++)
	//	{
	//		sample_1_3[i][j] = (BYTE)abs((double)sample_1_1[i][j] - (double)sample_1_2[i][j]);
	//	}
	//}


	char scal1[VECTOR_length][VECTOR_length];		// y축으로 이동해야하는 값
	char scal2[VECTOR_length][VECTOR_length];		// x축으로 이동해야하는 값

	for (i = 0; i < VECTOR_length; i++)
	{
		for (j = 0; j < VECTOR_length; j++)

		{
			scal1[i][j] = 0;
			scal2[i][j] = 0;
		}
	}


	printf("이동할 좌표값 초기화 완료! \n");

	for (i = 0, e = 0; i < HEI; i = i + BLOCKSIZE_length, e++)
	{
		for (j = 0, r = 0; j < WID; j = j + BLOCKSIZE_length, r++)
		{


			min = 99999;
			for (o = 0; o < SEARCHSIZE_length; o++)
			{
				for (p = 0; p < SEARCHSIZE_length; p++)
				{
					result = 0;

					for (k = 0; k < BLOCKSIZE_length; k++) //함수로
					{
						for (v = 0; v < BLOCKSIZE_length; v++)
						{
							result += (int)abs((double)((int)padding2[i + k + 6][j + v + 6] - (int)padding1[i + o + k][j + p + v]));

						}
					}


					if (min > result)
					{
						min = result;
						h = o - 6;
						l = p - 6;

					}

				}
			}
			scal1[e][r] = (char)h;
			scal2[e][r] = (char)l;



		}
	}

	printf("이동해야할 좌표 값 저장 완료! \n");

	//for (i = 0; i < 128; i++)
	//{
	//	for (j = 0; j < 128; j++)
	//	{

	//		printf("%d ",scal1[i][j]);
	//	}
	//}
	//printf("\n");
	//for (i = 0; i < 128; i++)
	//{
	//	for (j = 0; j < 128; j++)
	//	{

	//		printf("%d ", scal2[i][j]);
	//	}
	//}
	//printf("\n");

	printf("SAD 계산 후 Motion vector 찾기 완료! \n");

	BYTE ** modi_pic_1 = (BYTE**)malloc(VECTOR_length * sizeof(BYTE*));		// 이동할 y축 값
	for (i = 0; i < VECTOR_length; i++)
	{
		modi_pic_1[i] = (BYTE*)malloc(VECTOR_length * sizeof(BYTE));
	}

	BYTE ** modi_pic_2 = (BYTE**)malloc(VECTOR_length * sizeof(BYTE*));		// 이동할 x축 값
	for (i = 0; i < VECTOR_length; i++)
	{
		modi_pic_2[i] = (BYTE*)malloc(VECTOR_length * sizeof(BYTE));
	}
	printf("modi_pic 1,2 생성 완료! \n");

	for (i = 0; i < VECTOR_length; i++)
	{
		for (j = 0; j < VECTOR_length; j++)
		{
			modi_pic_1[i][j] = scal1[i][j];
		}
	}

	for (i = 0; i < VECTOR_length; i++)
	{
		for (j = 0; j < VECTOR_length; j++)
		{
			modi_pic_2[i][j] = scal2[i][j];
		}
	}
	printf("sacl1과 scal2를 modi_pic 1,2에 넣음 \n");
	// scal1,scal2 를 포인터로 옮긴 것 주석처리함


	printf("Motion Vector의 HEI값과 WID값 대입 완료! \n");
	printf("frame1과 Motion Vector로 frame2 생성 시작... \n\n");


	// ori_pic_1과 scal1, scal2를 이용해 frame 2 (예측?)생성하기

	int t = 0, f = 0;
	for (i = 0, e = 0; i < HEI; i = i + BLOCKSIZE_length, e++)
	{
		for (j = 0, r = 0; j < WID; j = j + BLOCKSIZE_length, r++)
		{
			t = (int)scal1[e][r];	// y축 이동값
			f = (int)scal2[e][r];	// x축 이동값

			for (o = 0; o < BLOCKSIZE_length; o++)
			{
				for (p = 0; p < BLOCKSIZE_length; p++)
				{
					modi_pic[i + o][j + p] = padding1[(i + t + 6) + o][(j + f + 6) + p];
				}
			}
		}
	}


	// scal1에 저장된 y축 이동값이랑 scal2에 저장된 x축 이동값을 대입하여 frame2를 구성




	printf("frame1을 이용하여 frame2 구성 완료! \n");


	//for (i = 0; i < HEI; i++)
	//{
	//	for (j = 0; j < WID; j++)
	//	{

	//		printf("%d ", modi_pic[i][j]);
	//	}
	//}


	for (i = 0; i < HEI; i++)
	{
		fwrite(modi_pic[i], WID, sizeof(BYTE), fout);
	}

	printf("frame2를 fout에 입력 완료!\n");


	fclose(fin1);
	fclose(fin2);
	fclose(fout);
	fclose(fout1);
	fclose(fout2);

	for (i = 0; i < EXPAND; i++)
	{
		free(sample_1_1[i]);
		free(sample_1_2[i]);
	}

	free(sample_1_1);
	free(sample_1_2);


	for (i = 0; i < HEI; i++)
	{
		free(ori_pic_1[i]);
		free(ori_pic_2[i]);
		free(modi_pic[i]);
	}
	for (i = 0; i < 128; i++)
	{
		free(modi_pic_1[i]);
		free(modi_pic_2[i]);
	}

	free(ori_pic_1);
	free(ori_pic_2);
	free(modi_pic);
	free(modi_pic_1);
	free(modi_pic_2);



}

```
