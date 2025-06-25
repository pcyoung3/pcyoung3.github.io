---
layout: single
title: 백준 14500번 - 테트로미노
categories: algorithmProblems
tags: [algorithm, simulation]
toc: true
toc_sticky: true
---

## 문제
[백준 14500번 문제](https://www.acmicpc.net/problem/14500) <br>

폴리오미노란 크기가 1×1인 정사각형을 여러 개 이어서 붙인 도형이며, 다음과 같은 조건을 만족해야 한다.

- 정사각형은 서로 겹치면 안 된다.
- 도형은 모두 연결되어 있어야 한다.
- 정사각형의 변끼리 연결되어 있어야 한다. 즉, 꼭짓점과 꼭짓점만 맞닿아 있으면 안 된다.

정사각형 4개를 이어 붙인 폴리오미노는 테트로미노라고 하며, 다음과 같은 5가지가 있다.
![14500_p_1.png](/assets/images/algorithm/14500_p_1.png) <br>
아름이는 크기가 N×M인 종이 위에 테트로미노 하나를 놓으려고 한다. 종이는 1×1 크기의 칸으로 나누어져 있으며, 각각의 칸에는 정수가 하나 쓰여 있다.

테트로미노 하나를 적절히 놓아서 테트로미노가 놓인 칸에 쓰여 있는 수들의 합을 최대로 하는 프로그램을 작성하시오.

테트로미노는 반드시 한 정사각형이 정확히 하나의 칸을 포함하도록 놓아야 하며, 회전이나 대칭을 시켜도 된다.

## 입력

첫째 줄에 종이의 세로 크기 N과 가로 크기 M이 주어진다. (4 ≤ N, M ≤ 500)

둘째 줄부터 N개의 줄에 종이에 쓰여 있는 수가 주어진다. i번째 줄의 j번째 수는 위에서부터 i번째 칸, 왼쪽에서부터 j번째 칸에 쓰여 있는 수이다. 입력으로 주어지는 수는 1,000을 넘지 않는 자연수이다.

## 출력

첫째 줄에 테트로미노가 놓인 칸에 쓰인 수들의 합의 최댓값을 출력한다.

## 예제 입력 1 복사

```
5 5
1 2 3 4 5
5 4 3 2 1
2 3 4 5 6
6 5 4 3 2
1 2 1 2 1
```

## 예제 출력 1 복사

```
19
```

## 풀이

<div class="notice--warning" markdown="1">
**<u>모든 회전과 대칭을 전부 기록하고 전위순회 때려버리는 방식</u>**
</div>

{% highlight cpp linenos%}
{% raw %}
#include <bits/stdc++.h>
using namespace std;

#define X first
#define Y second

// 테트로미노의 모든 조합 - 회전, 대칭 전부 포함
pair<int, int> tetro[19][4] = 
{
	// 청록색 블록
	{{0, 0}, {0, 1}, {0, 2}, {0, 3}},
	{{0, 0}, {1, 0}, {2, 0}, {3, 0}},
	// 노랑색 블록
	{{0, 0}, {1, 0}, {0, 1}, {1, 1}},
	// 주황색 블록
	{{0, 0}, {0, 1}, {0, 2}, {1, 2}},
	{{0, 0}, {0, 1}, {0, 2}, {1, 0}},
	{{1, 0}, {1, 1}, {1, 2}, {0, 2}},
	{{1, 0}, {1, 1}, {1, 2}, {0, 0}},

	{{0, 1}, {1, 1}, {2, 1}, {0, 0}},
	{{0, 1}, {1, 1}, {2, 1}, {2, 0}},
	{{0, 0}, {1, 0}, {2, 0}, {0, 1}},
	{{0, 0}, {1, 0}, {2, 0}, {2, 1}},
	// 초록색 블록
	{{0, 0}, {0, 1}, {1, 1}, {1, 2}},
	{{1, 0}, {1, 1}, {0, 1}, {0, 2}},
	{{0, 0}, {1, 0}, {1, 1}, {2, 1}},
	{{0, 1}, {1, 1}, {1, 0}, {2, 0}},
	// 자주색 블록
	{{0, 0}, {0, 1}, {0, 2}, {1, 1}},
	{{1, 0}, {1, 1}, {1, 2}, {0, 1}},
	{{0, 0}, {1, 0}, {2, 0}, {1, 1}},
	{{0, 1}, {1, 1}, {2, 1}, {1, 0}} 
};

int board[505][505];
int n, m;

int bruteforce(int x, int y) 
{
	int mx = 0;
	for (int i = 0; i < 19; i++)	//모든 블록 탐색
	{
		int sum = 0;
		for (int j = 0; j < 4; j++)
			sum += board[x + tetro[i][j].X][y + tetro[i][j].Y];
		mx = max(mx, sum);
	}
	return mx;
}

int main() 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n >> m;
	for (int i = 0; i < n; i++)
		for (int j = 0; j < m; j++)
			cin >> board[i][j];

	int ans = 0;
	//모든 칸 순회
	for (int i = 0; i < n; i++)
		for (int j = 0; j < m; j++)
			ans = max(ans, bruteforce(i, j));
	cout << ans;
}
{% endraw %}
{% endhighlight %}

