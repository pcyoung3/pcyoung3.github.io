---
layout: single
title: 백준 13460번 - 구슬 탈출 2
categories: algorithmProblems
tags: [algorithm, simulation]
toc: true
toc_sticky: true
---

## 문제
[백준 13460번 문제](https://www.acmicpc.net/problem/13460) <br>

스타트링크에서 판매하는 어린이용 장난감 중에서 가장 인기가 많은 제품은 구슬 탈출이다. 구슬 탈출은 직사각형 보드에 빨간 구슬과 파란 구슬을 하나씩 넣은 다음, 빨간 구슬을 구멍을 통해 빼내는 게임이다.

보드의 세로 크기는 N, 가로 크기는 M이고, 편의상 1×1크기의 칸으로 나누어져 있다. 가장 바깥 행과 열은 모두 막혀져 있고, 보드에는 구멍이 하나 있다. 빨간 구슬과 파란 구슬의 크기는 보드에서 1×1크기의 칸을 가득 채우는 사이즈이고, 각각 하나씩 들어가 있다. 게임의 목표는 빨간 구슬을 구멍을 통해서 빼내는 것이다. 이때, 파란 구슬이 구멍에 들어가면 안 된다.

이때, 구슬을 손으로 건드릴 수는 없고, 중력을 이용해서 이리 저리 굴려야 한다. 왼쪽으로 기울이기, 오른쪽으로 기울이기, 위쪽으로 기울이기, 아래쪽으로 기울이기와 같은 네 가지 동작이 가능하다.

각각의 동작에서 공은 동시에 움직인다. 빨간 구슬이 구멍에 빠지면 성공이지만, 파란 구슬이 구멍에 빠지면 실패이다. 빨간 구슬과 파란 구슬이 동시에 구멍에 빠져도 실패이다. 빨간 구슬과 파란 구슬은 동시에 같은 칸에 있을 수 없다. 또, 빨간 구슬과 파란 구슬의 크기는 한 칸을 모두 차지한다. 기울이는 동작을 그만하는 것은 더 이상 구슬이 움직이지 않을 때 까지이다.

보드의 상태가 주어졌을 때, 최소 몇 번 만에 빨간 구슬을 구멍을 통해 빼낼 수 있는지 구하는 프로그램을 작성하시오.

## 입력

첫 번째 줄에는 보드의 세로, 가로 크기를 의미하는 두 정수 N, M (3 ≤ N, M ≤ 10)이 주어진다. 다음 N개의 줄에 보드의 모양을 나타내는 길이 M의 문자열이 주어진다. 이 문자열은 '`.`', '`#`', '`O`', '`R`', '`B`' 로 이루어져 있다. '`.`'은 빈 칸을 의미하고, '`#`'은 공이 이동할 수 없는 장애물 또는 벽을 의미하며, '`O`'는 구멍의 위치를 의미한다. '`R`'은 빨간 구슬의 위치, '`B`'는 파란 구슬의 위치이다.

입력되는 모든 보드의 가장자리에는 모두 '`#`'이 있다. 구멍의 개수는 한 개 이며, 빨간 구슬과 파란 구슬은 항상 1개가 주어진다.

## 출력

최소 몇 번 만에 빨간 구슬을 구멍을 통해 빼낼 수 있는지 출력한다. 만약, 10번 이하로 움직여서 빨간 구슬을 구멍을 통해 빼낼 수 없으면 -1을 출력한다.

## 예제 입력 1 복사

```
5 5
#####
#..B#
#.#.#
#RO.#
#####
```

## 예제 출력 1 복사

1

## 예제 입력 2 복사

```
7 7
#######
#...RB#
#.#####
#.....#
#####.#
#O....#
#######
```

## 예제 출력 2 복사

5

## 풀이

<div class="notice--info" markdown="1">
**<u>백트레킹을 활용해서 문제 해결</u>**
</div>

<div class="notice--warning" markdown="1">
**<u>두 구슬이 동시에 움직이는 점에 주의</u>** <br>
구슬을 2개 동시에 전부 옮기려고 하면 코드가 복잡해지고 디버깅도 힘들어짐 <br>
따라서 구슬을 1개씩 옮기고 옮긴 뒤의 처리를 나중에 하는 편이 더 쉽게 해결가능
</div>

<div class="notice--warning" markdown="1">
**<u>구슬이 겹친 경우의 처리</u>** <br>
구슬이 겹친다면 두 구슬의 이동거리를 비교해서 좌표를 수정하는 식으로 처리할 수 있다. <br>
![KakaoTalk_20230823_115239584.jpg](/assets/images/algorithm/KakaoTalk_20230823_115239584.jpg)
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

#define X first
#define Y second

char board[12][12];
int n, m;
pair<int, int> red, blue;

int ans;

int dx[] = { 1, 0, -1, 0 };
int dy[] = { 0, 1, 0, -1 };

void TiltBoard(pair<int, int> r, pair<int, int> b, int dir, int cnt);
int MoveDist(pair<int, int> before, pair<int, int> after);

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n >> m;
	for (int i = 0; i < n; i++)
	{
		cin >> board[i];
		for (int j = 0; j < m; j++)
		{
			if (board[i][j] == 'R' || board[i][j] == 'r')
				red = { i, j };
			else if (board[i][j] == 'B' || board[i][j] == 'b')
				blue = { i, j };
		}
	}

	ans = 4000;
	for (int dir = 0; dir < 4; dir++)	//최초위치에서 4방향 검사
		TiltBoard(red, blue, dir, 1);

	if (ans >= 11)
		cout << -1;
	else
		cout << ans;

}

