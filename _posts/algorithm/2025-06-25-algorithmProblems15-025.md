---
layout: single
title: 백준 1915번 - 가장 큰 정사각형
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
---

## 문제
[백준 1915번 문제](https://www.acmicpc.net/problem/1915) <br>
n×m의 0, 1로 된 배열이 있다. 이 배열에서 1로 된 가장 큰 정사각형의 크기를 구하는 프로그램을 작성하시오.

|   |   |   |   |
|---|---|---|---|
|0|1|0|0|
|0|1|1|1|
|1|1|1|0|
|0|0|1|0|

위와 같은 예제에서는 가운데의 2×2 배열이 가장 큰 정사각형이다.

## 입력

첫째 줄에 n, m(1 ≤ n, m ≤ 1,000)이 주어진다. 다음 n개의 줄에는 m개의 숫자로 배열이 주어진다.

## 출력

첫째 줄에 가장 큰 정사각형의 넓이를 출력한다.

## 예제 입력 1 복사

```
4 4
0100
0111
1110
0010
```

## 예제 출력 1 복사

```
4
```

## 풀이
<div class="notice--info" markdown="1">
**<u>정사각형을 어떻게 판별하느냐가 관건</u>** <br>
우선 DP 문제임을 파악해야 한다. <br>

1. 처음에 정사각형의 넓이가 정답이므로 BFS라고 가정했을 때는 정사각형임을 알 수 있는 방법이 없기 때문에 안된다. <br>
2. 쿼드트리라고 생각했지만 들어오는 n과 m의 값이 2의 배수로 들어오지 않는다는 점, 그리고 직사각형도 가능한다는 점 때문에 쿼드트리도 아니다. <br>

그렇다면 남은건 DP이므로 DP로 풀어야 된다는 것을 알 수 있다. <br>

또 다른 문제는 정사각형을 판별하고, 넓이를 구하는 방법인데 해설은 다음과 같다. <br>
![KakaoTalk_20240105_115411671.jpg](/assets/images/algorithm/KakaoTalk_20240105_115411671.jpg)
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

const int MX = 1005;
char arr[MX][MX];
int d[MX][MX];	//d[i][j] : (i, j)를 포함한 정사각형 크기
int n, m;

int main(void)
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n >> m;

	for (int i = 1; i <= n; i++)
		for (int j = 1; j <= m; j++)
			cin >> arr[i][j];

	d[1][1] = arr[1][1];
	for (int i = 1; i <= n; i++)
	{
		for (int j = 1; j <= m; j++)
		{
			if (arr[i][j] == '1')
			{
				if (d[i][j - 1] && d[i - 1][j] && d[i - 1][j - 1])	//왼쪽 위쪽 왼쪽대각선이 모두 0이 아닐때
					d[i][j] = min({ d[i][j - 1], d[i - 1][j], d[i - 1][j - 1] }) + 1;
				else	//왼쪽, 위쪽, 왼쪽대각선 중 하나 이상이 0일때
					d[i][j] = 1;
			}
			else	//현재 자리가 0이면 정사각형이 이뤄질 수 없음
				d[i][j] = 0;
		}
	}

	int MXWidth = 0;
	for (int i = 1; i <= n; i++) //2차원 배열 max_element로 최댓값 구하기
		MXWidth = max(MXWidth, *max_element(d[i], d[i] + m+1));
	cout << MXWidth * MXWidth;
}
{% endhighlight %}

## 다른풀이
<div class="notice--success" markdown="1">
**<u>거의 비슷한데 코드를 좀 더 간소화 할 수 있다</u>**
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int n, m;
int board[1010][1010], d[1010][1010];

int main(void)
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n >> m;
	for (int i = 1; i <= n; ++i)
	{
		string s;
		cin >> s;

		for (int j = 1; j <= m; ++j)
			board[i][j] = s[j - 1] - '0';
	}

	int ans = 0;
	for (int i = 1; i <= n; ++i)
	{
		for (int j = 1; j <= m; ++j)
		{
			if (board[i][j])
			{
				d[i][j] = min({ d[i - 1][j], d[i][j - 1], d[i - 1][j - 1] }) + 1;
				ans = max(ans, d[i][j]);
			}
		}
	}

	cout << ans * ans;
}
{% endhighlight %}