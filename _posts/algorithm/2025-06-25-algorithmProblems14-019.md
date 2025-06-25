---
layout: single
title: 백준 3190번 - 뱀
categories: algorithmProblems
tags: [algorithm, simulation]
toc: true
toc_sticky: true
---

## 문제
[백준 3190번 문제](https://www.acmicpc.net/problem/3190) <br>
'Dummy' 라는 도스게임이 있다. 이 게임에는 뱀이 나와서 기어다니는데, 사과를 먹으면 뱀 길이가 늘어난다. 뱀이 이리저리 기어다니다가 벽 또는 자기자신의 몸과 부딪히면 게임이 끝난다.

게임은 NxN 정사각 보드위에서 진행되고, 몇몇 칸에는 사과가 놓여져 있다. 보드의 상하좌우 끝에 벽이 있다. 게임이 시작할때 뱀은 맨위 맨좌측에 위치하고 뱀의 길이는 1 이다. 뱀은 처음에 오른쪽을 향한다.

뱀은 매 초마다 이동을 하는데 다음과 같은 규칙을 따른다.

- 먼저 뱀은 몸길이를 늘려 머리를 다음칸에 위치시킨다.
- 만약 벽이나 자기자신의 몸과 부딪히면 게임이 끝난다.
- 만약 이동한 칸에 사과가 있다면, 그 칸에 있던 사과가 없어지고 꼬리는 움직이지 않는다.
- 만약 이동한 칸에 사과가 없다면, 몸길이를 줄여서 꼬리가 위치한 칸을 비워준다. 즉, 몸길이는 변하지 않는다.

사과의 위치와 뱀의 이동경로가 주어질 때 이 게임이 몇 초에 끝나는지 계산하라.

## 입력

첫째 줄에 보드의 크기 N이 주어진다. (2 ≤ N ≤ 100) 다음 줄에 사과의 개수 K가 주어진다. (0 ≤ K ≤ 100)

다음 K개의 줄에는 사과의 위치가 주어지는데, 첫 번째 정수는 행, 두 번째 정수는 열 위치를 의미한다. 사과의 위치는 모두 다르며, 맨 위 맨 좌측 (1행 1열) 에는 사과가 없다.

다음 줄에는 뱀의 방향 변환 횟수 L 이 주어진다. (1 ≤ L ≤ 100)

다음 L개의 줄에는 뱀의 방향 변환 정보가 주어지는데, 정수 X와 문자 C로 이루어져 있으며. 게임 시작 시간으로부터 X초가 끝난 뒤에 왼쪽(C가 'L') 또는 오른쪽(C가 'D')로 90도 방향을 회전시킨다는 뜻이다. X는 10,000 이하의 양의 정수이며, 방향 전환 정보는 X가 증가하는 순으로 주어진다.

## 출력

첫째 줄에 게임이 몇 초에 끝나는지 출력한다.

## 예제 입력 1 복사

```
6
3
3 4
2 5
5 3
3
3 D
15 L
17 D
```

## 예제 출력 1 복사

9

## 풀이
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

queue<pair<int, int>> order;	//뱀의 방향 정보 L:왼쪽, D:오른쪽
deque<pair<int, int>> snake;	//뱀
int snakeDir;		//뱀 머리의 방향

int board[101][101];	//빈칸 0, 사과 1, 뱀 -1
int n;
int curTime;

int dx[] = { 0, 1, 0, -1 };	//최초 방향 오른쪽 (동, 남, 서, 북)
int dy[] = { 1, 0, -1, 0 };	//왼쪽으로 회전 -> 반시계 , 오른쪽으로 회전 -> 시계방향

bool SnakeMove();
void SnakeRotation(char rotation);
bool GameExit(int r, int c);

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n;
	int appleCnt = 0;
	cin >> appleCnt;
	for (int i = 0; i < appleCnt; i++)
	{
		int x, y;
		cin >> x >> y;
		board[x - 1][y - 1] = 1;
	}

	int infoCnt = 0;
	cin >> infoCnt;
	for (int i = 0; i < infoCnt; i++)
	{
		pair<int, char> infomation;
		cin >> infomation.first;
		cin >> infomation.second;
		order.push(infomation);
	}

	snake.push_front({ 0, 0 });
	board[0][0] = -1;
	snakeDir = 0;

	int ans = 0;
	int rotationTime = order.front().first;
	while (true)
	{
		if (ans >= rotationTime)
		{
			auto curOrder = order.front();
			SnakeRotation(curOrder.second);	//x초 뒤 회전 적용

			order.pop();	//다음 회전명령
			if (order.empty())	//명령이 더 없을 경우 이제 회전하지 않음
			{
				rotationTime = 0x7f7f7f7f;
			}
			else
			{
				curOrder = order.front();
				rotationTime = curOrder.first;	//다음 회전이 올 때까지의 시간
			}
		}
		
		if(!SnakeMove())
			break;

		ans++;
	}
	cout << ++ans;
}

