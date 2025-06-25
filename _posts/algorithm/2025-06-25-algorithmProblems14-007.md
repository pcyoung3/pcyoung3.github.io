---
layout: single
title: 백준 14502번 - 연구소
categories: algorithmProblems
tags: [algorithm, simulation]
toc: true
toc_sticky: true
---

## 문제
[백준 14502번 문제](https://www.acmicpc.net/problem/14502) <br>

인체에 치명적인 바이러스를 연구하던 연구소에서 바이러스가 유출되었다. 다행히 바이러스는 아직 퍼지지 않았고, 바이러스의 확산을 막기 위해서 연구소에 벽을 세우려고 한다.

연구소는 크기가 N×M인 직사각형으로 나타낼 수 있으며, 직사각형은 1×1 크기의 정사각형으로 나누어져 있다. 연구소는 빈 칸, 벽으로 이루어져 있으며, 벽은 칸 하나를 가득 차지한다. 

일부 칸은 바이러스가 존재하며, 이 바이러스는 상하좌우로 인접한 빈 칸으로 모두 퍼져나갈 수 있다. 새로 세울 수 있는 벽의 개수는 3개이며, 꼭 3개를 세워야 한다.

예를 들어, 아래와 같이 연구소가 생긴 경우를 살펴보자.

```
2 0 0 0 1 1 0
0 0 1 0 1 2 0
0 1 1 0 1 0 0
0 1 0 0 0 0 0
0 0 0 0 0 1 1
0 1 0 0 0 0 0
0 1 0 0 0 0 0
```

이때, 0은 빈 칸, 1은 벽, 2는 바이러스가 있는 곳이다. 아무런 벽을 세우지 않는다면, 바이러스는 모든 빈 칸으로 퍼져나갈 수 있다.

2행 1열, 1행 2열, 4행 6열에 벽을 세운다면 지도의 모양은 아래와 같아지게 된다.

```
2 1 0 0 1 1 0
1 0 1 0 1 2 0
0 1 1 0 1 0 0
0 1 0 0 0 1 0
0 0 0 0 0 1 1
0 1 0 0 0 0 0
0 1 0 0 0 0 0
```

바이러스가 퍼진 뒤의 모습은 아래와 같아진다.

```
2 1 0 0 1 1 2
1 0 1 0 1 2 2
0 1 1 0 1 2 2
0 1 0 0 0 1 2
0 0 0 0 0 1 1
0 1 0 0 0 0 0
0 1 0 0 0 0 0
```

벽을 3개 세운 뒤, 바이러스가 퍼질 수 없는 곳을 안전 영역이라고 한다. 위의 지도에서 안전 영역의 크기는 27이다.

연구소의 지도가 주어졌을 때 얻을 수 있는 안전 영역 크기의 최댓값을 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 지도의 세로 크기 N과 가로 크기 M이 주어진다. (3 ≤ N, M ≤ 8)

둘째 줄부터 N개의 줄에 지도의 모양이 주어진다. 0은 빈 칸, 1은 벽, 2는 바이러스가 있는 위치이다. 2의 개수는 2보다 크거나 같고, 10보다 작거나 같은 자연수이다.

빈 칸의 개수는 3개 이상이다.

## 출력

첫째 줄에 얻을 수 있는 안전 영역의 최대 크기를 출력한다.

## 예제 입력 1 복사

```
7 7
2 0 0 0 1 1 0
0 0 1 0 1 2 0
0 1 1 0 1 0 0
0 1 0 0 0 0 0
0 0 0 0 0 1 1
0 1 0 0 0 0 0
0 1 0 0 0 0 0
```

## 예제 출력 1 복사

27

## 풀이

<div class="notice--info" markdown="1">
**<u>순열 + BFS로 해결</u>** <br>
next_permutation 함수를 이용해서 벽 세울 곳을 구하고 해당 회차에 BFS돌려서 해결
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

#define X first
#define Y second

const int maxIndex = 10;
int board[maxIndex][maxIndex];	//가장 처음에 입력되는 초기배열
int temp[maxIndex][maxIndex];	//매 회차 바뀌는 임시배열
int mask[maxIndex * maxIndex];	//순열에 사용되는 배열
int n, m;

int dx[] = { 1, 0, -1, 0 };
int dy[] = { 0, 1, 0, -1 };

int ans;

void CreateWall();
int SpreadVirus();

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n >> m;
	for (int i = 0; i < n; i++)
		for (int j = 0; j < m; j++)
			cin >> board[i][j];

	CreateWall();
	cout << ans;
}

