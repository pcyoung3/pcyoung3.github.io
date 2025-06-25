---
layout: single
title: 백준 14503번 - 로봇 청소기
categories: algorithmProblems
tags: [algorithm, simulation]
toc: true
toc_sticky: true
use_math: true
---

## 문제
[백준 14503번 문제](https://www.acmicpc.net/problem/14503) <br>
로봇 청소기와 방의 상태가 주어졌을 때, 청소하는 영역의 개수를 구하는 프로그램을 작성하시오.

로봇 청소기가 있는 방은 $N \times M$ 크기의 직사각형으로 나타낼 수 있으며, $1 \times 1$ 크기의 정사각형 칸으로 나누어져 있다. 각각의 칸은 벽 또는 빈 칸이다. 청소기는 바라보는 방향이 있으며, 이 방향은 동, 서, 남, 북 중 하나이다. 방의 각 칸은 좌표 $(r, c)$로 나타낼 수 있고, 가장 북쪽 줄의 가장 서쪽 칸의 좌표가 $(0, 0)$, 가장 남쪽 줄의 가장 동쪽 칸의 좌표가 $(N-1, M-1)$이다. 즉, 좌표 $(r, c)$는 북쪽에서 $(r+1)$번째에 있는 줄의 서쪽에서 $(c+1)$번째 칸을 가리킨다. 처음에 빈 칸은 전부 청소되지 않은 상태이다.

로봇 청소기는 다음과 같이 작동한다.

1. 현재 칸이 아직 청소되지 않은 경우, 현재 칸을 청소한다.
2. 현재 칸의 주변 $4$칸 중 청소되지 않은 빈 칸이 없는 경우,
	1. 바라보는 방향을 유지한 채로 한 칸 후진할 수 있다면 한 칸 후진하고 1번으로 돌아간다.
	2. 바라보는 방향의 뒤쪽 칸이 벽이라 후진할 수 없다면 작동을 멈춘다.
3. 현재 칸의 주변 $4$칸 중 청소되지 않은 빈 칸이 있는 경우,
	1. 반시계 방향으로 $90^\circ$ 회전한다.
	2. 바라보는 방향을 기준으로 앞쪽 칸이 청소되지 않은 빈 칸인 경우 한 칸 전진한다.
	3. 1번으로 돌아간다.

## 입력
첫째 줄에 방의 크기 $N$과 $M$이 입력된다. $(3 \le N, M \le 50)$  둘째 줄에 처음에 로봇 청소기가 있는 칸의 좌표 $(r, c)$와 처음에 로봇 청소기가 바라보는 방향 $d$가 입력된다. $d$가 $0$인 경우 북쪽, $1$인 경우 동쪽, $2$인 경우 남쪽, $3$인 경우 서쪽을 바라보고 있는 것이다.

셋째 줄부터 $N$개의 줄에 각 장소의 상태를 나타내는 $N \times M$개의 값이 한 줄에 $M$개씩 입력된다. $i$번째 줄의 $j$번째 값은 칸 $(i, j)$의 상태를 나타내며, 이 값이 $0$인 경우 $(i, j)$가 청소되지 않은 빈 칸이고, $1$인 경우 $(i, j)$에 벽이 있는 것이다. 방의 가장 북쪽, 가장 남쪽, 가장 서쪽, 가장 동쪽 줄 중 하나 이상에 위치한 모든 칸에는 벽이 있다. 로봇 청소기가 있는 칸은 항상 빈 칸이다.

## 출력

로봇 청소기가 작동을 시작한 후 작동을 멈출 때까지 청소하는 칸의 개수를 출력한다.

## 예제 입력 1 복사

```
3 3
1 1 0
1 1 1
1 0 1
1 1 1
```
## 예제 출력 1 복사

1

## 예제 입력 2 복사

```
11 10
7 4 0
1 1 1 1 1 1 1 1 1 1
1 0 0 0 0 0 0 0 0 1
1 0 0 0 1 1 1 1 0 1
1 0 0 1 1 0 0 0 0 1
1 0 1 1 0 0 0 0 0 1
1 0 0 0 0 0 0 0 0 1
1 0 0 0 0 0 0 1 0 1
1 0 0 0 0 0 1 1 0 1
1 0 0 0 0 0 1 1 0 1
1 0 0 0 0 0 0 0 0 1
1 1 1 1 1 1 1 1 1 1
```

## 예제 출력 2 복사

57

## 풀이

<div class="notice--info" markdown="1">
**<u>row와 col이 수학 상 x, y가 아니라 y, x라는 점에 유의하면서 좌표계산</u>**
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

#define X first
#define Y second

struct Robot
{
	int r;
	int c;
	int dir;
};

int board[55][55];
int n, m;
Robot robot;
int ans;

int dx[] = { -1, 0, 1, 0 };	//북동남서 방향
int dy[] = { 0, 1, 0, -1 };

void CleanRoom();
bool IsNotCleanSpace();	//주변에 청소 안한 구간이 있는가?

//Debug
void Printboard();

int main() 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n >> m;
	cin >> robot.r >> robot.c >> robot.dir;
	for (int i = 0; i < n; i++)
		for (int j = 0; j < m; j++)
			cin >> board[i][j];

	CleanRoom();
	cout << ans;
}

void CleanRoom()
{
	while (true)
	{
		//Printboard();
		//현재 자리가 0이면 현재 자리 청소
		if (board[robot.r][robot.c] == 0)
		{
			board[robot.r][robot.c] = 2;
			ans++;
			continue;
		}

		if (IsNotCleanSpace())	//주변에 청소안한 구간이 있는가?
		{	//청소안한 구간이 있을 경우
			robot.dir--;	//회전
			if (robot.dir < 0)
				robot.dir = 3;

			pair<int, int> frontMove;	//로봇이 바라보는 방향으로 한칸 앞쪽
			frontMove.X = robot.r + dx[robot.dir];
			frontMove.Y = robot.c + dy[robot.dir];
			if (board[frontMove.X][frontMove.Y] == 0)
			{
				robot.r = frontMove.X;
				robot.c = frontMove.Y;
				continue;
			}
			else
				continue;
		}
		else
		{	//청소안한 구간이 없을 경우
			pair<int, int> backMove;
			backMove.X = robot.r - dx[robot.dir];
			backMove.Y = robot.c - dy[robot.dir];
			if (board[backMove.X][backMove.Y] != 1)	//후진할 공간이 있는가?
			{
				robot.r = backMove.X;
				robot.c = backMove.Y;
				continue;
			}
			else
				break;	//후진할 공간이 없으면 종료
		}
	}
	
}

bool IsNotCleanSpace()
{
	for (int dir = 0; dir < 4; dir++)
	{
		int nx = robot.r + dx[dir];
		int ny = robot.c + dy[dir];

		if (board[nx][ny] == 0)
			return true;
	}
	return false;
}

void Printboard()
{
	cout << "=======================\n";
	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < m; j++)
		{
			if (i == robot.r && j == robot.c)
			{
				switch (robot.dir)
				{
				case 0:
					cout << "↑ ";
					break;
				case 1:
					cout << "→ ";
					break;
				case 2:
					cout << "↓ ";
					break;
				case 3:
					cout << "← ";
					break;
				default:
					cout << "@ ";
					break;
				}
			}
			else
				cout << board[i][j] << " ";
		}
			
		cout << "\n";
	}
	cout << "\n";
}
{% endhighlight %}