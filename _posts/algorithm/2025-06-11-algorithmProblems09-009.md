---
layout: single
title: 백준 7576번 - 토마토
categories: algorithmProblems
tags: [algorithm, BFS]
toc: true
toc_sticky: true
---

## 문제
[백준 7576번 문제](https://www.acmicpc.net/problem/7576)

철수의 토마토 농장에서는 토마토를 보관하는 큰 창고를 가지고 있다. 토마토는 아래의 그림과 같이 격자 모양 상자의 칸에 하나씩 넣어서 창고에 보관한다. 

![7576_p_01.png](/assets/images/algorithm/7576_p_01.png)

창고에 보관되는 토마토들 중에는 잘 익은 것도 있지만, 아직 익지 않은 토마토들도 있을 수 있다. 보관 후 하루가 지나면, 익은 토마토들의 인접한 곳에 있는 익지 않은 토마토들은 익은 토마토의 영향을 받아 익게 된다. 하나의 토마토의 인접한 곳은 왼쪽, 오른쪽, 앞, 뒤 네 방향에 있는 토마토를 의미한다. 대각선 방향에 있는 토마토들에게는 영향을 주지 못하며, 토마토가 혼자 저절로 익는 경우는 없다고 가정한다. 철수는 창고에 보관된 토마토들이 며칠이 지나면 다 익게 되는지, 그 최소 일수를 알고 싶어 한다.

토마토를 창고에 보관하는 격자모양의 상자들의 크기와 익은 토마토들과 익지 않은 토마토들의 정보가 주어졌을 때, 며칠이 지나면 토마토들이 모두 익는지, 그 최소 일수를 구하는 프로그램을 작성하시오. 단, 상자의 일부 칸에는 토마토가 들어있지 않을 수도 있다.

## 입력

첫 줄에는 상자의 크기를 나타내는 두 정수 M,N이 주어진다. M은 상자의 가로 칸의 수, N은 상자의 세로 칸의 수를 나타낸다. 단, 2 ≤ M,N ≤ 1,000 이다. 둘째 줄부터는 하나의 상자에 저장된 토마토들의 정보가 주어진다. 즉, 둘째 줄부터 N개의 줄에는 상자에 담긴 토마토의 정보가 주어진다. 하나의 줄에는 상자 가로줄에 들어있는 토마토의 상태가 M개의 정수로 주어진다. 정수 1은 익은 토마토, 정수 0은 익지 않은 토마토, 정수 -1은 토마토가 들어있지 않은 칸을 나타낸다.

토마토가 하나 이상 있는 경우만 입력으로 주어진다.

## 출력

여러분은 토마토가 모두 익을 때까지의 최소 날짜를 출력해야 한다. 만약, 저장될 때부터 모든 토마토가 익어있는 상태이면 0을 출력해야 하고, 토마토가 모두 익지는 못하는 상황이면 -1을 출력해야 한다.

## 예제 입력 1 복사

```
6 4
0 0 0 0 0 0
0 0 0 0 0 0
0 0 0 0 0 0
0 0 0 0 0 1
```

## 예제 출력 1 복사

```
8
```

## 정답코드

<div class="notice--warning" markdown="1">
**<u>시작점이 여러개일 수 있는 BFS</u>** 

Dist를 잘 처리한다면 시작점이 여러개 있을 경우 BFS를 시작하기 전에 미리 Queue에 넣어줘 처리하면 된다.
</div>

### 내가 푼 코드
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

#define X first
#define Y second

int board[1005][1005];
int dist[1005][1005];
int dx[4] = { 1, 0 ,-1, 0 };
int dy[4] = { 0, 1, 0, -1 };
int main(void)
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    int n, m;
    cin >> m >> n;

    
    for (int i = 0; i < n; i++)
        fill(dist[i], dist[i] + m, -1);     //배열 -1로 초기화

    queue<pair<int, int>> Q;
    for (int i = 0; i < n; ++i)
    {
        for (int j = 0; j < m; ++j)
        {
            int input;
            cin >> input;
            board[i][j] = input;

            if (input == 1)
            {
                Q.push({ i, j });   //미리 시작점을 큐에 담아놓는다
                dist[i][j] = 0;     //시작점에서는 dist를 0으로 셋팅
                /*dist 배열 : 초기화 -1, 시작 0, 방문하지 않은 곳 -1*/
            }
        }
    }

    int mx = 0;
    while (!Q.empty())
    {
        pair<int, int> cur = Q.front();
        Q.pop();
        for (int dir = 0; dir < 4; ++dir)
        {
            int nx = cur.X + dx[dir];
            int ny = cur.Y + dy[dir];

            if (nx < 0 || nx >= n || ny < 0 || ny >= m)
                continue;
            if (dist[nx][ny] != -1 || board[nx][ny] == -1)
                continue;
            dist[nx][ny] = dist[cur.X][cur.Y] + 1;
            Q.push({ nx, ny });
            mx = max(mx, dist[nx][ny]);
        }
    }

    for (int i = 0; i < n; i++) //거리중에서 가장 큰 값을 찾아냄
    {
        for (int j = 0; j < m; j++)
        {
            if (dist[i][j] == -1 && board[i][j] != -1)  //순회하지 못한 토마토가 있을 경우
            {
                cout << -1;
                return 0;
            }
        }
    }
    cout << mx;
}
{% endhighlight %}

### 참고용코드(dist 배열 초기화)
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

#define X first
#define Y second

int board[1002][1002];
int dist[1002][1002];
int n, m;
int dx[4] = { 1,0,-1,0 };
int dy[4] = { 0,1,0,-1 };

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	cin >> m >> n;

	queue<pair<int, int> > Q;
	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < m; j++) 
		{
			cin >> board[i][j];

			if (board[i][j] == 1)
				Q.push({ i,j });
			if (board[i][j] == 0)/*dist 배열 : 초기화 0, 시작 0, 방문하지 않은 곳 -1*/
				dist[i][j] = -1;
		}
	}

	while (!Q.empty()) 
	{
		auto cur = Q.front(); 
		Q.pop();
		for (int dir = 0; dir < 4; dir++) 
		{
			int nx = cur.X + dx[dir];
			int ny = cur.Y + dy[dir];
			if (nx < 0 || nx >= n || ny < 0 || ny >= m) 
				continue;
			if (dist[nx][ny] >= 0) 
				continue;
			dist[nx][ny] = dist[cur.X][cur.Y] + 1;
			Q.push({ nx,ny });
		}
	}

	int ans = 0;
	for (int i = 0; i < n; i++) 
	{
		for (int j = 0; j < m; j++) 
		{
			if (dist[i][j] == -1) 
			{
				cout << -1;
				return 0;
			}
			ans = max(ans, dist[i][j]);
		}
	}

	cout << ans;
}
{% endhighlight %}
