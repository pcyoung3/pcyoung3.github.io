---
layout: single
title: 백준 7562번 - 나이트의 이동
categories: algorithmProblems
tags: [algorithm, BFS]
toc: true
toc_sticky: true
---

## 문제
[백준 7562번 문제](https://www.acmicpc.net/problem/7562)

체스판 위에 한 나이트가 놓여져 있다. 나이트가 한 번에 이동할 수 있는 칸은 아래 그림에 나와있다. 나이트가 이동하려고 하는 칸이 주어진다. 나이트는 몇 번 움직이면 이 칸으로 이동할 수 있을까?

![knight.png](/assets/images/algorithm/knight.png)


## 입력

입력의 첫째 줄에는 테스트 케이스의 개수가 주어진다.

각 테스트 케이스는 세 줄로 이루어져 있다. 첫째 줄에는 체스판의 한 변의 길이 l(4 ≤ l ≤ 300)이 주어진다. 체스판의 크기는 l × l이다. 체스판의 각 칸은 두 수의 쌍 {0, ..., l-1} × {0, ..., l-1}로 나타낼 수 있다. 둘째 줄과 셋째 줄에는 나이트가 현재 있는 칸, 나이트가 이동하려고 하는 칸이 주어진다.

## 출력

각 테스트 케이스마다 나이트가 최소 몇 번만에 이동할 수 있는지 출력한다.

## 예제 입력 1 복사

```
3
8
0 0
7 0
100
0 0
30 50
10
1 1
1 1
```

## 예제 출력 1 복사

5
28
0
   
---
## 풀이
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

#define X first
#define Y second

//체스판은 어디든 갈 수 있으므로 board가 불필요
int dist[305][305];

//이동할 수 있는 가지수가 8개이므로 배열 8개로 돌리면 됨
int dx[8] = { 1, 1, 2, 2, -1, -1, -2, -2 };
int dy[8] = { 2, -2, 1, -1, 2, -2, 1, -1 };
int main(void)
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int t;
	cin >> t;

	while (t--)
	{
		int l;
		cin >> l;

		pair<int, int> start, target;
		cin >> start.X >> start.Y;
		cin >> target.X >> target.Y;

		for (int i = 0; i < 305; ++i)
			fill(dist[i], dist[i] + 305, -1);

		queue<pair<int, int>> Q;
		Q.push({ start.X, start.Y });
		dist[start.X][start.Y] = 0;

		while (!Q.empty())
		{
			auto cur = Q.front();
			Q.pop();
			for (int dir = 0; dir < 8; ++dir)
			{
				int nx = cur.X + dx[dir];
				int ny = cur.Y + dy[dir];
				if (nx < 0 || nx >= l || ny < 0 || ny >= l)
					continue;
				if (dist[nx][ny] != -1)
					continue;
				dist[nx][ny] = dist[cur.X][cur.Y] + 1;
				Q.push({ nx, ny });
			}
		}
		cout << dist[target.X][target.Y] << '\n';
	}
}
{% endhighlight %}
