---
layout: single
title: 백준 7569번 - 토마토
categories: algorithmProblems
tags: [algorithm, BFS]
toc: true
toc_sticky: true
---

## 문제
[백준 7569번 문제](https://www.acmicpc.net/problem/7569)

철수의 토마토 농장에서는 토마토를 보관하는 큰 창고를 가지고 있다. 토마토는 아래의 그림과 같이 격자모양 상자의 칸에 하나씩 넣은 다음, 상자들을 수직으로 쌓아 올려서 창고에 보관한다.

![7569_p_01.jpg](/assets/images/algorithm/7569_p_01.jpg)

창고에 보관되는 토마토들 중에는 잘 익은 것도 있지만, 아직 익지 않은 토마토들도 있을 수 있다. 보관 후 하루가 지나면, 익은 토마토들의 인접한 곳에 있는 익지 않은 토마토들은 익은 토마토의 영향을 받아 익게 된다. 하나의 토마토에 인접한 곳은 위, 아래, 왼쪽, 오른쪽, 앞, 뒤 여섯 방향에 있는 토마토를 의미한다. 대각선 방향에 있는 토마토들에게는 영향을 주지 못하며, 토마토가 혼자 저절로 익는 경우는 없다고 가정한다. 철수는 창고에 보관된 토마토들이 며칠이 지나면 다 익게 되는지 그 최소 일수를 알고 싶어 한다.

토마토를 창고에 보관하는 격자모양의 상자들의 크기와 익은 토마토들과 익지 않은 토마토들의 정보가 주어졌을 때, 며칠이 지나면 토마토들이 모두 익는지, 그 최소 일수를 구하는 프로그램을 작성하라. 단, 상자의 일부 칸에는 토마토가 들어있지 않을 수도 있다.

## 입력

첫 줄에는 상자의 크기를 나타내는 두 정수 M,N과 쌓아올려지는 상자의 수를 나타내는 H가 주어진다. M은 상자의 가로 칸의 수, N은 상자의 세로 칸의 수를 나타낸다. 단, 2 ≤ M ≤ 100, 2 ≤ N ≤ 100, 1 ≤ H ≤ 100 이다. 둘째 줄부터는 가장 밑의 상자부터 가장 위의 상자까지에 저장된 토마토들의 정보가 주어진다. 즉, 둘째 줄부터 N개의 줄에는 하나의 상자에 담긴 토마토의 정보가 주어진다. 각 줄에는 상자 가로줄에 들어있는 토마토들의 상태가 M개의 정수로 주어진다. 정수 1은 익은 토마토, 정수 0 은 익지 않은 토마토, 정수 -1은 토마토가 들어있지 않은 칸을 나타낸다. 이러한 N개의 줄이 H번 반복하여 주어진다.

토마토가 하나 이상 있는 경우만 입력으로 주어진다.

## 출력

여러분은 토마토가 모두 익을 때까지 최소 며칠이 걸리는지를 계산해서 출력해야 한다. 만약, 저장될 때부터 모든 토마토가 익어있는 상태이면 0을 출력해야 하고, 토마토가 모두 익지는 못하는 상황이면 -1을 출력해야 한다.

## 예제 입력 1 복사

```
5 3 1
0 -1 0 0 0
-1 -1 0 1 1
0 0 0 1 1
```

## 예제 출력 1 복사

-1

## 예제 입력 2 복사

```
5 3 2
0 0 0 0 0
0 0 0 0 0
0 0 0 0 0
0 0 0 0 0
0 0 1 0 0
0 0 0 0 0
```

## 예제 출력 2 복사

```
4
```
   
---
## 풀이
<div class="notice--danger" markdown="1">
**<u>거리를 이용한 BFS이지만 문제는 3차원이다</u>**

![KakaoTalk_20230602_074919877.jpg](/assets/images/algorithm/KakaoTalk_20230602_074919877.jpg)

1. 가로줄이 m으로 들어오고 세로줄이 n으로 들어와서 x가 m에 대응 y가 n에 대응할 것 같지만 좌표를 봤을 때 예를 들어 (0, 2, 1)이 어디에 대응하는지 생각해보면 x가 n에 대응, y가 m에 대응한다
2. for문의 경우 가장 많이 반복되어야 할 부분은 안쪽에 놓고 가장 단위가 큰 부분을 바깥에 놓는 개념으로 생각하면 한 줄에 5번째 원소를 x,y,z순으로 생각했을 때 (0, 4, 0)에 대응된다. 따라서 y를 가장 안쪽 for문에 배치
</div>

{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

int board[105][105][105];
int dist[105][105][105];

int dx[6] = { 1, -1, 0, 0, 0, 0 };
int dy[6] = { 0, 0, 1, -1, 0, 0 };
int dz[6] = { 0, 0, 0, 0, 1, -1 };
int main(void)
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int m, n, h;
	cin >> m >> n >> h;

	queue<tuple<int, int, int>> Q;
	//3차원배열에 주의
	//k > x > y 순서(2차원일때 x y 순서에 k만 추가)
	for (int k = 0; k < h; ++k)		//k개의 상자
	{
		for (int i = 0; i < n; ++i)		//i개의 세로줄
		{
			for (int j = 0; j < m; ++j)		//j개의 가로줄
			{
				int tmp;
				cin >> tmp;
				board[i][j][k] = tmp;
				if (tmp == 1)
					Q.push({ i, j, k });
				if (tmp == 0)	// 익지 않은 토마토를 -1로 둠
					dist[i][j][k] = -1;
			}
		}
	}

	while (!Q.empty())
	{
		int curX, curY, curZ;
		auto cur = Q.front();
		tie(curX, curY, curZ) = cur;
		Q.pop();
		for (int dir = 0; dir < 6; ++dir)
		{
			int nx = curX + dx[dir];
			int ny = curY + dy[dir];
			int nz = curZ + dz[dir];

			if (nx < 0 || nx >= n || ny < 0 || ny >= m || nz < 0 || nz >= h)
				continue;
			if (dist[nx][ny][nz] >= 0)	//익지 않은 토마토가 -1이므로 board 상관없이 그것만 검사하면 됨
				continue;

			dist[nx][ny][nz] = dist[curX][curY][curZ] + 1;
			Q.push({ nx, ny, nz });
		}
	}

	//결과 출력부
	int result = 0;
	for (int k = 0; k < h; ++k)
	{
		for (int i = 0; i < n; ++i)
		{
			for (int j = 0; j < m; ++j)
			{
				if (dist[i][j][k] == -1)	//익지않은 토마토가 있으면 -1
				{
					cout << -1 << '\n';
					return 0;
				}
				result = max<int>(dist[i][j][k], result);
			}
		}
	}
	cout << result << '\n';
}
{% endhighlight %}
