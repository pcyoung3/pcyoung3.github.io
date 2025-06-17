---
layout: single
title: 백준 2447번 - 별찍기 10
categories: algorithmProblems
tags: [algorithm, recursive]
toc: true
toc_sticky: true
---

## 문제
[백준 2447번 문제](https://www.acmicpc.net/problem/2447)

재귀적인 패턴으로 별을 찍어 보자. N이 3의 거듭제곱(3, 9, 27, ...)이라고 할 때, 크기 N의 패턴은 N×N 정사각형 모양이다.

크기 3의 패턴은 가운데에 공백이 있고, 가운데를 제외한 모든 칸에 별이 하나씩 있는 패턴이다.
```
***
* *
***
```
N이 3보다 클 경우, 크기 N의 패턴은 공백으로 채워진 가운데의 (N/3)×(N/3) 정사각형을 크기 N/3의 패턴으로 둘러싼 형태이다. 예를 들어 크기 27의 패턴은 예제 출력 1과 같다.

## 입력

첫째 줄에 N이 주어진다. N은 3의 거듭제곱이다. 즉 어떤 정수 k에 대해 $N=3^k$이며, 이때 1 ≤ k < 8이다.

## 출력

첫째 줄부터 N번째 줄까지 별을 출력한다.

## 예제 입력 1 복사

27

## 예제 출력 1
```
***************************
* ** ** ** ** ** ** ** ** *
***************************
***   ******   ******   ***
* *   * ** *   * ** *   * *
***   ******   ******   ***
***************************
* ** ** ** ** ** ** ** ** *
***************************
*********         *********
* ** ** *         * ** ** *
*********         *********
***   ***         ***   ***
* *   * *         * *   * *
***   ***         ***   ***
*********         *********
* ** ** *         * ** ** *
*********         *********
***************************
* ** ** ** ** ** ** ** ** *
***************************
***   ******   ******   ***
* *   * ** *   * ** *   * *
***   ******   ******   ***
***************************
* ** ** ** ** ** ** ** ** *
***************************
```
   
## 풀이
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

char board[2300][2300];

void DrawStar(int row, int col, int size)
{
	//탈출문구 - 가장 작은 도형 그리고 나오기
	//n == 1일 때 *을 그리고 나오는 것도 가능
	if (size <= 3)
	{
		for (int i = row; i < row + 3; ++i)
		{
			for (int j = col; j < col + 3; ++j)
			{
				if (i == row + 1 && j == col + 1)
					continue;
				board[i][j] = '*';
			}
		}
		return;
	}

	//총 9번중에 가운데를 제외한 8번을 돌림
	int rectSize = size / 3;
	for (int i = 0; i < 3; i++)	//가로 3번
	{
		for (int j = 0; j < 3; j++)	//세로 3번
		{
			if (i == 1 && j == 1)
				continue;
			
			//현재인덱스 + 가로세로 1/3등분한 사각형
			DrawStar(row + i * rectSize, col + j * rectSize, rectSize);
		}
	}
}

int main()
{
    ios_base::sync_with_stdio(false);
	cin.tie(NULL);

	int n;
	cin >> n;

	for (int i = 0; i < n; ++i)
		fill(board[i], board[i] + n, ' ');

	DrawStar(0, 0, n);

	for (int i = 0; i < n; ++i)
	{
		for (int j = 0; j < n; ++j)
		{
			cout << board[i][j];
		}
		cout << "\n";
	}
}
{% endhighlight %}
   
### 다른 풀이
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

//한 칸에 대해서 판단
void print_star(int i, int j, int num) 
{
	//텅 빈 곳인지 판단하는 문구
	if ((i / num) % 3 == 1 && (j / num) % 3 == 1)
		cout << ' ';
	else 
	{
		if (num / 3 == 0)
			cout << '*';
		else
			print_star(i, j, num / 3); //재귀적으로 들어가도 해당 칸이 *인지 공백인지 판단 후 찍고 나옴
	}
}

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int num;
	cin >> num;

	//모든 칸 순회
	for (int i = 0; i < num; i++) 
	{
		for (int j = 0; j < num; j++)
			print_star(i, j, num);

		cout << '\n';
	}
}
{% endhighlight %}
