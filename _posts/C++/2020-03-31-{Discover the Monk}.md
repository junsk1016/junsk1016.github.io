---
layout: single
title: "Discover the Monk - Hackerearth"
categories:
  - C++
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

#### 입력
The first line contains two integers, N and Q, denoting the size of array A and number of queries. The second line contains N space separated integers, denoting the array of elements Ai. The next Q lines contain a single integer X per line.  

Constraints:  
1 ≤ N,Q ≤ 10^5  
1 ≤ A_i ≤ 10^9  
1 ≤ X ≤ 10^9  
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

#### 출력
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

#### 정답
```c++
#include<iostream>
#include<vector>
#include<algorithm>
#include<cstdio>

using namespace std;

int main()
{
	ios_base::sync_with_stdio(0);
	cin.tie(0);

	int N = 0;
	int Q = 0;

	vector<int> v1;
	vector<int> v2;

	cin >> N >> Q;
	v1.resize(N);
	v2.resize(Q);

	for (int i = 0; i < N; ++i)
	{
		cin >> v1[i];
	}
	for (int i = 0; i < Q; ++i)
	{
		cin >> v2[i];
	}

	sort(v1.begin(), v1.end());
	int number = 0;

	for (int i = 0; i < Q; ++i)
	{
		number = v2[i];
		if (binary_search(v1.begin(), v1.end(), number))
			printf("YES\n");
		else
			printf("NO\n");
	}

	return 0;
}
```

---
