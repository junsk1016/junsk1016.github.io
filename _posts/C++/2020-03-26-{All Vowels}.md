---
layout: single
title: "All Vowels - Hackerearth"
categories:
  - C++
tags:
  - github
  - blog
  - Hackerearth
  - C++
---
## **All Vowels**
---

### 문제
---
Vowels are very essential characters to form any meaningful word in English dictionary. There are 5 vowels in English language - a, e, i, o u. You are given a randoms string containing only lowercase letters and you need to find if the string contains ALL the vowels.

### 입력
---
FIrst line contains N , the size of the string.  
Second line contains the letters (only lowercase).  

Constraints:  
The size of the string will not be greater than 10,000  
1 ≤ N ≤ 10000  
```
8
atuongih
```

### 출력
---
Print "YES" (without the quotes) if all vowels are found in the string, "NO" (without the quotes) otherwise.
```
NO
```

### 정답
---
```c++
#include <iostream>
#include <string>
using namespace std;

int main()
{
	int i,n;
	string s;
	cin >> n >> s; // n과 s를 입력받음
	int countA = 0, countE = 0, countI = 0, countO = 0, countU = 0;

	for (i = 0; i < n; i++)  // 모음들의 갯수 count
	{
		if (s[i] == 'a')
			countA++;		
		if (s[i] == 'e')		
			countE++;		
		if (s[i] == 'i')		
			countI++;		
		if (s[i] == 'o')
			countO++;		
		if (s[i] == 'u')		
			countU++;		
	}

	if (countA > 0 && countE > 0 && countI > 0 && countO > 0 && countU > 0)
		cout << "YES";
	else
		cout << "NO";
  // 모음이 모두 있으면 YES, 하나라도 없으면 0을 출력
	return 0;
}
```
이번 문제는  for문과 if문의 사용을 묻는 간단한 예제입니다.

---