void TiltBoard(pair<int, int> r, pair<int, int> b, int dir, int cnt)
{
	if (cnt >= ans)
		return;
	if (cnt > 10)
		return;

	bool result = false;

	pair<int, int> moveR;
	pair<int, int> moveB;
	bool isGoalRed = false;
	bool isGoalBlue = false;

	//빨간구슬 움직임
	moveR.X = r.X + dx[dir];
	moveR.Y = r.Y + dy[dir];
	while (true)
	{
		if(board[moveR.X][moveR.Y] == '#')
			break;
		if (board[moveR.X][moveR.Y] == 'O')
		{
			isGoalRed = true;
			break;
		}
		moveR.X += dx[dir];
		moveR.Y += dy[dir];
	}
	moveR.X -= dx[dir];	//마지막 이동은 취소
	moveR.Y -= dy[dir];


	//파란구슬 움직임
	moveB.X = b.X + dx[dir];
	moveB.Y = b.Y + dy[dir];
	while (true)
	{
		if (board[moveB.X][moveB.Y] == '#')
			break;
		if (board[moveB.X][moveB.Y] == 'O')
		{
			isGoalBlue = true;
			break;
		}
		moveB.X += dx[dir];
		moveB.Y += dy[dir];
	}
	moveB.X -= dx[dir];	//마지막 이동은 취소
	moveB.Y -= dy[dir];

	if (isGoalBlue)	//파란구슬이 나온경우
		return;
	else if (isGoalRed)	//빨간구슬만 나온경우
	{
		ans = min(ans, cnt);
		return;
	}

	if (moveR == moveB)	//두 구슬의 이동이 겹친 경우
	{
		//두 구슬의 이동거리를 계산해서 더 많이 이동한 쪽의 좌표를 조정
		//막히는 벽까지 이동하므로 적게 이동한 쪽이 먼저 벽에 도착함
		int distR = MoveDist(r, moveR);
		int distB = MoveDist(b, moveB);
		if (distR > distB)
		{
			moveR.X -= dx[dir];
			moveR.Y -= dy[dir];
		}
		else
		{
			moveB.X -= dx[dir];
			moveB.Y -= dy[dir];
		}
	}

	for (int i = 0; i < 4; i++)	//백트레킹으로 4방향 검사
	{
		if(dir == i)
			continue;

		TiltBoard(moveR, moveB, i, cnt + 1);
	}
}

int MoveDist(pair<int, int> before, pair<int, int> after)
{
	return abs(before.X - after.X) + abs(before.Y - after.Y);
}
{% endhighlight %}

## 다른 풀이

<div class="notice--info" markdown="1">
**<u>BFS로 풀어내는 방법</u>**
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

#define X first
#define Y second

