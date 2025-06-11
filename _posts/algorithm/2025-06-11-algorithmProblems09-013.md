---
layout: single
title: 백준 2146번 - 다리만들기
categories: algorithmProblems
tags: [algorithm, BFS]
toc: true
toc_sticky: true
---

## 문제
[백준 2146번 문제](https://www.acmicpc.net/problem/2146)

여러 섬으로 이루어진 나라가 있다. 이 나라의 대통령은 섬을 잇는 다리를 만들겠다는 공약으로 인기몰이를 해 당선될 수 있었다. 하지만 막상 대통령에 취임하자, 다리를 놓는다는 것이 아깝다는 생각을 하게 되었다. 그래서 그는, 생색내는 식으로 한 섬과 다른 섬을 잇는 다리 하나만을 만들기로 하였고, 그 또한 다리를 가장 짧게 하여 돈을 아끼려 하였다.

이 나라는 N×N크기의 이차원 평면상에 존재한다. 이 나라는 여러 섬으로 이루어져 있으며, 섬이란 동서남북으로 육지가 붙어있는 덩어리를 말한다. 다음은 세 개의 섬으로 이루어진 나라의 지도이다.

![2146_p_1.png](/assets/images/algorithm/2146_p_1.png)

위의 그림에서 색이 있는 부분이 육지이고, 색이 없는 부분이 바다이다. 이 바다에 가장 짧은 다리를 놓아 두 대륙을 연결하고자 한다. 가장 짧은 다리란, 다리가 격자에서 차지하는 칸의 수가 가장 작은 다리를 말한다. 다음 그림에서 두 대륙을 연결하는 다리를 볼 수 있다.

![2146_p_2.png](/assets/images/algorithm/2146_p_2.png)

물론 위의 방법 외에도 다리를 놓는 방법이 여러 가지 있으나, 위의 경우가 놓는 다리의 길이가 3으로 가장 짧다(물론 길이가 3인 다른 다리를 놓을 수 있는 방법도 몇 가지 있다).

지도가 주어질 때, 가장 짧은 다리 하나를 놓아 두 대륙을 연결하는 방법을 찾으시오.

## 입력

첫 줄에는 지도의 크기 N(100이하의 자연수)가 주어진다. 그 다음 N줄에는 N개의 숫자가 빈칸을 사이에 두고 주어지며, 0은 바다, 1은 육지를 나타낸다. 항상 두 개 이상의 섬이 있는 데이터만 입력으로 주어진다.

## 출력

첫째 줄에 가장 짧은 다리의 길이를 출력한다.

## 예제 입력 1 복사
```
10
1 1 1 0 0 0 0 1 1 1
1 1 1 1 0 0 0 0 1 1
1 0 1 1 0 0 0 0 1 1
0 0 1 1 1 0 0 0 0 1
0 0 0 1 0 0 0 0 0 1
0 0 0 0 0 0 0 0 0 1
0 0 0 0 0 0 0 0 0 0
0 0 0 0 1 1 0 0 0 0
0 0 0 0 1 1 1 0 0 0
0 0 0 0 0 0 0 0 0 0
```

## 예제 출력 1 복사

```
3
```
   
---
## 풀이

<div class="notice--warning" markdown="1">
**<u>각 타일마다 BFS를 돌리는 약간은 무식한 방법으로 푸는 문제</u>** 

시간복잡도 : O(n^4)
시간제한이 2초이고 한 변의 크기가 100이하기 때문에 가능한 방법
</div>

{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

#define X first
#define Y second

const int MX = 105;
int board[MX][MX];
int dist[MX][MX];
bool vis[MX][MX];

int dx[4] = { 1, -1, 0, 0 };
int dy[4] = { 0, 0, 1, -1 };

int n;

bool OutOfBound(int x, int y)
{
    return x < 0 || x >= n || y < 0 || y >= n;
}

int main(void) 
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    cin >> n;
    for (int i = 0; i < n; ++i)
    {
        for (int j = 0; j < n; ++j)
        {
            cin >> board[i][j];
        }
    }

    //각 섬에 번호를 남기는 작업
    queue<pair<int, int>> Q;
    int cnt = 0;
    for (int i = 0; i < n; ++i)
    {
        for (int j = 0; j < n; ++j)
        {
            if (board[i][j] == 0 || vis[i][j])
                continue;
            cnt++;
            Q.push({ i, j });
            board[i][j] = cnt;
            vis[i][j] = 1;
            while (!Q.empty())
            {
                auto cur = Q.front();
                Q.pop();
                for (int dir = 0; dir < 4; ++dir)
                {
                    int nx = cur.X + dx[dir];
                    int ny = cur.Y + dy[dir];
                    if (OutOfBound(nx, ny))
                        continue;
                    if (board[nx][ny] == 0 || vis[nx][ny] == 1)
                        continue;
                    vis[nx][ny] = 1;
                    board[nx][ny] = cnt;
                    Q.push({ nx, ny });
                }
            }
        }
    }

    for (int i = 0; i < n; ++i)
        fill(dist[i], dist[i] + n, -1);

    //육지 하나당 한번의 BFS를 돌게 됨
    //BFS를 한번 돌 때 O(n^2)인데 한 칸당 한번의 BFS를 도니까 O(n^4)가 됨
    queue<pair<int, int>> Q2;
    int result = 1999999999;
    for (int i = 0; i < n; ++i)
    {
        for (int j = 0; j < n; ++j)
        {
            if (board[i][j] == 0)
                continue;
            for (int i = 0; i < n; ++i)
                fill(dist[i], dist[i] + n, -1);

            int startLand = board[i][j];  //출발점의 육지 저장장
            Q2.push({ i, j });
            dist[i][j] = 0;
            while (!Q2.empty())
            {
                auto cur = Q2.front();
                Q2.pop();
                for (int dir = 0; dir < 4; ++dir)
                {
                    int nx = cur.X + dx[dir];
                    int ny = cur.Y + dy[dir];

                    if (OutOfBound(nx, ny))
                        continue;
                    if (dist[nx][ny] != -1) //이미 방문한 곳
                        continue;
                    if (board[nx][ny] != 0) //육지
                    {
                        if (board[nx][ny] != startLand) //출발점의 육지와 다른 경우
                            result = min(result, dist[cur.X][cur.Y]);   //결과값 반영
                        continue;   //출발점의 육지와 같은 경우
                    }
                    dist[nx][ny] = dist[cur.X][cur.Y] + 1;  //육지가 아닌 경우
                    Q2.push({ nx, ny });
                }
            }
        }
    }

    cout << result << '\n';
}
{% endhighlight %}
   
### 다른풀이

<div class="notice--info" markdown="1">
**<u>시간복잡도를 O(n^2)으로 줄이는 방법</u>** 

육지타일 하나하나 BFS를 돌리는 것을 최종적으로 한번만 돌려서 시간복잡도를 n^2만큼 줄일 수 있다.
이렇게 하기 위해서는 위에서 사용된 2중for문을 없애야되는데 없애는 방법의 포인트는 2개이다
   
1. 육지인 부분을 전부 큐에 넣고 시작하면 타일 하나하나를 검사할 필요가 없어진다.
2. 한번 검사할 때마다 dist 배열을 초기화했어야 했는데 육지를 확장하면서 dist를 증가시키고, 각 최초 육지부터 연장된 육지까지의 거리를 합치는 방식을 사용해서 초기화를 수행하지 않을 수 있다

![Pasted image 20230608142857.png](/assets/images/algorithm/Pasted image 20230608142857.png)
</div>
    
```cpp
#include <bits/stdc++.h>
using namespace std;
#define X first
#define Y second
int board[101][101];
bool vis[101][101]; // 섬을 분류하는 첫 번째 bfs에서 사용
int dist[101][101]; // 다리의 길이를 구하는 두 번째 bfs에서 사용
int dx[4] = { 1,-1,0,0 };
int dy[4] = { 0,0,1,-1 };
int n;
bool OOB(int a, int b) { // Out Of Bounds인지 체크하는 함수
    return a < 0 or a >= n or b < 0 or b >= n;
}

// 섬을 분류하는 첫 번째 bfs
void island() {
    int idx = 1; // 섬의 번호. board를 이 섬의 번호로 갱신할 예정
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            if (vis[i][j] || board[i][j] == 0) continue;
            // 아직 방문하지 않은 육지에 도달
            queue<pair<int, int> > Q;
            Q.push({ i,j });
            vis[i][j] = true;
            while (!Q.empty()) {
                auto cur = Q.front(); Q.pop();
                board[cur.X][cur.Y] = idx;
                for (int dir = 0; dir < 4; dir++) {
                    int nx = cur.X + dx[dir];
                    int ny = cur.Y + dy[dir];
                    if (OOB(nx, ny) || vis[nx][ny] || board[nx][ny] == 0) continue;
                    Q.push({ nx,ny });
                    vis[nx][ny] = true;
                }
            }
            idx++; // 번호를 1 증가시켜 다음 섬에는 1 증가된 값이 기록될 수 있게끔 한다.
        }
    }
}

int main(void) {
    ios::sync_with_stdio(0);
    cin.tie(0);
    cin >> n;
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            cin >> board[i][j];
    island();
    for (int i = 0; i < n; i++)
        fill(dist[i], dist[i] + n, -1);
    queue<pair<int, int>> Q;
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            if (board[i][j] != 0) {
                dist[i][j] = 0;
                Q.push({ i,j });
            }
        }
    }
    int ans = 999999;
    while (!Q.empty()) {
        auto cur = Q.front(); Q.pop();
        for (int dir = 0; dir < 4; dir++) {
            int nx = cur.X + dx[dir];
            int ny = cur.Y + dy[dir];
            if (OOB(nx, ny) || board[nx][ny] == board[cur.X][cur.Y]) // OOB거나 인접한 곳이 같은 섬일 경우
                continue;
            if (board[nx][ny] != 0) { // 인접한 곳이 다른 섬일 경우
                // dist[nx][ny] : 건너편 섬의 확장된 거리(0일수도 있음)
                // dist[cur.X][cur.Y] : 현 상태 섬의 확장된 거리(0일수도 있음)
                // 2개를 합하면 섬과 섬 사이의 거리
                ans = min(ans, dist[nx][ny] + dist[cur.X][cur.Y]); // cur와 (nx, ny) 각각이 육지로부터 떨어진 거리를 합하면 된다.
            }
            else { // 바다일 경우
                board[nx][ny] = board[cur.X][cur.Y]; //육지를 확장하는 부분
                dist[nx][ny] = dist[cur.X][cur.Y] + 1;
                Q.push({ nx,ny });
            }
        }
    }
    cout << ans;
}
```
