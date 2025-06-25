---
layout: single
title: 백준 2193번 - 이친수
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
---

## 문제
[백준 2193번 문제](https://www.acmicpc.net/problem/2193) <br>
0과 1로만 이루어진 수를 이진수라 한다. 이러한 이진수 중 특별한 성질을 갖는 것들이 있는데, 이들을 이친수(pinary number)라 한다. 이친수는 다음의 성질을 만족한다.

1. 이친수는 0으로 시작하지 않는다.
2. 이친수에서는 1이 두 번 연속으로 나타나지 않는다. 즉, 11을 부분 문자열로 갖지 않는다.

예를 들면 1, 10, 100, 101, 1000, 1001 등이 이친수가 된다. 하지만 0010101이나 101101은 각각 1, 2번 규칙에 위배되므로 이친수가 아니다.

N(1 ≤ N ≤ 90)이 주어졌을 때, N자리 이친수의 개수를 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 N이 주어진다.

## 출력

첫째 줄에 N자리 이친수의 개수를 출력한다.

## 예제 입력 1 복사

```
3
```

## 예제 출력 1 복사

```
2
```

## 풀이
<div class="notice" markdown="1">
**<u>각 항을 살펴보면 +1의 항과 +(1*2)의 항에서 규칙을 찾을 수 있다</u>** <br>
![KakaoTalk_20231110_093334173.jpg](/assets/images/algorithm/KakaoTalk_20231110_093334173.jpg)
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

using ul = unsigned long;	//int 범위를 벗어남
ul dp[100];


int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int n;
	cin >> n;

	//초기값
	dp[1] = 1;
	dp[2] = 1;

	//점화식
	for (int i = 3; i <= n; i++)
		dp[i] = dp[i - 1] + dp[i - 2];

	cout << dp[n];
}
{% endhighlight %}

## 다른풀이
<div class="notice" markdown="1">
**<u>2차원 배열로 0일때와 1일때를 나눠서 해결가능</u>**
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

typedef long long ll;

int n;
ll d[100][2];

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n;
	d[1][1] = 1LL;
	for (int i = 2; i <= n; ++i) 
	{
		d[i][0] = d[i - 1][0] + d[i - 1][1];
		d[i][1] = d[i - 1][0];
	}

	cout << d[n][0] + d[n][1];
}
{% endhighlight %}