## 다른풀이

<div class="notice--warning" markdown="1">
**<u>각 도형의 공통점을 찾아서 해당 특성으로 백트레킹을 이용하여 해결</u>** <br>
![KakaoTalk_20230819_125233074.jpg](/assets/images/algorithm/KakaoTalk_20230819_125233074.jpg) <br>
각 도형의 방문하는 깊이가 4인 것을 이용 <br>
다만 이 방법을 사용하면 도형중 하나는 깊이가 4로 완성할 수 없기 때문에 예외처리가 필요
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int n, m;
bool vis[502][502];
int board[502][502];

int dx[] = { 1, 0, -1, 0 };
int dy[] = { 0, 1, 0, -1 };

int mx, sum;

bool Oob(int x, int y);
void solve(int cx, int cy, int cnt);

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n >> m;
	for (int i = 0; i < n; i++)
		for (int j = 0; j < m; j++)
			cin >> board[i][j];

	//1칸씩 모든 도형 대입
	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < m; j++)
		{
			sum = board[i][j];	//해당 칸 숫자 덮어씀
			vis[i][j] = 1;	//해당 칸 방문표시
			solve(i, j, 1);
			vis[i][j] = 0;
		}
	}

	cout << mx << "\n";
}

bool Oob(int x, int y)
{
	return (x < 0 || x >= n || y < 0 || y >= m);
}

void solve(int cx, int cy, int cnt)
{
	if (cnt == 4)	//Depth가 4이면 완성된 도형
	{
		mx = max(mx, sum);
		return;
	}

	for (int dir = 0; dir < 4; dir++)
	{
		int nx = cx + dx[dir];
		int ny = cy + dy[dir];
		
		if(Oob(nx, ny))
			continue;
		if(vis[nx][ny])
			continue;

		sum += board[nx][ny];
		vis[nx][ny] = 1;
		solve(nx, ny, cnt + 1);
		if (cnt == 2)	//ㅗㅏㅜㅓ 모양 처리
			solve(cx, cy, cnt + 1);	//nx, ny가 아닌 cx, cy를 주어서 해당 자리에서 cnt만 1증가시켜 남은 칸을 탐색
		sum -= board[nx][ny];
		vis[nx][ny] = 0;
	}
}
{% endhighlight %}

<div class="notice--info" markdown="1">
**<u>ㅗㅏㅜㅓ 모양 처리 부분 참고</u>** <br>

![KakaoTalk_20230819_125303939.jpg](/assets/images/algorithm/KakaoTalk_20230819_125303939.jpg) <br>
같은 칸을 재방문 시키면 원하는 모양을 검사할 수 있다
</div>