---
layout: single
title: 백준 1149번 - RGB거리
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
---

## 문제
[백준 1149번 문제](https://www.acmicpc.net/problem/1149) <br>
RGB거리에는 집이 N개 있다. 거리는 선분으로 나타낼 수 있고, 1번 집부터 N번 집이 순서대로 있다.

집은 빨강, 초록, 파랑 중 하나의 색으로 칠해야 한다. 각각의 집을 빨강, 초록, 파랑으로 칠하는 비용이 주어졌을 때, 아래 규칙을 만족하면서 모든 집을 칠하는 비용의 최솟값을 구해보자.

- 1번 집의 색은 2번 집의 색과 같지 않아야 한다.
- N번 집의 색은 N-1번 집의 색과 같지 않아야 한다.
- i(2 ≤ i ≤ N-1)번 집의 색은 i-1번, i+1번 집의 색과 같지 않아야 한다.

## 입력

첫째 줄에 집의 수 N(2 ≤ N ≤ 1,000)이 주어진다. 둘째 줄부터 N개의 줄에는 각 집을 빨강, 초록, 파랑으로 칠하는 비용이 1번 집부터 한 줄에 하나씩 주어진다. 집을 칠하는 비용은 1,000보다 작거나 같은 자연수이다.

## 출력

첫째 줄에 모든 집을 칠하는 비용의 최솟값을 출력한다.

## 예제 입력 1 복사

```
3
26 40 83
49 60 57
13 89 99
```

## 예제 출력 1 복사

```
96
```

## 예제 입력 2 복사

```
3
1 100 100
100 1 100
100 100 1
```

## 예제 출력 2 복사

```
3
```

## 예제 입력 3 복사

```
3
1 100 100
100 100 100
1 100 100
```

## 예제 출력 3 복사

```
102
```

## 예제 입력 4 복사

```
6
30 19 5
64 77 64
15 19 97
4 71 57
90 86 84
93 32 91
```

## 예제 출력 4 복사

```
208
```

## 예제 입력 5 복사

```
8
71 39 44
32 83 55
51 37 63
89 29 100
83 58 11
65 13 15
47 25 29
60 66 19
```

## 예제 출력 5 복사

```
253
```

## 풀이
<div class="notice--info" markdown="1">
**<u>백트레킹으로 접근하기에는 시간복잡도가 안되기 때문에 DP 문제</u>** <br>
1. 테이블 정의 <br>
	* D\[i] => i번째 집까지 칠할 때 비용의 최솟값으로 정의할 경우 같은 색이 중복되면 안된다는 조건을 충족하는 점화식을 찾기가 매우 어려워짐 <br>
	* 그래서 다음과 같이 2차원 배열로 처리 <br>
		D\[i]\[0] => i번째 집까지 칠할 때 비용의 최솟값, 단 i번째 집은 빨강 <br>
		D\[i]\[1] => i번째 집까지 칠할 때 비용의 최솟값, 단 i번째 집은 초록 <br>
		D\[i]\[2] => i번째 집까지 칠할 때 비용의 최솟값, 단 i번째 집은 파랑 <br>
		
2. 점화식 찾기 <br>
	앞에 같은 색깔이 있으면 칠할 수 없기 때문에 K번째 집을 칠할 때 같은 색을 제외한 다른 색 중 최소비용을 고려해야 함 <br>
	또한 k번째 집의 최소비용이 R,G,B 중 어떤 것일지 모르기 때문에 3개의 색칠 비용을 다 구해야 됨 <br>
	* `D[k][0] = min(D[k-1][1], D[k-1][2] +R[k]` <br>
	* `D[k][1] = min(D[k-1][0], D[k-1][2] +G[k]` <br>
	* `D[k][2] = min(D[k-1][0], D[k-1][1] +B[k]` <br>
	
3. 초기값 채우기 <br>
	점화식에 k-1까지 밖에 없기 때문에 가장 처음집의 3가지 색깔 비용만 채워주면 됨 <br>
	* `D[1][0] = R[1]` <br>
	* `D[1][1] = G[1]` <br>
	* `D[1][2] = B[1]`
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int house[1005][3];
int dp[1005][3];

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int n;
	cin >> n;
	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < 3; j++)
			cin >> house[i][j];
	}

	dp[0][0] = house[0][0];
	dp[0][1] = house[0][1];
	dp[0][2] = house[0][2];
	for (int i = 1; i < n; i++)
	{
		dp[i][0] = min(dp[i - 1][1], dp[i - 1][2]) + house[i][0];
		dp[i][1] = min(dp[i - 1][0], dp[i - 1][2]) + house[i][1];
		dp[i][2] = min(dp[i - 1][0], dp[i - 1][1]) + house[i][2];
	}

	cout << min({ dp[n - 1][0], dp[n - 1][1], dp[n - 1][2] });

	return 0;
}
{% endhighlight %}

## 비슷한풀이
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int d[1005][3];
int r[1005], g[1005], b[1005];	//색깔배열을 3개로 분리

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int n;
	cin >> n;

	for (int i = 1; i <= n; i++) 
		cin >> r[i] >> g[i] >> b[i];

	d[1][0] = r[1];
	d[1][1] = g[1];
	d[1][2] = b[1];

	for (int i = 2; i <= n; i++) 
	{
		d[i][0] = min(d[i - 1][1], d[i - 1][2]) + r[i];
		d[i][1] = min(d[i - 1][0], d[i - 1][2]) + g[i];
		d[i][2] = min(d[i - 1][0], d[i - 1][1]) + b[i];
	}

	cout << *min_element(d[n], d[n] + 3);
}
{% endhighlight %}