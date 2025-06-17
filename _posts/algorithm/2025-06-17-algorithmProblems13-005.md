---
layout: single
title: 백준 9663번 - N-Queen
categories: algorithmProblems
tags: [algorithm, backtracking]
toc: true
toc_sticky: true
---

## 문제
[백준 9663번 문제](https://www.acmicpc.net/problem/9663)

N-Queen 문제는 크기가 N × N인 체스판 위에 퀸 N개를 서로 공격할 수 없게 놓는 문제이다.

N이 주어졌을 때, 퀸을 놓는 방법의 수를 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 N이 주어진다. (1 ≤ N < 15)

## 출력

첫째 줄에 퀸 N개를 서로 공격할 수 없게 놓는 경우의 수를 출력한다.

## 예제 입력 1 복사

```
8
```

## 예제 출력 1 복사

```
92
```

### 2-1. 풀이 및 정답코드

![Pasted image 20230628180958.png](/assets/images/algorithm/Pasted image 20230628180958.png)

![KakaoTalk_20230628_180916422.jpg](/assets/images/algorithm/KakaoTalk_20230628_180916422.jpg)

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int n;
int result = 0;
int arr[15];

bool Check(int row);
void nQueen(int row);

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n;
	nQueen(0);
	cout << result;
}

//같은 줄 또는 대각선에 있는지 검사
bool Check(int row)
{
	for (int i = 0; i < row; ++i)
	{
		//arr[row] == arr[i] : 같은 열에 있는지 검사
		//row - i == abs(arr[row] - arr[i]) : 대각선에 있는지 검사
		if (arr[row] == arr[i] || row - i == abs(arr[row] - arr[i]))
			return false;
	}
	
	return true;
}

//백트래킹 시작
void nQueen(int row)
{
	if (row == n)
	{
		result++;
		return;
	}

	for (int i = 0; i < n; ++i)
	{
		arr[row] = i; //현재 행의 몇번째 열에 퀸을 놓을 것인지?
		if (Check(row)) //놓을 수 있는 위치인가?
			nQueen(row + 1); //다음 행으로 진행
	}
}
{% endhighlight %}
   
### 2-2. 다른 풀이 및 정답코드

2-1번 풀이를 이해해야 좀 더 이해하기 쉽다

![KakaoTalk_20230715_204910736.jpg](/assets/images/algorithm/KakaoTalk_20230715_204910736.jpg)

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int n;
int result = 0;

bool isUsed1[40];	// 세로
bool isUsed2[40];	// [/] 방향 대각선
bool isUsed3[40];	// [\] 방향 대각선

void nQueen(int row);
int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n;
	nQueen(0);
	cout << result;
}

void nQueen(int row) //row번째 줄에 퀸을 배치할 예정
{
	if (row == n) //n개를 놓는데 성공
	{
		result++;
		return;
	}

	for (int i = 0; i < n; ++i) // (row, i)에 퀸을 놓을 예정
	{
		//세로나 대각선에 이미 퀸이 있다면
		if (isUsed1[i] || isUsed2[i + row] || isUsed3[row - i + n - 1])
			continue;
		isUsed1[i] = 1; //세로에 방문표시
		isUsed2[i + row] = 1; // [/] 방향 대각선 방문표시
		isUsed3[row - i + n - 1] = 1; // [\] 방향 대각선 방문표시
		nQueen(row + 1);
		isUsed1[i] = 0;
		isUsed2[i + row] = 0;
		isUsed3[row - i + n - 1] = 0;
	}
}
{% endhighlight %}
