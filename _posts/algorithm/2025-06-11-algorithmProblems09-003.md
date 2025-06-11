---
layout: single
title: 백준 1012번 - 유기농 배추
categories: algorithmProblems
tags: [algorithm, BFS]
toc: true
toc_sticky: true
---

## 문제
[백준 1012번 문제](https://www.acmicpc.net/problem/1012)

차세대 영농인 한나는 강원도 고랭지에서 유기농 배추를 재배하기로 하였다. 농약을 쓰지 않고 배추를 재배하려면 배추를 해충으로부터 보호하는 것이 중요하기 때문에, 한나는 해충 방지에 효과적인 배추흰지렁이를 구입하기로 결심한다. 이 지렁이는 배추근처에 서식하며 해충을 잡아 먹음으로써 배추를 보호한다. 특히, 어떤 배추에 배추흰지렁이가 한 마리라도 살고 있으면 이 지렁이는 인접한 다른 배추로 이동할 수 있어, 그 배추들 역시 해충으로부터 보호받을 수 있다. 한 배추의 상하좌우 네 방향에 다른 배추가 위치한 경우에 서로 인접해있는 것이다.

한나가 배추를 재배하는 땅은 고르지 못해서 배추를 군데군데 심어 놓았다. 배추들이 모여있는 곳에는 배추흰지렁이가 한 마리만 있으면 되므로 서로 인접해있는 배추들이 몇 군데에 퍼져있는지 조사하면 총 몇 마리의 지렁이가 필요한지 알 수 있다. 예를 들어 배추밭이 아래와 같이 구성되어 있으면 최소 5마리의 배추흰지렁이가 필요하다. 0은 배추가 심어져 있지 않은 땅이고, 1은 배추가 심어져 있는 땅을 나타낸다.

|   |   |   |   |   |   |   |   |   |   |
|---|---|---|---|---|---|---|---|---|---|
|**1**|**1**|0|0|0|0|0|0|0|0|
|0|**1**|0|0|0|0|0|0|0|0|
|0|0|0|0|**1**|0|0|0|0|0|
|0|0|0|0|**1**|0|0|0|0|0|
|0|0|**1**|**1**|0|0|0|**1**|**1**|**1**|
|0|0|0|0|**1**|0|0|**1**|**1**|**1**|

## 입력

입력의 첫 줄에는 테스트 케이스의 개수 T가 주어진다. 그 다음 줄부터 각각의 테스트 케이스에 대해 첫째 줄에는 배추를 심은 배추밭의 가로길이 M(1 ≤ M ≤ 50)과 세로길이 N(1 ≤ N ≤ 50), 그리고 배추가 심어져 있는 위치의 개수 K(1 ≤ K ≤ 2500)이 주어진다. 그 다음 K줄에는 배추의 위치 X(0 ≤ X ≤ M-1), Y(0 ≤ Y ≤ N-1)가 주어진다. 두 배추의 위치가 같은 경우는 없다.

## 출력

각 테스트 케이스에 대해 필요한 최소의 배추흰지렁이 마리 수를 출력한다.

## 예제 입력 1 복사

```
2
10 8 17
0 0
1 0
1 1
4 2
4 3
4 5
2 4
3 4
7 4
8 4
9 4
7 5
8 5
9 5
7 6
8 6
9 6
10 10 1
5 5
```

## 예제 출력 2 복사

2

## 풀이
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

#define X first
#define Y second

int board[60][60];
bool vis[60][60];
int dx[4] = { 1, 0, -1, 0 };
int dy[4] = { 0, 1, 0, -1 };
int main(void)
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    int t;
    cin >> t;
    //tast case
    for (int tc = 0; tc < t; ++tc)
    {
        int m, n;
        int k;
        cin >> m >> n >> k;

        int result = 0; //배추흰지렁이 개수

        //배추밭 입력
        for (int i = 0; i < k; ++i)
        {
            int x, y;
            cin >> x >> y;
            board[x][y] = 1;
        }

        //각 칸마다 체크되었는지 확인 O(mn)
        for (int i = 0; i < m; ++i)
        {
            for (int j = 0; j < n; ++j)
            {
                if (vis[i][j] == 1 || board[i][j] == 0) //이미 체크되어있다면 검사 X
                    continue;

                result++;
                
                //BFS 시작
                queue<pair<int, int>> Q;
                Q.push({ i, j });
                vis[i][j] = 1;
                while (!Q.empty())
                {
                    auto cur = Q.front();
                    Q.pop();
                    for (int dir = 0; dir < 4; ++dir)
                    {
                        int nx = cur.X + dx[dir];
                        int ny = cur.Y + dy[dir];

                        if (nx < 0 || nx >= m || ny < 0 || ny >= n)
                            continue;
                        if (vis[nx][ny] == 1 || board[nx][ny] == 0)
                            continue;
                        Q.push({ nx, ny });
                        vis[nx][ny] = 1;
                    }
                }
            }
        }

        cout << result << '\n';
        for (int i = 0; i < 60; i++)
        {
            fill(board[i], board[i] + 60, 0);
            fill(vis[i], vis[i] + 60, 0);
        }
    }
}
{% endhighlight %}
