---
layout: single
title: 백준 1926번 - 그림
categories: algorithmProblems
tags: [algorithm, BFS]
toc: true
toc_sticky: true
---

## 문제
[백준 1926번 문제](https://www.acmicpc.net/problem/1926)

어떤 큰 도화지에 그림이 그려져 있을 때, 그 그림의 개수와, 그 그림 중 넓이가 가장 넓은 것의 넓이를 출력하여라. 단, 그림이라는 것은 1로 연결된 것을 한 그림이라고 정의하자. 가로나 세로로 연결된 것은 연결이 된 것이고 대각선으로 연결이 된 것은 떨어진 그림이다. 그림의 넓이란 그림에 포함된 1의 개수이다.

## 입력

첫째 줄에 도화지의 세로 크기 n(1 ≤ n ≤ 500)과 가로 크기 m(1 ≤ m ≤ 500)이 차례로 주어진다. 두 번째 줄부터 n+1 줄 까지 그림의 정보가 주어진다. (단 그림의 정보는 0과 1이 공백을 두고 주어지며, 0은 색칠이 안된 부분, 1은 색칠이 된 부분을 의미한다)

## 출력

첫째 줄에는 그림의 개수, 둘째 줄에는 그 중 가장 넓은 그림의 넓이를 출력하여라. 단, 그림이 하나도 없는 경우에는 가장 넓은 그림의 넓이는 0이다.

## 예제 입력 1

```
6 5
1 1 0 1 1
0 1 1 0 0
0 0 0 0 0
1 0 1 1 1
0 0 1 1 1
0 0 1 1 1
```

## 예제 출력 1

```
4
9
```

## 정답코드

<div class="notice--warning" markdown="1">
**<u>팁</u>** 
1. 그림의 개수를 세는 방법 : 2중for문을 돌면서 BFS를 수행하고 방문하지 않은 1인지점을 체크
2. 그림의 넓이를 세는 방법 : queue에서 pop 할 때마다 더해주면 넓이를 셀 수 있음
</div>

{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

#define X first
#define Y second
int board[502][502];
int vis[502][502];
int n = 0, m = 0;
int dx[4] = { 0, 1, 0, -1 };
int dy[4] = { 1, 0, -1, 0 };
int main(void)
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    int input;
    cin >> n >> m;
    for (int i = 0; i < n; i++)
    {
        for (int j = 0; j < m; j++)
        {
            cin >> input;
            board[i][j] = input;
        }
    }
    
    int num = 0;
    int maxArea = 0;
    for (int i = 0; i < n; i++)
    {
        for (int j = 0; j < m; j++)
        {
            if (board[i][j] == 1 && vis[i][j] == 0) // i, j 가 시작점일 때
            {
                int area = 0;   //그림의 넓이
                num++;      //그림의 개수
                queue<pair<int, int>> Q;
                Q.push({ i, j });
                vis[i][j] = 1;
                while (!Q.empty())
                {
                    pair<int, int> cur = { Q.front() };
                    Q.pop();
                    area++; //큐에 들어있는 원소를 뺄 때마다 넓이가 1씩 증가
                    for (int dir = 0; dir < 4; ++dir)
                    {
                        int nx = cur.X + dx[dir];
                        int ny = cur.Y + dy[dir];
                        if (nx < 0 || nx >= n || ny < 0 || ny >= m)
                            continue;
                        if (vis[nx][ny] == 1 || board[nx][ny] == 0)
                            continue;
                        Q.push({ nx, ny });
                        vis[nx][ny] = 1;
                    }
                }

                if (maxArea < area)//최대 그림넓이 찾는 부분 
                    maxArea = area;
                //maxArea = max(maxArea, area); // 해당 코드도 가능
            }
        }
    }

    cout << num << '\n' << maxArea;
}
{% endhighlight %}
