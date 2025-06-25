---
layout: single
title: 백준 2240번 - 자두나무
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
---

## 문제
[백준 2240번 문제](https://www.acmicpc.net/problem/2240) <br>
자두는 자두를 좋아한다. 그래서 집에 자두나무를 심어두고, 여기서 열리는 자두를 먹고는 한다. 하지만 자두는 키가 작아서 자두를 따먹지는 못하고, 자두가 떨어질 때까지 기다린 다음에 떨어지는 자두를 받아서 먹고는 한다. 자두를 잡을 때에는 자두가 허공에 있을 때 잡아야 하는데, 이는 자두가 말랑말랑하여 바닥에 떨어지면 못 먹을 정도로 뭉개지기 때문이다.

매 초마다, 두 개의 나무 중 하나의 나무에서 열매가 떨어지게 된다. 만약 열매가 떨어지는 순간, 자두가 그 나무의 아래에 서 있으면 자두는 그 열매를 받아먹을 수 있다. 두 개의 나무는 그다지 멀리 떨어져 있지 않기 때문에, 자두는 하나의 나무 아래에 서 있다가 다른 나무 아래로 빠르게(1초보다 훨씬 짧은 시간에) 움직일 수 있다. 하지만 자두는 체력이 그다지 좋지 못해서 많이 움직일 수는 없다.

자두는 T(1≤T≤1,000)초 동안 떨어지게 된다. 자두는 최대 W(1≤W≤30)번만 움직이고 싶어 한다. 매 초마다 어느 나무에서 자두가 떨어질지에 대한 정보가 주어졌을 때, 자두가 받을 수 있는 자두의 개수를 구해내는 프로그램을 작성하시오. 자두는 1번 자두나무 아래에 위치해 있다고 한다.

## 입력

첫째 줄에 두 정수 T, W가 주어진다. 다음 T개의 줄에는 각 순간에 자두가 떨어지는 나무의 번호가 1 또는 2로 주어진다.

## 출력

첫째 줄에 자두가 받을 수 있는 자두의 최대 개수를 출력한다.

## 예제 입력 1 복사

```
7 2
2
1
1
2
2
1
1
```

## 예제 출력 1 복사

```
6
```

## 풀이
<div class="notice" markdown="1">
**<u>3차원 배열로 접근</u>** <br>
테이블을 `dp[t][w][l]`로 잡고 t는 시점, w는 이동횟수, l은 위치로 잡음 <br>
이 때 w값을 어떻게 채우는지가 고민인데 brute_force로 w로 전부 돌면서 채움
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int dp[1005][35][3];	//dp[t][w][l] : t시점에서 w번 이동한 상태, 위치는 l일 때 최대값
int arr[1005];

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int t, w;
	cin >> t >> w;
	for (int i = 1; i <= t; i++)
		cin >> arr[i];

	//최대 이동 개수 표시
	const int moveNum = w;

	//초기값 채우기
	if (arr[1] == 1)	//1번 나무에서 자두가 떨어진다면
		dp[1][0][1] = 1;	//1초때 0번움직이고 1번나무위치
	else if (moveNum > 0)	//2번 나무에서 자두가 떨어진다면
		dp[1][1][2] = 1;	//1초때 1번 움직이고 2번나무위치
	
	for (int i = 2; i <= t; i++)
	{
		for (int j = 0; j <= moveNum; j++)
		{
			if (arr[i] == 1)	//1번 나무에서 자두가 떨어진다면
			{
				//2번나무에서는 아무것도 안떨어지므로 1초전 값을 그대로 백업
				dp[i][j][2] = dp[i - 1][j][2];
				if (j > 0)	//Stack Underflow 방지
				{
					//움직이지 않고 자두를 먹는 경우, 움직이고 자두를 먹는 경우
					//움직이지 않을 경우 1번 나무 값에 + 1
					//움직일 경우 2번 나무에서 1번으로 오기 때문에 2번 값에 + 1
					dp[i][j][1] = max(dp[i - 1][j][1] + 1, dp[i - 1][j - 1][2] + 1);
				}
				else
				{
					//j == 0 -> 처음 움직임 -> 전회차가 없으므로 움직이지 않고 자두를 먹음
					dp[i][j][1] = dp[i - 1][j][1] + 1;
				}
			}
			else	//2번 나무에서 자두가 떨어진다면 - 나머지는 위와 동일
			{
				dp[i][j][1] = dp[i - 1][j][1];
				if (j > 0)
					dp[i][j][2] = max(dp[i - 1][j][2] + 1, dp[i - 1][j - 1][1] + 1);
				else
					dp[i][j][2] = dp[i - 1][j][2] + 1;
			}
		}
	}

	int ans = 0;
	for (int i = 0; i <= moveNum; i++)
	{
		int number = max(dp[t][i][1], dp[t][i][2]);
		ans = max(ans, number);
	}

	cout << ans;
}
{% endhighlight %}

