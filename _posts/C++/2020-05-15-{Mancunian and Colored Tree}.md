---
layout: single
title: "Mancunian and Colored Tree - Hackerearth"
categories:
  - C++
tags:
  - github
  - blog
  - Hackerearth
  - C++
---
## **Mancunian and Colored Tree**
---

### 문제
---
After a hectic week at work, Mancunian and Liverbird decide to go on a fun weekend camping trip. As they were passing through a forest, they stumbled upon a unique tree of N nodes. Vertices are numbered from 1 to N.  

Each node of the tree is assigned a color (out of C possible colors). Being bored, they decide to work together (for a change) and test their reasoning skills. The tree is rooted at vertex 1. For each node, they want to find its closest ancestor having the same color.  

### 입력
---
The first line contains two integers N and C denoting the number of vertices in the tree and the number of possible colors.  
The second line contains N - 1 integers. The i<sup>th</sup> integer denotes the parent of the (i + 1)<sup>th</sup> vertex.  
The third line contains N integers, denoting the colors of the vertices. Each color lies between 1 and C inclusive.  
Constraints:  
1 ≤ N ≤ 100,000   
1 ≤ C ≤ 100,000   

```
5 4
1 1 3 3
1 4 2 1 2
```

### 출력
---
Print N space-separated integers. The i<sup>th</sup> th integer is the vertex number of lowest ancestor of the i<sup>th</sup> node which has the same color. If there is no such ancestor, print 1 for that node.  
```
-1 -1 -1 1 3
```

### 정답
---
```c++
#include <iostream>
#include <cstdio>

using namespace std;

struct Node
{
	int parent;
	int color;
};

int main()
{
	ios_base::sync_with_stdio(0);
	cin.tie(0);

	Node tree[100001] = { { 0 } };
	int N, C;

	cin >> N >> C;

	for (int i = 2; i <= N; i++)
		cin >> tree[i].parent;

	for (int i = 1; i <= N; i++)
		cin >> tree[i].color;

	for (int i = 1; i <= N - 1; i++) // 마지막 한번 뺴고(띄어쓰기때문에)
	{
		int color = tree[i].color; // i 의 색깔
		int parent = tree[i].parent; // i 의 부모

		while (true)
		{
			// 부모를 찾고 부모의 컬러와 같은가? 아니면 다시 부모를 찾고 반복
			if (color == tree[parent].color)
			{
				cout << parent << " ";
				break;
			}

			if (tree[parent].parent == 0) // 더이상 부모가 없을 경우
			{
				cout << "-1 ";
				break;
			}

			parent = tree[parent].parent; // 부모를 부모의 부모로 재설정
		}
	}
	int parent = tree[N].parent;
	int color = tree[N].color;
	while (true) // 마지막 신호에 대해
	{
		if (color == tree[parent].color)
		{
			cout << parent;
			break;
		}

		if (tree[parent].parent == 0) // 더이상 부모가 없을 경우
		{
			cout << "-1";
			break;
		}

		parent = tree[parent].parent;
	}

	return 0;
}
```

---
