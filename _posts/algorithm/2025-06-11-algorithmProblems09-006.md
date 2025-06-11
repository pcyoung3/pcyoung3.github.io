---
layout: single
title: 백준 1697번 - 숨바꼭질
categories: algorithmProblems
tags: [algorithm, BFS]
toc: true
toc_sticky: true
---

## 문제
[백준 1697번 문제](https://www.acmicpc.net/problem/1697)

수빈이는 동생과 숨바꼭질을 하고 있다. 수빈이는 현재 점 N(0 ≤ N ≤ 100,000)에 있고, 동생은 점 K(0 ≤ K ≤ 100,000)에 있다. 수빈이는 걷거나 순간이동을 할 수 있다. 만약, 수빈이의 위치가 X일 때 걷는다면 1초 후에 X-1 또는 X+1로 이동하게 된다. 순간이동을 하는 경우에는 1초 후에 2\*X의 위치로 이동하게 된다.

수빈이와 동생의 위치가 주어졌을 때, 수빈이가 동생을 찾을 수 있는 가장 빠른 시간이 몇 초 후인지 구하는 프로그램을 작성하시오.

## 입력

첫 번째 줄에 수빈이가 있는 위치 N과 동생이 있는 위치 K가 주어진다. N과 K는 정수이다.

## 출력

수빈이가 동생을 찾는 가장 빠른 시간을 출력한다.

## 예제 입력 1 복사
```
5 17
```

## 예제 출력 1 복사

```
4
```

## 정답코드

<div class="notice--warning" markdown="1">
**<u>1차원 BFS</u>**

BFS처럼 보이지 않지만 BFS 문제이다
</div>

### 내가 푼 코드
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

int board[100005];  //1차원 배열로 충분
int dist[100005];
int dx[3] = { -1, 1, 2 };   // -1, +1, *2
int main(void)
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    int n, k;
    cin >> n >> k;

    fill(dist, dist + 100004, -1);

    queue<int> Q;
    Q.push(n);
    dist[n] = 0;
    while (!Q.empty())
    {
        int cur = Q.front();
        Q.pop();

        if (cur == k)   // 목적지에 도착했을 경우
        {
            cout << dist[cur];
            return 0;
        }
        for (int dir = 0; dir < 3; ++dir)
        {
            int nx;
            if (dir == 2)
                nx = cur * dx[dir];
            else
                nx = cur + dx[dir];

            if (nx < 0 || nx > 100000)
                continue;
            if (dist[nx] != -1)
                continue;
            Q.push(nx);
            dist[nx] = dist[cur] + 1;
        }
    }
}
{% endhighlight %}

### 참고코드
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;
#define X first
#define Y second
int dist[100002];
int n,k;
int main(void){
  ios::sync_with_stdio(0);
  cin.tie(0);
  cin >> n >> k;
  fill(dist, dist+100001,-1);
  dist[n] = 0;
  queue<int> Q;
  Q.push(n);
  while(dist[k] == -1){
    int cur = Q.front(); Q.pop();
    for(int nxt : {cur-1, cur+1, 2*cur}){  //범위기반 for문을 다음과 같이 사용할 수도 있으니 참고
      if(nxt < 0 || nxt > 100000) continue;
      if(dist[nxt] != -1) continue;
      dist[nxt] = dist[cur]+1;
      Q.push(nxt);
    }        
  }
  cout << dist[k];
}
{% endhighlight %}
