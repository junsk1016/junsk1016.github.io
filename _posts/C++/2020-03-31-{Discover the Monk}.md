---
layout: single
title: "C++ Discover the Monk - Hackerearth"
categories:
  - C++
classes: wide
tags:
  - github
  - blog
  - Hackerearth
  - C++
---
## **Discover the Monk**
---

### 문제
---
You are given an array A of size N, and Q queries to deal with. For each query, you are given an integer X, and you're supposed to find out if X is present in the array A or not.

### 입력
---
The first line contains two integers, N and Q, denoting the size of array A and number of queries. The second line contains N space separated integers, denoting the array of elements Ai. The next Q lines contain a single integer X per line.  

Constraints:  
1 ≤ N,Q ≤ 10<sup>5</sup>  
1 ≤ A<sub>i</sub> ≤ 10<sup>9</sup>  
1 ≤ X ≤ 10<sup>9</sup>  
```
5 10
10 20 30 40 50
10
20
30
40
50
60
70
80
90
100
```

### 출력
---
For each query, print YES if the X is in the array, otherwise print NO.
```
YES
YES
YES
YES
YES
NO
NO
NO
NO
NO
```

### 정답
---
```c++
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int main()
{
	int N = 0, Q = 0;
	int num = 0;
	cin >> N >> Q;

	vector<int> v;
	v.resize(N);

	for (int i = 0; i < N; i++)
	{
		cin >> v[i];  // vector의 size만큼 입력받음
	}
	sort(v.begin(), v.end());  // 정렬(오름차순)
	for (int i = 0; i < Q; i++)
	{
		cin >> num;
		if (binary_search(v.begin(), v.end(), num)) //  num값이 있으면 YES를, 없으면 NO를 출력
			cout << "YES\n";
		else
			cout << "NO\n";
	}
	return 0;
}

```

---
