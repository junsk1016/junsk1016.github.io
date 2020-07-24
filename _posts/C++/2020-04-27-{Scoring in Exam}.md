---
layout: single
title: "Scoring in Exam - Hackerearth"
categories:
  - C++
classes: wide
tags:
  - github
  - blog
  - Hackerearth
  - C++
---
## **Scoring in Exam**
---

### 문제
---
Milly is at the examination hall where she is reading a question paper. She checked the question paper and discovered that there are N questions in that paper. Each question has some score value. Ideally it's like questions requiring more time have more score value and strangely no two questions on the paper require same time to be solved.

She is very excited by looking these questions. She decided to solve K questions while maximizing their score value. Could you please help Milly to determine the exact time she needs to solve the questions.

### 입력
---
  + First line of input contains two space separated integers N and Q, where N is the number of questions available and Q is number of queries.
  + Next line contains N space separated integers denoting the time Ti of N questions.
  + Next line contains N space separated integers denoting the scores Si of N questions.
  + Next Q lines contains a number K each, the number of questions she wants to solve.

Constraints :  
1 <= N <= 10<sup>5</sup>  
1 <= Q <= 10<sup>5</sup>    
1 <= K <= N  
1 <= T_i, S_i <= 10<sup>9</sup>    
```
5 2
2 3 9 4 5
3 5 11 6 7
5
3
```

### 출력
---
Print the time required for each query in a separate line.
```
23
18
```

### 정답
---
```c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <cstdio>

using namespace std;

struct Quest {
	int time;
	int score;
};

bool compareQuest(Quest q1, Quest q2) //  score 내림차순 정렬
{
	return(q1.score > q2.score);
}

int main()
{
	ios_base::sync_with_stdio(0);
	cin.tie(0);
	vector<Quest> v;
	int N, Q, K, i;

	cin >> N >> Q;

	for (i = 0; i < N; i++)  // N번 개의 vector size를 만들고 time만 저장
	{
		v.push_back(Quest());
		cin >> v[i].time;
	}
	for (i = 0; i < N; i++)  // 해당 score를 저장
	{
		cin >> v[i].score;
	}

	sort(v.begin(), v.end(), compareQuest);  // score를 기준 내림차순으로 정렬

	for (i = 0; i < Q; i++)
	{
		cin >> K;
		int sum = 0;
		for (int j = 0; j < K; j++)
		{
			sum += v[j].time;    // 시간이 큰 순서들로 K개 더함
		}
		cout << sum << '\n';
	}

	return 0;
}
```

---
