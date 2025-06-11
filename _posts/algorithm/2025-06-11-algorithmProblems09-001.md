---
layout: single
title: 백준 10026번 - 적록색약
categories: algorithmProblems
tags: [algorithm, BFS]
toc: true
toc_sticky: true
---

## 문제
[백준 10026번 문제](https://www.acmicpc.net/problem/10026)

적록색약은 빨간색과 초록색의 차이를 거의 느끼지 못한다. 따라서, 적록색약인 사람이 보는 그림은 아닌 사람이 보는 그림과는 좀 다를 수 있다.

크기가 N×N인 그리드의 각 칸에 R(빨강), G(초록), B(파랑) 중 하나를 색칠한 그림이 있다. 그림은 몇 개의 구역으로 나뉘어져 있는데, 구역은 같은 색으로 이루어져 있다. 또, 같은 색상이 상하좌우로 인접해 있는 경우에 두 글자는 같은 구역에 속한다. (색상의 차이를 거의 느끼지 못하는 경우도 같은 색상이라 한다)

예를 들어, 그림이 아래와 같은 경우에

```
RRRBB
GGBBB
BBBRR
BBRRR
RRRRR
```

적록색약이 아닌 사람이 봤을 때 구역의 수는 총 4개이다. (빨강 2, 파랑 1, 초록 1) 하지만, 적록색약인 사람은 구역을 3개 볼 수 있다. (빨강-초록 2, 파랑 1)

그림이 입력으로 주어졌을 때, 적록색약인 사람이 봤을 때와 아닌 사람이 봤을 때 구역의 수를 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 N이 주어진다. (1 ≤ N ≤ 100)

둘째 줄부터 N개 줄에는 그림이 주어진다.

## 출력

적록색약이 아닌 사람이 봤을 때의 구역의 개수와 적록색약인 사람이 봤을 때의 구역의 수를 공백으로 구분해 출력한다.

## 예제 입력 1 복사

```
5
RRRBB
GGBBB
BBBRR
BBRRR
RRRRR
```

## 예제 출력 1 복사

4 3

## 풀이
### 내가 푼 코드
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

#define X first
#define Y second

char board[105][105];
bool vis[105][105];

int dx[4] = { 1, 0, -1, 0 };
int dy[4] = { 0, 1, 0, -1 };
int n;

bool isEquelColor(bool bRGBlind, char firstColor, char curColor);
void BFS(int x, int y, bool mode);

int main(void)
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    cin >> n;
    for (int i = 0; i < n; ++i)
        cin >> board[i];

    //일반 사람의 BFS
    int normal = 0;
    for (int i = 0; i < n; ++i)
    {
        for (int j = 0; j < n; ++j)
        {
            if (vis[i][j] == 0)
            {
                BFS(i, j, false);
                normal++;
            }
        }
    }
    
    //방문배열 초기화
    for (int i = 0; i < n; ++i)
        fill(vis[i], vis[i] + n, 0);

    //색약 사람의 BFS
    int unNormal = 0;
    for (int i = 0; i < n; ++i)
    {
        for (int j = 0; j < n; ++j)
        {
            if (vis[i][j] == 0)
            {
                BFS(i, j, true);
                unNormal++;
            }
        }
    }
    cout << normal << ' ' << unNormal;
}

//색약 - 일반 사람인지를 받아서 같은 색인지 다른 색인지 분간
bool isEquelColor(bool bRGBlind, char firstColor, char curColor)
{
    if (!bRGBlind)
    {
        if (firstColor != curColor)
            return false;
        else 
            return true;
    }
    else
    {
        if (firstColor != 'B')  // R, G
        {
            if (curColor == 'B')
                return false;
            else 
                return true;
        }
        else   //B
        {
            if (curColor == 'B')
                return true;
            else
                return false;
        }
    }
}

void BFS(int x, int y, bool mode)
{
    queue<pair<int, int>> Q;
    char firstChar = board[x][y];
    Q.push({ x, y });
    vis[x][y] = 1;
    while (!Q.empty())
    {
        auto cur = Q.front();
        Q.pop();
        for (int dir = 0; dir < 4; ++dir)
        {
            int nx = cur.X + dx[dir];
            int ny = cur.Y + dy[dir];
            if (nx < 0 || nx >= n || ny < 0 || ny >= n)
                continue;
            if (vis[nx][ny] == 1 || !isEquelColor(mode, firstChar, board[nx][ny]))
                continue;
            vis[nx][ny] = 1;
            Q.push({ nx, ny });
        }
    }
}
{% endhighlight %}

### 참고코드
<div class="notice--info" markdown="1">
**<u>board 자체를 바꿈</u>**

조건을 복잡하게 나눌 필요 없이 맵을 바꾸면 조건부를 쉽게 가져갈 수 있다
</div>

{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;
#define X first
#define Y second
char board[101][101];
bool vis[101][101];
int n;
int dx[4] = { 1,0,-1,0 };
int dy[4] = { 0,1,0,-1 }; 

void bfs(int i, int j) {
  queue<pair<int, int>> Q;
  Q.push({ i,j });
  vis[i][j] = 1;
  while (!Q.empty()) {
    auto cur = Q.front(); Q.pop();
    for (int dir = 0; dir < 4; dir++) {
      int nx = cur.X + dx[dir];
      int ny = cur.Y + dy[dir];
      if (nx < 0 || nx >= n || ny < 0 || ny >= n) continue;
      if (vis[nx][ny] == 1 || board[i][j] != board[nx][ny]) continue;
      vis[nx][ny] = 1;
      Q.push({ nx,ny });
    }
  }
}

int area(){
  int cnt = 0;
  for (int i = 0; i < n; i++) {
    for (int j = 0; j < n; j++) {
      if (!vis[i][j]) {
        cnt++;
        bfs(i, j);
      }
    }
  }
  return cnt;
}

int main(void) {
  ios::sync_with_stdio(0);
  cin.tie(0);
  cin >> n;
  for (int i = 0; i < n; i++) {
    for (int j = 0; j < n; j++) {
      cin >> board[i][j];
    }
  }
  int not_g = area(); //적록색약이 아닌사람

  // 적록색약인 사람을 구하기위한 방문배열 초기화
  for(int i = 0; i < n; i++)
    fill(vis[i], vis[i]+n, false);
  
  // 적록색약은 초록과 빨강을 구분 못하므로 초록이면 빨강으로 바꿔줌
  for (int i = 0; i < n; i++) {
    for (int j = 0; j < n; j++) {
      if (board[i][j] == 'G')
        board[i][j] = 'R';
    }
  }

  int is_g = area(); //적록색약인 사람
  cout << not_g << " " << is_g;
  return 0;
}
{% endhighlight %}
