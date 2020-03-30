---
layout: single
title: "Linear Search와 Binary Search에 대해"
categories:
  - C++
tags:
  - github
  - blog
  - C++
---

Linear Search와 Binary Search에 대해알아보는 시간을 가져보도록 하겠습니다.

Search Algorithm에는 linear search와 binary search가 있습니다.

1. Linear Search
 + 처음부터 하나씩 확인하는 방법
 + worst case : n 번 확인 (data가 n개 있을 때)
 + 간단한 예제를 보겠습니다.
 ```c++
 int serarch(int a[], int v, int l, int r)
 {
   int i;
   for (i = 1; i <= r; i++)
     if (v ==  a[i]) return i;
   return -1;
 }
 ```

2. Binary Search
 + 조건 : 가지고 있는 Data를 크기대로 sorting하여 저장해야 함
 + linear search보다 빨리 찾을 수 있는 장점
 + 간단한 예제를 보겠습니다.
 ```c++
 int serarch(int a[], int v, int l, int r)
 {
   while (r >= l)
   {
     int m = (l + r) / 2;
     if (v == a[m]) return m;
     if (v < a[m]) r = m - 1; else l = m + 1;
   }
 }
 ```

 다음 포스팅에서 이를 활용한 백준 문제를 풀어보도록 하겠습니다.
 
 ---
