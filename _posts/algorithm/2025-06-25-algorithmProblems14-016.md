---
layout: single
title: 백준 15686번 - 치킨배달
categories: algorithmProblems
tags: [algorithm, simulation]
toc: true
toc_sticky: true
---

## 문제
[백준 15686번 문제](https://www.acmicpc.net/problem/15686) <br>
크기가 N×N인 도시가 있다. 도시는 1×1크기의 칸으로 나누어져 있다. 도시의 각 칸은 빈 칸, 치킨집, 집 중 하나이다.

이 도시에 사는 사람들은 치킨을 매우 좋아한다. 따라서, 사람들은 "**치킨 거리**"라는 말을 주로 사용한다. **치킨 거리**는 집과 가장 가까운 치킨집 사이의 거리이다. 즉, 치킨 거리는 집을 기준으로 정해지며, 각각의 집은 **치킨 거리**를 가지고 있다. **도시의 치킨 거리**는 모든 집의 **치킨 거리**의 합이다.

임의의 두 칸 (r1, c1)과 (r2, c2) 사이의 거리는 |r1-r2| + |c1-c2|로 구한다.

프렌차이즈 본사에서는 수익을 증가시키기 위해 일부 치킨집을 폐업시키려고 한다. 오랜 연구 끝에 이 도시에서 가장 수익을 많이 낼 수 있는  치킨집의 개수는 최대 M개라는 사실을 알아내었다.

도시에 있는 치킨집 중에서 최대 M개를 고르고, 나머지 치킨집은 모두 폐업시켜야 한다. 어떻게 고르면, **도시의 치킨 거리**가 가장 작게 될지 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 N(2 ≤ N ≤ 50)과 M(1 ≤ M ≤ 13)이 주어진다.

둘째 줄부터 N개의 줄에는 도시의 정보가 주어진다.

도시의 정보는 0, 1, 2로 이루어져 있고, 0은 빈 칸, 1은 집, 2는 치킨집을 의미한다. 집의 개수는 2N개를 넘지 않으며, 적어도 1개는 존재한다. 치킨집의 개수는 M보다 크거나 같고, 13보다 작거나 같다.

## 출력

첫째 줄에 폐업시키지 않을 치킨집을 최대 M개를 골랐을 때, 도시의 치킨 거리의 최솟값을 출력한다.

## 예제 입력 1 복사
```
5 3
0 0 1 0 0
0 0 2 0 1
0 1 2 0 0
0 0 1 0 0
0 0 0 0 2
```

## 예제 출력 1 복사

5

## 풀이

<div class="notice--info" markdown="1">
**<u>최소거리는 치킨집이 많을 수록 줄어든다</u>** <br>
도시의 있는 치킨집 중에서 최대 M개를 구하라고 했지만 치킨집이 많을수록 도시의 치킨거리는 줄어듦 <br>
그렇기 때문에 시작부터 그냥 M개의 치킨집을 고르고 시작하는 것이 포인트
</div>

<div class="notice--info" markdown="1">
**<u>문제의 2가지 포인트</u>** <br>
1. 폐업시키지 않을 치킨집을 뽑기 <br>
	최대 13개 중에서 M개의 치킨집 순서 상관없이 뽑는다? -> 순열과 조합 즉, 백트레킹으로 접근가능 <br>
2. 각 집에서 각 치킨집과의 최소거리를 구해서 해당 순열에서 도시치킨거리를 구하기
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

#define X first
#define Y second

int board[52][52];
vector<pair<int, int>> house;
vector<pair<int, int>> chickenStore;
int n, m;

int main(void)
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    cin >> n >> m;
    for (int i = 0; i < n; i++)
    {
        for (int j = 0; j < n; j++)
        {
            cin >> board[i][j];
            if (board[i][j] == 1)
                house.push_back({ i, j });
            if(board[i][j] == 2)
                chickenStore.push_back({ i, j });
        }
    }

    //vector에 값을 1로 초기화 후
    //폐업할 치킨집을 0 아닌 치킨집을 1로 정한 수열 생성
    //0과 1밖에 없으니 순열과 조합 중에서 조합이고 시간복잡도는 13C6 이다
    vector<int> brute(chickenStore.size(), 1);
    fill(brute.begin(), brute.begin() + chickenStore.size() - m, 0);

    int cityDist = 0x7f7f7f7f;
    do
    {
        int curCityDist = 0;
        for (auto home : house) //집에서 각 치킨집과의 치킨거리 중 최소값
        {
            int dist = 0x7f7f7f7f;
            for (int i = 0; i < brute.size(); i++)
            {
                if (brute[i] == 0)
                    continue;

                //집에서 각 치킨가게까지의 거리구함
                dist = min(dist, abs(chickenStore[i].X - home.X) + abs(chickenStore[i].Y - home.Y));
            }
            curCityDist += dist; //해당 순열의 도시치킨거리
        }

        cityDist = min(cityDist, curCityDist); //모든 순열의 도시치킨거리 중 최소값
    } while (next_permutation(brute.begin(), brute.end()));

    cout << cityDist;
}
{% endhighlight %}