bool SnakeMove()
{
	auto head = snake.front();
	head.first += dx[snakeDir];
	head.second += dy[snakeDir];
	if (!GameExit(head.first, head.second))
		return false;
	
	//사과를 먹음
	bool eatApple = false;
	if (eatApple = (board[head.first][head.second] == 1))
	{
		auto tail = snake.back();
		snake.push_back(tail);
	}

	for (auto iter = snake.begin(); iter != snake.end(); iter++)
	{
		auto temp = *iter;
		if (iter + 1 == snake.end())	//마지막 원소
		{
			if(eatApple)	//사과를 먹고 마지막일 경우 앞으로 한칸 전진안함
				break;
			
			board[temp.first][temp.second] = 0;	//사과를 먹지 않았을 경우 0표시
		}

		*iter = head;	//1칸씩 진행
		head = temp;
		board[iter->first][iter->second] = -1;	//진행하는 뱀이므로 -1표시
	}

	return true;
}

void SnakeRotation(char rotation)
{
	if (rotation == 'L' || rotation == 'l')	//왼쪽
	{
		snakeDir--;
		if (snakeDir < 0)
			snakeDir = 3;
	}
	else //오른쪽
	{
		snakeDir++;
		if (snakeDir >= 4)
			snakeDir = 0;
	}
}

bool GameExit(int r, int c)
{
	if (r < 0 || r >= n || c < 0 || c >= n)	//Out Of Bounce
		return false;
	
	if (board[r][c] == -1)	//머리가 몸에 닿았을 경우
		return false;

	return true;
}
{% endhighlight %}

## 다른풀이
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;
#define X first
#define Y second

int n, k;
int a, b, c, l;
char d;
int board[105][105]; // 1: 뱀, 2: 사과
int second = 0;
int dx[4] = {1, 0, -1, 0};  // [1][0]: 아래, [0][1]: 오른쪽, [-1][0]: 위, [0][-1]: 왼쪽
int dy[4] = {0, 1, 0, -1};
deque<pair<int, int>> dq;
queue<pair<int, char>> switchDir;

int main(void){
  ios::sync_with_stdio(0);
  cin.tie(0);

  cin >> n;
  cin >> k;
  while(k--){
    cin >> a >> b;
    // 사과
    board[a][b] = 2;
  }

  cin >> l;
  while(l--){
    // 방향전환
    cin >> c >> d;
    switchDir.push({c, d});
  }

  dq.push_front({1,1});
  int dir = 1;

  while(1){
    dir %= 4;

    auto cur = dq.front();
    board[cur.X][cur.Y] = 1;
    second++;

    int nx = cur.X + dx[dir];
    int ny = cur.Y + dy[dir];

    if(nx < 1 || nx > n || ny < 1 || ny > n) break;
    if(board[nx][ny] == 1) break;

    if(board[nx][ny] != 2) {
      auto tail = dq.back();
      board[tail.X][tail.Y] = 0;
      dq.pop_back();
    }

    auto sd = switchDir.front();
    if(sd.X == second){
      if(sd.Y == 'L') dir += 1;
      else if(sd.Y == 'D') dir += 3;
      switchDir.pop();
    }
    dq.push_front({nx, ny});
  }
  cout << second;
}
{% endhighlight %}