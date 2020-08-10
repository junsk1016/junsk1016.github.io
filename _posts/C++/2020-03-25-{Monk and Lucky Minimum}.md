---
layout: single
title: "C++ Monk and Lucky Minimun - Hackerearth"
categories:
  - C++
classes: wide
tags:
  - github
  - blog
  - Hackerearth
  - C++
---
## **Monk and Lucky Minimun**
---

### 문제
---
  Monk just purchased an array A having N integers.  
  Monk is very superstitious. He calls the array A Lucky if the frequency of the minimum element is odd, otherwise he considers it Unlucky.  
  Help Monk in finding out if the array is Lucky or not.

### 입력
---
First line consists of a single integer T denoting the number of test cases.  
First line of each test case consists of a single integer N denoting the size of array A.  
Second line of each test case consists of N space separated integers denoting the array A.  
Constraints:  
1 ≤ T ≤ 10   
1 ≤ M ≤ 10<sup>5</sup>   
1 ≤ A[i] ≤ 10<sup>9</sup>  
```
2
5
8 8 9 5 9
5
3 3 3 5 3
```

### 출력
---
For each test case, print "Lucky" (without quotes) if frequency of minimum element is odd, otherwise print "Unlucky"(without quotes). Print a new line after each test case.
```
Lucky
Unlucky
```

### 정답
---
```c++
#include <iostream>
#include <vector>
#include <limits>
#include <cstdio>
using namespace std;

int main()
{
	int tc, num, i, a; // tc = # of test case, num = # of data
	int min, freq;
	vector<int> v;

	cin >> tc;
	while (tc--)
	{
		v.clear();
		cin >> num; // data의 수 입력받음
		for (i = 0; i < num; i++) //num만큼 입력받아 vecter에 추가
		{
			cin >> a;
			v.push_back(a);
		}

		min = numeric_limits<int>::max(); // 처음 min 값으로 int형의 최댓값 넣음
		freq = 0;

		for (i = 0; i < num; i++) // min값 찾기
		{
			if (min > v[i])
				min = v[i];
		}
		for (i = 0; i < num; i++) // min값이 갯수 찾기
		{
			if (min == v[i])
				freq++;
		}
		if (freq % 2 == 1) // min의 값이 홀수 개 있으면 Lucky, 짝수 개 있으면 Unlucky 출력
			printf("Lucky\n");
		else
			printf("Unlucky\n");
	}

	return 0;
}
```
이번 문제는 vector를 이용하는 문제입니다.

Test case의 수를 입력받은 후 Data의 수를 입력하고 Data의 수 만큼 입력합니다. 그 후 입력받은 Data의 값들 중 최솟값의 수가 홀수개이면 Lucky를, 짝수개이면 Unlucky를 출력하는 문제입니다.

---
