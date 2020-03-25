---
layout: single
title: "Algorithm Efficiency에 대해"
categories:
  - C++
tags:
  - github
  - blog
  - C++
---

Algorithm Efficiency에 대해 알아보는 시간을 가져보도록 하겠습니다.

같은 문제에 대해 알고리즘이 각각 다를 수 있습니다. 과연 어떤 알고리즘이 더 좋은 것일까요?

그 중 하나의 기준으로 runnning time을 볼 수 있습니다.
worst-case runnig time이라고 하는데요. worst case에 대해서 최대 몇 개의 line이 실행되는지 알 수 있습니다.

예를 하나 가져와보겠습니다.
```c++
...

    for (i = 0; i < n; i++)
    {
      if (a[i] < a[i - 1])
      {
        temp = a[i - 1]
        a[i - 1] = a[i];
        a[i] = temp;
      }

    }
    printf("\n");
...
```

for문에서 i가 n번 실행되고 if문이 실행된다면 4 lines을 실행하므로 worst case의 경우 printf()까지 합쳐 f(n) = 4n + 1가 됩니다.

이렇게 여러개의 반복문에 대해서도 f(n)을 구할 수 있습니다. 여러개의 예들을 더 확인해보겠습니다.

```
Constants
  c lines of statements;  -> f(n) = c

Linear loops
  for (i = 0; i < n; i++)
  {
    application code (c lines)
  }                       -> f(n) = c*n

  for (i = 0; i < n; i += 2)
  {
    application code (c lines)
  }                       -> f(n) = c*n/2

Multiply loops
  for(i = 2; i <= n; i *= 2)
   application code (c lines)
   termination condition : 2^Iteration > n
                          -> f(n) = c*log_2(n)

Divide loops
  for(i = 2; i <= n; i /= 2)
   application code (c lines)
   termination condition : (n / 2^Iteration) < 2
                          -> f(n) = c*log_2(n)

Quadratic (2중 반복문)
  for (i = 0; i < n; i++)       outer : n iterations
  {
    for (j = 0; j < n; j++)    inner : n iterations
  }
                          -> f(n) = c*n^2

Dependent quadratic       
  for (i = 0; i < n; i++)       outer : n
  {
    for ( j = 0; j <= n; j++)   inner : (n-1)/2
  }
                          -> f(n) = c*n*(n-1)/2

Linear logarithm
  for (i = 0; i < n; i++)       outer : n
  {
    for (j = 0; j < n; j *= 2)  inner : log_2(n)
  }
                          -> f(n) = c*nlog_2(n)
```
---
여러개의 f(n)을 확인할 수 있었습니다. 그렇다면 f(n) = 15n^2 + 45n + 2000이 있다고 가정해봅시다.
n이 커질수록 어떤 것이 dominant해질까요? 직감적으로도 n^2이란 것을 알 수 있습니다.

다시 말해, n이 커질수록 n^2이 기하급수적으로 늘어납니다.
여기서 n을 data로 볼 수 있습니다. 알고리즘에 따라 f(n)은 바뀌게 되고 n의 차수가 낮을수록 f(n)의 값이 작음을 확인할 수 있습니다.

즉, data가 커질수록 알고리즘이 중요하다는 것을 알 수 있습니다.

앞서 볼 수 있듯이 최고 차항이 중요한 것을 알 수 있는데요. 이렇게 최고차랑만 고려하는 것을 asymptotic efficiency of algorithms라고 합니다.
