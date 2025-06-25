---
layout: single
title: 백준 15684번 - 사다리 조작
categories: algorithmProblems
tags: [algorithm, simulation]
toc: true
toc_sticky: true
---

## 문제
[백준 15684번 문제](https://www.acmicpc.net/problem/15684) <br>

사다리 게임은 N개의 세로선과 M개의 가로선으로 이루어져 있다. 인접한 세로선 사이에는 가로선을 놓을 수 있는데, 각각의 세로선마다 가로선을 놓을 수 있는 위치의 개수는 H이고, 모든 세로선이 같은 위치를 갖는다.

사다리에 가로선을 추가해서, 사다리 게임의 결과를 조작하려고 한다. 이때, i번 세로선의 결과가 i번이 나와야 한다. 그렇게 하기 위해서 추가해야 하는 가로선 개수의 최솟값을 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 세로선의 개수 N, 가로선의 개수 M, 세로선마다 가로선을 놓을 수 있는 위치의 개수 H가 주어진다. (2 ≤ N ≤ 10, 1 ≤ H ≤ 30, 0 ≤ M ≤ (N-1)×H)

둘째 줄부터 M개의 줄에는 가로선의 정보가 한 줄에 하나씩 주어진다.

가로선의 정보는 두 정수 a과 b로 나타낸다. (1 ≤ a ≤ H, 1 ≤ b ≤ N-1) b번 세로선과 b+1번 세로선을 a번 점선 위치에서 연결했다는 의미이다.

## 출력

i번 세로선의 결과가 i번이 나오도록 사다리 게임을 조작하려면, 추가해야 하는 가로선 개수의 최솟값을 출력한다. 만약, 정답이 3보다 큰 값이면 -1을 출력한다. 또, 불가능한 경우에도 -1을 출력한다.

## 예제 입력 1 복사

```
2 0 3
```

## 예제 출력 1 복사

0

## 풀이

<div class="notice--warning" markdown="1">
**<u>시간복잡도가 빡빡한 문제니 주의해야 함</u>**
</div>

<div class="notice--info" markdown="1">
**<u>가지치기를 활용한 백트레킹으로 해결</u>**
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int board[31][11];
int n, m, horizon;
int ans;

bool OOB(int r, int c);
void BackTracking(int cnt, int r);
bool SearchLadder();

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int ir, ic;
	cin >> m >> horizon >> n;
	for (int i = 0; i < horizon; i++)
	{
		cin >> ir >> ic;
		board[ir - 1][ic - 1] = 1;
	}

	ans = 0x7f7f7f7f;
	BackTracking(0, 0);
	if (ans > 3)
		cout << -1;
	else
		cout << ans;
}

bool OOB(int r, int c)
{
	return (r < 0 || r >= n || c < 0 || c >= m);
}

void BackTracking(int cnt, int r)
{
	if (cnt > 3)
		return;

	if (SearchLadder())
	{
		ans = min(cnt, ans);
		return;
	}

	for (int i = r; i < n; i++)
	{
		for (int j = 0; j < m - 1; j++)
		{
			if (OOB(i, j))
				continue;
			if (board[i][j] == 1)
				continue;
			if (j > 0 && board[i][j - 1] == 1)
				continue;
			if (board[i][j + 1] == 1)
				continue;

			board[i][j] = 1;
			BackTracking(cnt + 1, i);
			board[i][j] = 0;
		}
	}
}

bool SearchLadder()
{
	int nr = 0, nc = 0;
	for (int i = 0; i < m; i++)
	{
		nr = 0;
		nc = i;
		while (true)
		{
			if (OOB(nr, nc))
				break;

			if (board[nr][nc] == 1)
				nc++;
			else if (nc >= 1 && board[nr][nc - 1] == 1)
				nc--;
			nr++;
		}
		if (nc != i)
			return false;
	}
	return true;
}
{% endhighlight %}