---
layout: single
title: 백준 2206번 - 벽 부수고 이동하기
categories: algorithmProblems
tags: [algorithm, BFS]
toc: true
toc_sticky: true
---

## 문제
[백준 2206번 문제](https://www.acmicpc.net/problem/2206)

N×M의 행렬로 표현되는 맵이 있다. 맵에서 0은 이동할 수 있는 곳을 나타내고, 1은 이동할 수 없는 벽이 있는 곳을 나타낸다. 당신은 (1, 1)에서 (N, M)의 위치까지 이동하려 하는데, 이때 최단 경로로 이동하려 한다. 최단경로는 맵에서 가장 적은 개수의 칸을 지나는 경로를 말하는데, 이때 시작하는 칸과 끝나는 칸도 포함해서 센다.

만약에 이동하는 도중에 한 개의 벽을 부수고 이동하는 것이 좀 더 경로가 짧아진다면, 벽을 한 개 까지 부수고 이동하여도 된다.

한 칸에서 이동할 수 있는 칸은 상하좌우로 인접한 칸이다.

맵이 주어졌을 때, 최단 경로를 구해 내는 프로그램을 작성하시오.

## 입력

첫째 줄에 N(1 ≤ N ≤ 1,000), M(1 ≤ M ≤ 1,000)이 주어진다. 다음 N개의 줄에 M개의 숫자로 맵이 주어진다. (1, 1)과 (N, M)은 항상 0이라고 가정하자.

## 출력

첫째 줄에 최단 거리를 출력한다. 불가능할 때는 -1을 출력한다.

## 예제 입력 1 복사

```
6 4
0100
1110
1000
0000
0111
0000
```

## 예제 출력 1 복사

15

## 예제 입력 2 복사

```
4 4
0111
1111
1111
1110
```

## 예제 출력 2 복사

-1
   
---
## 풀이
### 반례
```
5 8
01000000
01010000
01010000
01010011
00010010

6 7
0000000
0111111
0100010
0101010
0101010
0001010
```
   
### 정답코드
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;
#define X first
#define Y second

int dx[4] = { 0,1,0,-1 };
int dy[4] = { 1,0,-1,0 };

char board[1000][1000];
int dist[1000][1000][2];
// dist[x][y][0] : 벽을 하나도 부수지 않고 (x,y)까지 오는데 걸리는 비용
// dist[x][y][1] : 벽을 하나만 부수고 (x,y)까지 오는데 걸리는 비용, (x,y)가 벽이라서 부수는 경우 포함
int n, m;

int main(void) {
    ios::sync_with_stdio(0);
    cin.tie(0);
    cin >> n >> m;
    for (int i = 0; i < n; ++i)
        for (int j = 0; j < m; ++j)
            cin >> board[i][j];

    for (int i = 0; i < n; ++i)
        for (int j = 0; j < m; ++j)
            dist[i][j][0] = dist[i][j][1] = -1;

    dist[0][0][0] = dist[0][0][1] = 1;
    queue<tuple<int, int, int>> q;
    q.push({ 0,0,0 });
    while (!q.empty())
    {
        int x, y, broken;
        tie(x, y, broken) = q.front();
        if (x == n - 1 && y == m - 1)
        {
            cout << dist[x][y][broken];
            return 0;
        }
        q.pop();
        int nextdist = dist[x][y][broken] + 1;
        for (int dir = 0; dir < 4; ++dir)
        {
            int nx = x + dx[dir];
            int ny = y + dy[dir];
            if (nx < 0 || nx >= n || ny < 0 || ny >= m)
                continue;
            //부수지 않은 경우 또는 이미 부순 경우
            if (board[nx][ny] == '0' && dist[nx][ny][broken] == -1)
            {
                dist[nx][ny][broken] = nextdist;
                q.push({ nx, ny, broken });
            }
            // (nx, ny)를 부수는 경우
            if (!broken && board[nx][ny] == '1' && dist[nx][ny][1] == -1)
            {
                dist[nx][ny][1] = nextdist;
                q.push({ nx, ny, 1 });
            }
        }
    }
    cout << -1;
    return 0;
}
{% endhighlight %}
   
### 내가 푼 틀린답안
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

#define X first
#define Y second

char board[1005][1005];
int dist[1005][1005];
int dist2[1005][1005];

int dx[4] = { 1, 0, -1, 0 };
int dy[4] = { 0, 1, 0, -1 };
int main(void)
{
	ios_base::sync_with_stdio(0);
	cin.tie(0);

	int n, m;
	cin >> n >> m;

	for (int i = 0; i < n; ++i)
	{
		for (int j = 0; j < m; ++j)
		{
			cin >> board[i][j];
		}
	}

	for (int i = 0; i < n; ++i)
	{
		fill(dist[i], dist[i] + m, -1);
		fill(dist2[i], dist2[i] + m, -1);
	}
		

	queue<pair<int, int>> Q;
	Q.push({ 0, 0 });
	dist[0][0] = 0;

	queue<pair<int, int>> breakQ;
	while (!Q.empty())
	{
		auto cur = Q.front();
		Q.pop();
		for (int dir = 0; dir < 4; ++dir)
		{
			int nx = cur.X + dx[dir];
			int ny = cur.Y + dy[dir];
			if (nx < 0 || nx >= n || ny < 0 || ny >= m)
				continue;
			if (board[nx][ny] == '1')
			{
				int breakX = cur.X + dx[dir] * 2;
				int breakY = cur.Y + dy[dir] * 2;
				if (breakX < 0 || breakX >= n || breakY < 0 || breakY >= m)
					continue;
				if (board[breakX][breakY] == '1')
					continue;
				breakQ.push({ breakX, breakY });
				if(dist2[breakX][breakY] == -1)
					dist2[breakX][breakY] = dist[cur.X][cur.Y] + 2;
				else
					dist2[breakX][breakY] = min(dist2[breakX][breakY],dist[cur.X][cur.Y] + 2);
				continue;
			}
			if (dist[nx][ny] != -1)
				continue;
			dist[nx][ny] = dist[cur.X][cur.Y] + 1;
			Q.push({ nx, ny });
		}
	}

	while (!breakQ.empty())
	{
		auto cur = breakQ.front();
		breakQ.pop();
		for (int dir = 0; dir < 4; ++dir)
		{
			int nx = cur.X + dx[dir];
			int ny = cur.Y + dy[dir];
			if (nx < 0 || nx >= n || ny < 0 || ny >= m)
				continue;
			if (dist2[nx][ny] != -1 && dist2[nx][ny] < dist2[cur.X][cur.Y]+1)
				continue;
			if (board[nx][ny] == '1')
				continue;
			breakQ.push({ nx, ny });
			dist2[nx][ny] = dist2[cur.X][cur.Y] + 1;
		}
	}

	int result = 0;
	if (dist2[n - 1][m - 1] == -1)
		cout << "-1\n";
	else
		cout << dist2[n - 1][m - 1] + 1 << '\n';
}
{% endhighlight %}