## 다른풀이
<div class="notice" markdown="1">
**<u>내가 푼 방법과 비슷</u>**
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int a[1002];
int d[1002][32][3]; // d[i][j][k]: i:현재시간, j:이동횟수, k:나무번호

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int t, w;
	cin >> t >> w;
	for (int i = 1; i <= t; i++)
		cin >> a[i];

	for (int i = 1; i <= t; i++) 
	{
		// 이동하지 않는 경우 (2번 나무는 이동안하면 못감)
		d[i][0][1] = d[i - 1][0][1] + (a[i] == 1 ? 1 : 0);

		for (int j = 1; j <= w; j++)  // j번 이동
		{
			if (i < j) 
				break; // 현재 시간보다 많이 이동할 수 없다
			if (a[i] == 1) 
			{ // 1번 나무인 경우
				d[i][j][1] = max(d[i - 1][j - 1][2], d[i - 1][j][1]) + 1;
				d[i][j][2] = d[i - 1][j][2];
			}
			else 
			{ // 2번 나무인 경우
				d[i][j][1] = d[i - 1][j][1];
				d[i][j][2] = max(d[i - 1][j - 1][1], d[i - 1][j][2]) + 1;
			}
		}
	}

	int ans = 0;
	for (int j = 0; j <= w; j++)
		ans = max({ ans, d[t][j][1], d[t][j][2] });
	cout << ans;
}
{% endhighlight %}

## 다른풀이
<div class="notice" markdown="1">
**<u>2차원 배열로 해결하는 법</u>** <br>
자두는 가장 처음에 1번나무에 서있으므로 그 다음 이동은 무조건 2번 나무인 것을 이용해서 <br>
홀수번 이동하면 2번나무 짝수번 이동하면 1번나무로 해서 마지막 배열을 없앨 수 있음
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int a[1005];
int d[1005][35];  // d[t][w]에서 t는 자두가 떨어진 시간, w는 자두가 움직인 횟수

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int t, w;
	cin >> t >> w;
	for (int i = 1; i <= t; i++) 
		cin >> a[i];

	// 자두가 한 번도 움직이지 않았을 때
	int cnt = 0;
	for (int i = 1; i <= t; i++) 
	{
		if (a[i] == 1) 
			cnt++;
		d[i][0] = cnt;
	}

	for (int i = 1; i <= t; i++)
		for (int j = 1; j <= w; j++)
			// 자두가 움직인 횟수가 홀수일 경우 2번 나무, 짝수일 경우 1번 나무에 가게 된다.
			d[i][j] = max(d[i - 1][j - 1], d[i - 1][j]) + (1 + j % 2 == a[i]);

	cout << *max_element(d[t], d[t] + w + 1);
}
{% endhighlight %}

`(1 + j % 2 == a[i])`의 경우 `1 + j % 2 `이게 한 묶음으로 봐야 됨 <br>
`j % 2`는 j가 짝수인지 검사하는 것이며 거기다가 `+1`을 했기 때문에 <br>
원래는 0, 1이 나오는 것이 1, 2가 나오게 됨 <br>
근데 이게 홀수번 움직이면 2번 나무, 짝수번 움직이면 1번 나무가 나와서 결국 상황이 딱 맞음