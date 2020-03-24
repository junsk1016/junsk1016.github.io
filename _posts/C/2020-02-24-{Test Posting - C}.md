---
layout: single
title: "Test Posting - C"
categories:
  - C
tags:
  - jeykll
  - github
  - Minimalmistakes
  - blog
  - C
---

Category - C test!

Example : 간단한 While문

```c
#include <stdio.h>

int main(void)
{
  int total = 0, num = 0;

  while(num!=0)
  {
    printf("정수 입력(0 to quit): ");
    scanf("%d", &num);
    total += num;
  }
  printf("합계 : %d \n", total);
  return 0;
}
```
입력으로 0이 들어올 때까지 입력한 모든 값을 더해주는 Example입니다.
