---
layout: single
title: 백준 13549번 - 숨바꼭질3
categories: algorithmProblems
tags: [algorithm, BFS]
toc: true
toc_sticky: true
---

## 문제
[백준 13549번 문제](https://www.acmicpc.net/problem/13549)

수빈이는 동생과 숨바꼭질을 하고 있다. 수빈이는 현재 점 N(0 ≤ N ≤ 100,000)에 있고, 동생은 점 K(0 ≤ K ≤ 100,000)에 있다. 수빈이는 걷거나 순간이동을 할 수 있다. 만약, 수빈이의 위치가 X일 때 걷는다면 1초 후에 X-1 또는 X+1로 이동하게 된다. 순간이동을 하는 경우에는 0초 후에 2\*X의 위치로 이동하게 된다.

수빈이와 동생의 위치가 주어졌을 때, 수빈이가 동생을 찾을 수 있는 가장 빠른 시간이 몇 초 후인지 구하는 프로그램을 작성하시오.

## 입력

첫 번째 줄에 수빈이가 있는 위치 N과 동생이 있는 위치 K가 주어진다. N과 K는 정수이다.

## 출력

수빈이가 동생을 찾는 가장 빠른 시간을 출력한다.

## 예제 입력 1 복사

5 17

## 예제 출력 1 복사

2

## 풀이

<div class="notice--warning" markdown="1">
**<u>가중치가 있는 BFS</u>** 
다익스트라로도 풀 수 있지만 2배수 하는걸 따로 처리해도 가능
</div>

{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

const int MX = 200005;
int dist[MX];

queue<int> Q;

// 현재 인덱스의 2배수 하는 것부터 전부 큐에 집어넣음
void teleport(int idx)
{
    int teleport = idx;
    while (1)
    {
        teleport *= 2;
        if (teleport < 0 || teleport >= MX)
            break;
        if (dist[teleport] != -1)
            break;
        dist[teleport] = dist[idx];
        Q.push(teleport);
    }
}

int main(void)
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    int n, k;
    cin >> n >> k;

    fill(dist, dist + MX, -1);

    Q.push(n);
    dist[n] = 0;
    teleport(n);    //시작할 때 가장 처음에 큐에 다 집어넣고 시작
    while (!Q.empty())
    {
        int cur = Q.front();
        Q.pop();
        for (int dir = 0; dir < 2; ++dir)
        {
            int nx = cur;
            if (dir == 0)
                nx += 1;
            else
                nx -= 1;

            if (nx < 0 || nx >= MX)
                continue;
            if (dist[nx] != -1)
                continue;
            
            dist[nx] = dist[cur] + 1;
            Q.push(nx);
            teleport(nx);   // 앞뒤로 한칸 움직일 때마다 배수를 전부 다 집어넣어줌
        }

        if (dist[k] != -1)
        {
            cout << dist[k] << '\n';
            return 0;
        }
    }
}
{% endhighlight %}