void CreateWall()
{
	fill(mask + 3, mask + n * m, 1);

	//벽의 위치를 모든 index 중에서 3개를 꼽는 순열로 구함
	do 
	{
		bool isPossible = true;
		for (int i = 0; i < n; i++)
		{
			for (int j = 0; j < m; j++)
			{
				temp[i][j] = board[i][j];
				int idx = i * m + j;
				if (mask[idx] == 0)
				{
					if (board[i][j] == 0)
						temp[i][j] = 1;
					else
						isPossible = false;	//기존 board에 이미 벽 또는 바이러스가 있기 때문에 벽을 세우지 못함
				}
			}
		}
		if(isPossible)
			ans = max(ans, SpreadVirus());
	} while (next_permutation(mask, mask + n * m));
}

int SpreadVirus()
{
	//BFS를 돌면서 바이러스가 퍼지는 곳 체크
	queue<pair<int, int>> Q;
	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < m; j++)
		{
			if (temp[i][j] == 2)
				Q.push({ i,j });
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
			
			if(nx < 0 || nx >= n || ny < 0 || ny >= m)
				continue;
			if(temp[nx][ny] != 0)
				continue;
			temp[nx][ny] = 2;
			Q.push({ nx, ny });
		}
	}

	//이번 회차의 바이러스가 전부 퍼졌을 때 안전지대의 수 구하기
	int result = 0;
	for (int i = 0; i < n; i++)
		for (int j = 0; j < m; j++)
			if (temp[i][j] == 0) result++;

	return result;
}
{% endhighlight %}

## 다른 풀이

<div class="notice--info" markdown="1">
**<u>3중 for문을 돌면서 벽을 하나씩 세우면서 해결</u>**
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

#define X first
#define Y second

int board[8][8];
int n, m, ans;
int free_cells = 0;
queue<pair<int, int>> virus;  // 바이러스
vector<pair<int, int>> frees; // 빈칸
int dx[4] = { 0, 1, 0, -1 };
int dy[4] = { 1, 0, -1, 0 };

// 바이러스가 전파된 칸의 수를 반환
int bfs() 
{
	queue<pair<int, int>> q;
	bool vis[8][8] = {};
	for (int i = 0; i < n; i++) 
	{
		for (int j = 0; j < m; j++) 
		{
			if (board[i][j] == 2) 
				q.push({ i, j });
		}
	}
	int ret = 0;
	while (!q.empty()) 
	{
		pair<int, int> cur = q.front();
		q.pop();
		ret++;
		for (int i = 0; i < 4; i++) 
		{
			int nx = cur.X + dx[i];
			int ny = cur.Y + dy[i];
			if (nx < 0 || ny < 0 || nx >= n || ny >= m) 
				continue;
			if (board[nx][ny] != 0 || vis[nx][ny]) 
				continue;
			vis[nx][ny] = 1;
			q.push({ nx, ny });
		}
	}
	return ret;
}

int main() 
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	cin >> n >> m;
	for (int i = 0; i < n; i++) 
	{
		for (int j = 0; j < m; j++) 
		{
			cin >> board[i][j];
			if (board[i][j] == 0)	// 빈 칸 개수와 좌표 저장
			{
				free_cells++;
				frees.push_back({ i, j });
			}
			else if (board[i][j] == 2) // 바이러스 좌표 저장
				virus.push({ i, j });
		}
	}
	for (int i = 0; i < frees.size(); i++) 
	{
		board[frees[i].X][frees[i].Y] = 1;	//첫번째 벽 세우기
		for (int j = i + 1; j < frees.size(); j++)
		{
			board[frees[j].X][frees[j].Y] = 1;	//두번째 벽 세우기
			for (int k = j + 1; k < frees.size(); k++) 
			{
				board[frees[k].X][frees[k].Y] = 1;	//세번째 벽 세우기

				// 바이러스 퍼진 개수 체크
				int tmp = bfs();

				// 안전영역 = 빈칸 - 바이러스 퍼진 칸, 3을 빼는 이유는 벽을 3개 세웠으므로.
				ans = max(free_cells - 3 - tmp + (int)virus.size(), ans);
				board[frees[k].X][frees[k].Y] = 0;	//세번째 벽 초기화
			}
			board[frees[j].X][frees[j].Y] = 0;	//두번째 벽 초기화
		}
		board[frees[i].X][frees[i].Y] = 0;	//첫번째 벽 초기화
	}
	cout << ans;
}
{% endhighlight %}