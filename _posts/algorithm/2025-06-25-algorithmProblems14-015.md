---
layout: single
title: 백준 15685번 - 드래곤 커브
categories: algorithmProblems
tags: [algorithm, simulation]
toc: true
toc_sticky: true
---

## 문제
[백준 15685번 문제](https://www.acmicpc.net/problem/15685) <br>
드래곤 커브는 다음과 같은 세 가지 속성으로 이루어져 있으며, 이차원 좌표 평면 위에서 정의된다. 좌표 평면의 x축은 → 방향, y축은 ↓ 방향이다.

1. 시작 점
2. 시작 방향
3. 세대

0세대 드래곤 커브는 아래 그림과 같은 길이가 1인 선분이다. 1세대 드래곤 커브는 0세대 드래곤 커브를 끝 점을 기준으로 시계 방향으로 90도 회전시킨 다음 0세대 드래곤 커브의 끝 점에 붙인 것이다.

즉, K(K > 1)세대 드래곤 커브는 K-1세대 드래곤 커브를 끝 점을 기준으로 90도 시계 방향 회전 시킨 다음, 그것을 끝 점에 붙인 것이다.

크기가 100×100인 격자 위에 드래곤 커브가 N개 있다. 이때, 크기가 1×1인 정사각형의 네 꼭짓점이 모두 드래곤 커브의 일부인 정사각형의 개수를 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 드래곤 커브의 개수 N(1 ≤ N ≤ 20)이 주어진다. 둘째 줄부터 N개의 줄에는 드래곤 커브의 정보가 주어진다. 드래곤 커브의 정보는 네 정수 x, y, d, g로 이루어져 있다. x와 y는 드래곤 커브의 시작 점, d는 시작 방향, g는 세대이다. (0 ≤ x, y ≤ 100, 0 ≤ d ≤ 3, 0 ≤ g ≤ 10)

방향은 0, 1, 2, 3 중 하나이고, 다음을 의미한다.

- 0: x좌표가 증가하는 방향 (→)
- 1: y좌표가 감소하는 방향 (↑)
- 2: x좌표가 감소하는 방향 (←)
- 3: y좌표가 증가하는 방향 (↓)

## 출력

첫째 줄에 크기가 1×1인 정사각형의 네 꼭짓점이 모두 드래곤 커브의 일부인 것의 개수를 출력한다.

## 예제 입력 1 복사

```
3
3 3 0 1
4 2 1 3
4 2 2 1
```

## 예제 출력 1 복사

4

## 풀이

<div class="notice--danger" markdown="1">
**<u>좌표기준이 아닌 방향기준으로 접근해야 함</u>** <br>
![KakaoTalk_20230901_090611773.jpg](/assets/images/algorithm/KakaoTalk_20230901_090611773.jpg)
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int n, x, y, d, g;
int board[105][105];
int cnt = 0;

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n;
	while (n--)
	{
		cin >> x >> y >> d >> g;

		vector<int> v;
		v.push_back(d);
		board[y][x] = 1;

		while (g--)
		{
			int vSize = v.size();
			// 세대가 증가할 때마다 기존 세대의 방향(숫자)을 뒤에서부터 1을 더한 방향(숫자)을 추가
			// 0: (0)
			// 1: (0) (1)
			// 2: (0, 1) (2, 1)
			// 3: (0, 1, 2, 1) (2, 3, 2, 1)
			for (int j = vSize - 1; j >= 0; j--)
				v.push_back((v[j] + 1) % 4);
		}

		//정해진 방향대로 이동시킴
		for (int i = 0; i < v.size(); i++)
		{
			int dir = v[i];
			if (dir == 0)
				x++;
			else if (dir == 1)
				y--;
			else if (dir == 2)
				x--;
			else if (dir == 3)
				y++;

			board[y][x] = 1;
		}
	}

	for (int i = 0; i < 100; i++)
	{
		for (int j = 0; j < 100; j++)
		{
			//사각형이 몇개 있는지 판별
			//4개의 점이 true이면 사각형임
			if (board[i][j] && board[i + 1][j] && board[i][j + 1] && board[i + 1][j + 1])
				cnt++;
		}
	}
	cout << cnt;
}
{% endhighlight %}