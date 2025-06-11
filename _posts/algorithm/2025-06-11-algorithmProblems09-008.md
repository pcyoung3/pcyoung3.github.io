---
layout: single
title: 백준 2178번 - 미로탐색
categories: algorithmProblems
tags: [algorithm, BFS]
toc: true
toc_sticky: true
---

## 문제
[백준 2178번 문제](https://www.acmicpc.net/problem/2178)

N×M크기의 배열로 표현되는 미로가 있다.

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|1|0|1|1|1|1|
|1|0|1|0|1|0|
|1|0|1|0|1|1|
|1|1|1|0|1|1|

미로에서 1은 이동할 수 있는 칸을 나타내고, 0은 이동할 수 없는 칸을 나타낸다. 이러한 미로가 주어졌을 때, (1, 1)에서 출발하여 (N, M)의 위치로 이동할 때 지나야 하는 최소의 칸 수를 구하는 프로그램을 작성하시오. 한 칸에서 다른 칸으로 이동할 때, 서로 인접한 칸으로만 이동할 수 있다.

위의 예에서는 15칸을 지나야 (N, M)의 위치로 이동할 수 있다. 칸을 셀 때에는 시작 위치와 도착 위치도 포함한다.

## 입력

첫째 줄에 두 정수 N, M(2 ≤ N, M ≤ 100)이 주어진다. 다음 N개의 줄에는 M개의 정수로 미로가 주어진다. 각각의 수들은 **붙어서** 입력으로 주어진다.

## 출력

첫째 줄에 지나야 하는 최소의 칸 수를 출력한다. 항상 도착위치로 이동할 수 있는 경우만 입력으로 주어진다.

## 예제 입력 1 복사

```
4 6
101111
101010
101011
111011
```

## 예제 출력 1 복사

```
15
```

## 정답코드

<div class="notice--warning" markdown="1">
**<u>팁</u>** 

queue에서 Push할 때 +1 씩 시키면 시작점에서부터 거리가 얼마인지 알 수 있다
</div>

### 내가 푼 코드
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

#define X first
#define Y second

int board[105][105];
int dist[105][105]; //거리를 구하는 문제이기 때문에 vis대신 dist 사용
int dx[4] = { 0, 1, 0, -1 };
int dy[4] = { 1, 0, -1, 0 };
int main(void)
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    int n, m;
    cin >> n >> m;

    string input;
    for (int i = 0; i < n; i++)
    {
        cin >> input;
        for (int j = 0; j < input.size(); ++j)
        {
            char ch = input[j];
            board[i][j] = atoi(&ch);
        }
    }

    for (int i = 0; i < 105; i++)
        fill(dist[i], dist[i] + 105, -1);

    queue<pair<int, int>> Q;
    Q.push({ 0, 0 });
    dist[0][0] = 0;

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
            if (dist[nx][ny] != -1 || board[nx][ny] != 1)
                continue;
            dist[nx][ny] = dist[cur.X][cur.Y] + 1;  // 이 부분에서 +1씩 더해주면 거리를 구할 수 있음
            Q.push({ nx, ny });
        }
    }
    cout << dist[n - 1][m - 1]+1;
}
{% endhighlight %}

### 참고용 코드(string 처리법)
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;
#define X first
#define Y second
string board[102];
int dist[102][102];
int n,m;
int dx[4] = {1,0,-1,0};
int dy[4] = {0,1,0,-1};
int main(void)
{
  ios::sync_with_stdio(0);
  cin.tie(0);
  
  cin >> n >> m;
  for(int i = 0; i < n; i++)
    cin >> board[i];
  for(int i = 0; i < n; i++) 
	  fill(dist[i],dist[i]+m,-1);
  queue<pair<int,int> > Q;
  Q.push({0,0});
  dist[0][0] = 0;
  while(!Q.empty())
  {
    auto cur = Q.front(); Q.pop();
    for(int dir = 0; dir < 4; dir++)
    {
      int nx = cur.X + dx[dir];
      int ny = cur.Y + dy[dir];
      if(nx < 0 || nx >= n || ny < 0 || ny >= m) 
	      continue;
      if(dist[nx][ny] >= 0 || board[nx][ny] != '1') 
	      continue;
      dist[nx][ny] = dist[cur.X][cur.Y]+1;
      Q.push({nx,ny});
    }
  }
  cout << dist[n-1][m-1]+1; // 문제의 특성상 거리+1이 정답
}
{% endhighlight %}
