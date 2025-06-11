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
    
    // 테스트 케이스 처리
    for (int tc = 0; tc < t; ++tc)
    {
        int m, n, k;
        cin >> m >> n >> k;

        int result = 0; // 배추흰지렁이 개수

        // 배추밭 입력
        for (int i = 0; i < k; ++i)
        {
            int x, y;
            cin >> x >> y;
            board[x][y] = 1;
        }

        // 각 칸마다 체크되었는지 확인 O(mn)
        for (int i = 0; i < m; ++i)
        {
            for (int j = 0; j < n; ++j)
            {
                // 이미 방문했거나 배추가 없다면 건너뛰기
                if (vis[i][j] == 1 || board[i][j] == 0) 
                    continue;

                result++; // 새로운 연결 구성 요소 발견
                
                // BFS 시작
                queue<pair<int, int>> Q;
                Q.push({ i, j });
                vis[i][j] = 1;
                
                while (!Q.empty())
                {
                    auto cur = Q.front();
                    Q.pop();
                    
                    // 4방향 탐색
                    for (int dir = 0; dir < 4; ++dir)
                    {
                        int nx = cur.X + dx[dir];
                        int ny = cur.Y + dy[dir];

                        // 경계 체크
                        if (nx < 0 || nx >= m || ny < 0 || ny >= n)
                            continue;
                        // 이미 방문했거나 배추가 없다면 건너뛰기
                        if (vis[nx][ny] == 1 || board[nx][ny] == 0)
                            continue;
                            
                        Q.push({ nx, ny });
                        vis[nx][ny] = 1;
                    }
                }
            }
        }

        cout << result << '\n';
        
        // 다음 테스트 케이스를 위한 초기화
        for (int i = 0; i < 60; i++)
        {
            fill(board[i], board[i] + 60, 0);
            fill(vis[i], vis[i] + 60, 0);
        }
    }
    
    return 0;
}
{% endhighlight %}
