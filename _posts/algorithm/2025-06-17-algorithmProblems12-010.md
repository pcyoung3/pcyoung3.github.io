---
layout: single
title: 백준 2630번 - 색종이 만들기
categories: algorithmProblems
tags: [algorithm, recursive]
toc: true
toc_sticky: true
---

## 문제
[백준 2630번 문제](https://www.acmicpc.net/problem/2630)

아래 <그림 1>과 같이 여러개의 정사각형칸들로 이루어진 정사각형 모양의 종이가 주어져 있고, 각 정사각형들은 하얀색으로 칠해져 있거나 파란색으로 칠해져 있다. 주어진 종이를 일정한 규칙에 따라 잘라서 다양한 크기를 가진 정사각형 모양의 하얀색 또는 파란색 색종이를 만들려고 한다.

![bwxBxc7ghGOedQfiT3p94KYj1y9aLR.png](/assets/images/algorithm/bwxBxc7ghGOedQfiT3p94KYj1y9aLR.png)

전체 종이의 크기가 N×N(N=2k, k는 1 이상 7 이하의 자연수) 이라면 종이를 자르는 규칙은 다음과 같다.

전체 종이가 모두 같은 색으로 칠해져 있지 않으면 가로와 세로로 중간 부분을 잘라서 <그림 2>의 I, II, III, IV와 같이 똑같은 크기의 네 개의 N/2 × N/2색종이로 나눈다. 나누어진 종이 I, II, III, IV 각각에 대해서도 앞에서와 마찬가지로 모두 같은 색으로 칠해져 있지 않으면 같은 방법으로 똑같은 크기의 네 개의 색종이로 나눈다. 이와 같은 과정을 잘라진 종이가 모두 하얀색 또는 모두 파란색으로 칠해져 있거나, 하나의 정사각형 칸이 되어 더 이상 자를 수 없을 때까지 반복한다.

위와 같은 규칙에 따라 잘랐을 때 <그림 3>은 <그림 1>의 종이를 처음 나눈 후의 상태를, <그림 4>는 두 번째 나눈 후의 상태를, <그림 5>는 최종적으로 만들어진 다양한 크기의 9장의 하얀색 색종이와 7장의 파란색 색종이를 보여주고 있다.

![VHJpKWQDv.png](/assets/images/algorithm/VHJpKWQDv.png)

입력으로 주어진 종이의 한 변의 길이 N과 각 정사각형칸의 색(하얀색 또는 파란색)이 주어질 때 잘라진 하얀색 색종이와 파란색 색종이의 개수를 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에는 전체 종이의 한 변의 길이 N이 주어져 있다. N은 2, 4, 8, 16, 32, 64, 128 중 하나이다. 색종이의 각 가로줄의 정사각형칸들의 색이 윗줄부터 차례로 둘째 줄부터 마지막 줄까지 주어진다. 하얀색으로 칠해진 칸은 0, 파란색으로 칠해진 칸은 1로 주어지며, 각 숫자 사이에는 빈칸이 하나씩 있다.

## 출력

첫째 줄에는 잘라진 햐얀색 색종이의 개수를 출력하고, 둘째 줄에는 파란색 색종이의 개수를 출력한다.

## 예제 입력 1 복사
```
8
1 1 0 0 0 0 1 1
1 1 0 0 0 0 1 1
0 0 0 0 1 1 0 0
0 0 0 0 1 1 0 0
1 0 0 0 1 1 1 1
0 1 0 0 1 1 1 1
0 0 1 1 1 1 1 1
0 0 1 1 1 1 1 1
```


## 예제 출력 1 복사

9
7
   
## 풀이
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int board[150][150];
int white, blue;

bool AllSame(int row, int col, int size);
void CutPaper(int row, int col, int size);

int main(void)
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int n;
	cin >> n;

	for (int i = 0; i < n; ++i)
		for (int j = 0; j < n; ++j)
			cin >> board[i][j];

	CutPaper(0, 0, n);

	cout << white << "\n" << blue;
}

bool AllSame(int row, int col, int size)
{
	int firstNumber = board[row][col];
	int r = row + size;
	int c = col + size;

	//시작점에 주의
	for (int i = row; i < r; ++i)
	{
		for (int j = col; j < c; ++j)
		{
			if (firstNumber != board[i][j])
				return false;
		}
	}
	return true;
}

void CutPaper(int row, int col, int size)
{
	//Base Condition
	//현재 주어진 사각형의 색깔이 모두 같거나
	//주어진 사각형의 크기가 1이하일 때
	if (AllSame(row, col, size) || size <= 1)
	{
		int firstNumber = board[row][col];
		if (firstNumber == 0)
			white += 1;
		else
			blue += 1;
		return;
	}

	//사각형이 4개로 분리되니 4개로 작업
	//왼쪽 위
	CutPaper(row, col, size / 2);
	//오른쪽 위
	CutPaper(row, col + (size / 2), size / 2);
	//왼쪽 아래
	CutPaper(row + (size / 2), col, size / 2);
	//오른쪽 아래
	CutPaper(row + (size / 2), col + (size / 2), size / 2);
}
{% endhighlight %}