int n, m;
pair<int, int> red, blue; // 빨간 구슬과 파란 구슬의 위치
string board[11];

// dist[a][b][c][d] : 빨간 구슬이 (a, b)이고 파란 구슬이 (c, d)에 위치한 상황에 도달하기 위한 동작의 횟수
int dist[11][11][11][11];
int dx[4] = { 0, 1, 0, -1 };
int dy[4] = { 1, 0, -1, 0 };

// red_x, red_y : 빨간 구슬 좌표, blue_x, blue_y : 파란 구슬 좌표
int bfs() 
{
	queue<tuple<int, int, int, int>> q;
	q.push({ red.X, red.Y, blue.X, blue.Y });
	dist[red.X][red.Y][blue.X][blue.Y] = 0;

	while (!q.empty()) 
	{
		int rx, ry, bx, by;
		tie(rx, ry, bx, by) = q.front();
		q.pop();
		int cnt = dist[rx][ry][bx][by];

		// 10번 넘게 탈출 못하면 -1
		if (cnt >= 10)
			return -1;

		// 4방향으로 기울이기
		for (int i = 0; i < 4; i++)
		{
			int n_rx = rx, n_ry = ry, n_bx = bx, n_by = by;

			// Blue 이동, 가장자리에 모두 '#'이 있다는 조건으로 인해 OOB를 체크하지 않아도 됨. #나 O의 앞에서 정지함.
			while (board[n_bx + dx[i]][n_by + dy[i]] == '.') 
			{
				n_bx += dx[i];
				n_by += dy[i];
			}

			// Blue가 탈출했다면 실패이므로 continue
			if (board[n_bx + dx[i]][n_by + dy[i]] == 'O') 
				continue;

			// Red 이동
			while (board[n_rx + dx[i]][n_ry + dy[i]] == '.') 
			{
				n_rx += dx[i];
				n_ry += dy[i];
			}

			// Red가 탈출했다면 종료, 바로 정답을 반환
			if (board[n_rx + dx[i]][n_ry + dy[i]] == 'O') 
				return cnt + 1;

			// Red, Blue가 겹쳐진 경우 늦게 출발한 구슬을 한칸 뒤로 이동
			if ((n_rx == n_bx) && (n_ry == n_by)) 
			{
				if (i == 0) // 아래쪽 (0, 1)로 이동
					ry < by ? n_ry-- : n_by--;
				else if (i == 1) // 오른쪽 (1, 0)로 이동
					rx < bx ? n_rx-- : n_bx--;
				else if (i == 2) // 위쪽 (0, -1)로 이동
					ry > by ? n_ry++ : n_by++;
				else // 왼쪽 (-1, 0)로 이동
					rx > bx ? n_rx++ : n_bx++;
				
				//이런 방법도 가능 (위에서 사용한 거리를 이용)
				//int distR = abs(n_rx - rx) + abs(n_ry - ry);
				//int distB = abs(n_bx - bx) + abs(n_by - by);
				//if (distR > distB)
				//	n_rx -= dx[dir], n_ry -= dy[dir];
				//else
				//	n_bx -= dx[dir], n_by -= dy[dir];
			}

			// 이미 (n_rx, n_ry, n_bx, n_by)를 방문한 적이 있다면 continue
			if (dist[n_rx][n_ry][n_bx][n_by] != -1) 
				continue;
			dist[n_rx][n_ry][n_bx][n_by] = cnt + 1;
			q.push({ n_rx, n_ry, n_bx, n_by });
		}
	}
	return -1;
}

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	// 거리 배열 초기화
	for (int i = 0; i < 10; i++)
		for (int j = 0; j < 10; j++)
			for (int k = 0; k < 10; k++)
				fill(dist[i][j][k], dist[i][j][k] + 10, -1);

	cin >> n >> m;
	for (int i = 0; i < n; i++) 
	{
		cin >> board[i];
		for (int j = 0; j < m; j++) 
		{
			if (board[i][j] == 'B') 
			{
				blue = { i, j };
				board[i][j] = '.';
			}
			else if (board[i][j] == 'R') 
			{
				red = { i, j };
				board[i][j] = '.';
			}
		}
	}

	cout << bfs();
}
{% endhighlight %}