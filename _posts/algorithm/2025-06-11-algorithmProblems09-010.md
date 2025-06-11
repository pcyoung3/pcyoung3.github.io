---
layout: single
title: 백준 4179번 - 불!
categories: algorithmProblems
tags: [algorithm, BFS]
toc: true
toc_sticky: true
---

## 문제
[백준 4179번 문제](https://www.acmicpc.net/problem/4179)

지훈이는 미로에서 일을 한다. 지훈이를 미로에서 탈출하도록 도와주자!

미로에서의 지훈이의 위치와 불이 붙은 위치를 감안해서 지훈이가 불에 타기전에 탈출할 수 있는지의 여부, 그리고 얼마나 빨리 탈출할 수 있는지를 결정해야한다.

지훈이와 불은 매 분마다 한칸씩 수평또는 수직으로(비스듬하게 이동하지 않는다) 이동한다.

불은 각 지점에서 네 방향으로 확산된다.

지훈이는 미로의 가장자리에 접한 공간에서 탈출할 수 있다.

지훈이와 불은 벽이 있는 공간은 통과하지 못한다.

## 입력

입력의 첫째 줄에는 공백으로 구분된 두 정수 R과 C가 주어진다. 단, 1 ≤ R, C ≤ 1000 이다. R은 미로 행의 개수, C는 열의 개수이다.

다음 입력으로 R줄동안 각각의 미로 행이 주어진다.

각각의 문자들은 다음을 뜻한다.

- #: 벽
- .: 지나갈 수 있는 공간
- J: 지훈이의 미로에서의 초기위치 (지나갈 수 있는 공간)
- F: 불이 난 공간

J는 입력에서 하나만 주어진다.

## 출력

지훈이가 불이 도달하기 전에 미로를 탈출 할 수 없는 경우 IMPOSSIBLE 을 출력한다.

지훈이가 미로를 탈출할 수 있는 경우에는 가장 빠른 탈출시간을 출력한다.

## 예제 입력 1 복사

```
4 4
####
#JF#
#..#
#..#
```

## 예제 출력 1 복사
3

## 정답코드

<div class="notice--warning" markdown="1">
**<u>시작점이 2종류일 때</u>** 

시작점이 2종류일때는 BFS를 2번 돌리면 된다.
</div>

### 반례들
```
//일반적인 상황
6 5
#####
#J#F#
#...#
#...#
#...#
#..##

//불가능한 상황
6 5
#####
#JF.#
#...#
#...#
#....
#####

//불가능한 상황 2
6 5
#####
#J.F#
#...#
#...#
#....
##.##

//시작하자마자 탈출
6 5
#J###
#.#F#
#...#
#...#
#...#
#####

//불과 분리되어있는 상황
6 5
#####
#J#F#
#.#.#
#.#.#
#.#.#
#..##
```

{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

#define X first
#define Y second

char board[1005][1005];
int fire[1005][1005];   //불의 전파 시간
int dist[1005][1005];   //지훈이의 이동시간
int dx[4] = { 1, 0 , -1, 0 };
int dy[4] = { 0, 1, 0, -1 };

int main(void)
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    int n, m;
    cin >> n >> m;
    
    queue<pair<int, int>> fireQ;    //불
    queue<pair<int, int>> Q;    //지훈

    for (int i = 0; i < n; i++) //거리 배열 -1로 초기화
    {
        fill(fire[i], fire[i] + m, -1);
        fill(dist[i], dist[i] + m, -1);
    }

    // 시작지점에 미리 표시
    for (int i = 0; i < n; i++)
    {
        for (int j = 0; j < m; j++)
        {
            cin >> board[i][j]; // cin >> board[i];  // String 이라서 for문 하나쓰고 한줄로 받아도 됨
            if (board[i][j] == 'F')
            {
                fireQ.push({ i, j });
                fire[i][j] = 0;
            }
            if (board[i][j] == 'J')
            { 
                Q.push({ i, j });
                dist[i][j] = 0;
            }
                
        }
    }

    //불의 BFS - 일반적인 BFS
    while (!fireQ.empty())
    {
        auto cur = fireQ.front();
        fireQ.pop();
        for (int dir = 0; dir < 4; ++dir)
        {
            int nx = cur.X + dx[dir];
            int ny = cur.Y + dy[dir];
            if (nx < 0 || nx >= n || ny < 0 || ny >= m)
                continue;
            if (fire[nx][ny] != -1 || board[nx][ny] == '#')
                continue;
            fire[nx][ny] = fire[cur.X][cur.Y] + 1;
            fireQ.push({ nx, ny });
        }
    }

    //지훈이의 BFS
    while (!Q.empty())
    {
        auto cur = Q.front();
        Q.pop();
        for (int dir = 0; dir < 4; ++dir)
        {
            int nx = cur.X + dx[dir];
            int ny = cur.Y + dy[dir];
            
            //cur 의 주변 타일을 검사했을 때 경계선을 넘었다면 현재 위치는 배열의 끝자락이니 탈출 성공
            if (nx < 0 || nx >= n || ny < 0 || ny >= m)
            {
                cout << dist[cur.X][cur.Y] + 1;
                return 0;
            }            
            if (dist[nx][ny] != -1 || board[nx][ny] == '#' || board[nx][ny] == 'F')
                continue;
            //불이 이미 지나갔다면 갈 수 없음
            //불이 붙지 않은 타일은 -1인데 이거랑 대소비교v 를 하면 안되서 예외처리
            if (dist[cur.X][cur.Y] + 1 >= fire[nx][ny] && fire[nx][ny] != -1)
                continue;
            dist[nx][ny] = dist[cur.X][cur.Y] + 1;
            Q.push({ nx, ny });
        }
    }

    //전수조사를 해도 경계선에 다다르지 못했으므로 실패
    cout << "IMPOSSIBLE";
}
{% endhighlight %}

<div class="notice--warning" markdown="1">
**<u>주의</u>** 

문제에서는 불의 진행경로가 지훈이의 진행경로에는 영향을 끼쳤지만 그 반대상황에는 끼치지 않았다. 이처럼 어느 하나가 독립적이지 않고 서로에게 영향을 끼친다면 백트레킹 기법이 필요
</div>
