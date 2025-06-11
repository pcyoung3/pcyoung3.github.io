---
layout: single
title: 백준 1600번 - 말이 되고픈 원숭이
categories: algorithmProblems
tags: [algorithm, BFS]
toc: true
toc_sticky: true
---

## 문제
[백준 1600번 문제](https://www.acmicpc.net/problem/1600)

동물원에서 막 탈출한 원숭이 한 마리가 세상구경을 하고 있다. 그 녀석은 말(Horse)이 되기를 간절히 원했다. 그래서 그는 말의 움직임을 유심히 살펴보고 그대로 따라 하기로 하였다. 말은 말이다. 말은 격자판에서 체스의 나이트와 같은 이동방식을 가진다. 다음 그림에 말의 이동방법이 나타나있다. x표시한 곳으로 말이 갈 수 있다는 뜻이다. 참고로 말은 장애물을 뛰어넘을 수 있다.

|   |   |   |   |   |
|---|---|---|---|---|
||x||x||
|x||||x|
|||말|||
|x||||x|
||x||x||

근데 원숭이는 한 가지 착각하고 있는 것이 있다. 말은 저렇게 움직일 수 있지만 원숭이는 능력이 부족해서 총 K번만 위와 같이 움직일 수 있고, 그 외에는 그냥 인접한 칸으로만 움직일 수 있다. 대각선 방향은 인접한 칸에 포함되지 않는다.

이제 원숭이는 머나먼 여행길을 떠난다. 격자판의 맨 왼쪽 위에서 시작해서 맨 오른쪽 아래까지 가야한다. 인접한 네 방향으로 한 번 움직이는 것, 말의 움직임으로 한 번 움직이는 것, 모두 한 번의 동작으로 친다. 격자판이 주어졌을 때, 원숭이가 최소한의 동작으로 시작지점에서 도착지점까지 갈 수 있는 방법을 알아내는 프로그램을 작성하시오.

## 입력

첫째 줄에 정수 K가 주어진다. 둘째 줄에 격자판의 가로길이 W, 세로길이 H가 주어진다. 그 다음 H줄에 걸쳐 W개의 숫자가 주어지는데, 0은 아무것도 없는 평지, 1은 장애물을 뜻한다. 장애물이 있는 곳으로는 이동할 수 없다. 시작점과 도착점은 항상 평지이다. W와 H는 1이상 200이하의 자연수이고, K는 0이상 30이하의 정수이다.

## 출력

첫째 줄에 원숭이의 동작수의 최솟값을 출력한다. 시작점에서 도착점까지 갈 수 없는 경우엔 -1을 출력한다.

## 예제 입력 1 복사
```
1
4 4
0 0 0 0
1 0 0 0
0 0 1 0
0 1 0 0
```

## 예제 출력 1 복사

```
4
```

## 예제 입력 2 복사
```
2
5 2
0 0 1 1 0
0 0 1 1 0
```

## 예제 출력 2 복사

```
-1
```

## 풀이

<div class="notice--warning" markdown="1">
**<u>도착배열을 3차원으로 설계하는 문제</u>** 

한번 뛰었을 때마다 다른 배열에 저장 : 즉, 뛴 회수가 같으면 같은 배열에서 BFS를 돌게 된다.
이 부분이 이해가 잘 안갈 수도 있는데 시작점이 여러개인 BFS를 생각하면 된다.

예를들어 점프 1번을 한 경우 3번걷고 점프한 BFS와 1번걷고 점프한 BFS는 같은 배열이여야 된다.
어짜피 큐는 거리순으로 채워지기 때문에 더 빨리 도착한 기록만 남게 될 것이고
모든 걷는 경우의 수와 뛰는 경우의 수를 다 큐에 넣기 때문에 
벽으로 막힌 못가는 곳도 점프타이밍에 따라서 갈 수 있는 경우까지 전부 방문기록에 남게 됨
</div>

<div class="notice--warning" markdown="1">
**<u>뛰는 경우를 언제 넣어야 할까</u>** 

어떤 조건에서 뛰는 경우를 넣어야 할지 고민이 될 수 있다.
정답은 가능할 때 전부 넣으면 된다.
뛸 수 있을 때 큐에 일단 집어넣으면 마지막에 뛰어야 도착할 수 있을 때 못뛰지 않을까 라는 생각을 하겠지만
뛸 수 있는 경우를 if문을 이용해 다 집어넣고 걷는 경우를 전부 다 집어넣으면
뛰지않고 걷는 경우의 수도 전부 큐에 다 집어넣어져서 마지막에 뛰는 예외도 처리가 됨
</div>

{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

const int MX = 205;
int board[MX][MX];
int dist[MX][MX][35];
//뛰어넘었을 때의 상황을 위해서 3차원으로 설계
//걷다가 뛰던가 처음부터 뛰고 걷던가 뛰는 회수가 같으면 같은배열에 들어감
//큐에 여러개를 넣고 시작하는 BFS를 생각하면 됨

queue<tuple<int, int, int>> Q;

//걸었을 때
int walkX[4] = { 1, -1, 0, 0 };
int walkY[4] = { 0, 0, 1, -1 };

//뛰었을 때
int runX[8] = { 1, 2, -1, -2, 1, 2, -1, -2 };
int runY[8] = { 2, 1, 2, 1, -2, -1, -2, -1 };

int w, h;
int k;

void walk(int x, int y, int runNum);
void run(int x, int y, int runNum);

int main(void)
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> k >> h >> w;

	for (int i = 0; i < w; ++i)
		for (int j = 0; j < h; ++j)
			cin >> board[i][j];

	//dist 배열 초기화
	for (int k = 0; k < 35; ++k)
		for (int i = 0; i < w; ++i)
			for (int j = 0; j < h; ++j)
				dist[i][j][k] = -1;

	Q.push({ 0, 0, 0 });
	dist[0][0][0] = 0;

	while (!Q.empty())
	{
		int curX, curY, runNum;
		tie(curX, curY, runNum) = Q.front();
		Q.pop();

		if(runNum < k) //큐에 달렸을 경우 저장
			run(curX, curY, runNum);
		walk(curX, curY, runNum); //달린것과 상관없이 걷는 것도 저장
		//처음부터 걷고 마지막에 뛰는 경우도 어짜피 다 집어넣어서 처리 완료됨
	}

	int result = 99999999;
	for (int k = 0; k < 35; ++k)
	{
		if(dist[w - 1][h - 1][k] != -1)	//-1은 도착못한 경우니 결과값에서 뺌
			result = min(result, dist[w-1][h-1][k]);
	}
	if(result == 99999999)	//갱신이 안된경우
		cout << -1 << '\n';
	else	
		cout << result << '\n';
}

//걷는 경우
void walk(int x, int y, int runNum)
{
	for (int dir = 0; dir < 4; ++dir)
	{
		int nx = x + walkX[dir];
		int ny = y + walkY[dir];
		int run = runNum;

		if (nx < 0 || nx >= w || ny < 0 || ny >= h)
			continue;
		if (board[nx][ny] == 1 || dist[nx][ny][run] != -1)
			continue;

		Q.push({ nx, ny, run });
		dist[nx][ny][run] = dist[x][y][runNum] + 1;
	}
}

//뛰는 경우
void run(int x, int y, int runNum)
{
	int run = runNum + 1;
	for (int dir = 0; dir < 8; ++dir)
	{
		int nx = x + runX[dir];
		int ny = y + runY[dir];
		if (nx < 0 || nx >= w || ny < 0 || ny >= h)
			continue;
		if (dist[nx][ny][run] != -1 || board[nx][ny] == 1)
			continue;

		dist[nx][ny][run] = dist[x][y][runNum] + 1;
		Q.push({ nx, ny, run });
	}
}
{% endhighlight %}
