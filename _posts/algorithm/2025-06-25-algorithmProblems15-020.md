---
layout: single
title: 백준 15988번 - 1, 2, 3 더하기 3
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
---

## 문제
[백준 15988번 문제](https://www.acmicpc.net/problem/15988) <br>
정수 4를 1, 2, 3의 합으로 나타내는 방법은 총 7가지가 있다. 합을 나타낼 때는 수를 1개 이상 사용해야 한다.

- 1+1+1+1
- 1+1+2
- 1+2+1
- 2+1+1
- 2+2
- 1+3
- 3+1

정수 n이 주어졌을 때, n을 1, 2, 3의 합으로 나타내는 방법의 수를 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 테스트 케이스의 개수 T가 주어진다. 각 테스트 케이스는 한 줄로 이루어져 있고, 정수 n이 주어진다. n은 양수이며 1,000,000보다 작거나 같다.

## 출력

각 테스트 케이스마다, n을 1, 2, 3의 합으로 나타내는 방법의 수를 1,000,000,009로 나눈 나머지를 출력한다.

## 예제 입력 1 복사

```
3
4
7
10
```

## 예제 출력 1 복사

```
7
44
274
```

## 풀이
<div class="notice--primary" markdown="1">
**[1, 2, 3 더하기](/algorithmproblems/algorithmProblems15-002/) 참고**
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int t;
long long d[1000005];

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	d[1] = 1;
	d[2] = 2;
	d[3] = 4;
	//=3개 더하는 과정에서 int값을 벗어나는 경우가 있음
	for (int i = 4; i <= 1000002; i++)
		d[i] = (d[i - 1] + d[i - 2] + d[i - 3]) % 1000000009ll;

	int n;
	cin >> t;
	while (t--)
	{
		cin >> n;
		cout << d[n] << "\n";
	}
}
{% endhighlight %}

## 다른풀이
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int n;
int d[1000010];
int mod = 1e9 + 9;

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	d[1] = 1;
	d[2] = 2;
	d[3] = 4;
	for (int i = 4; i <= 1000000; ++i)
		for (int j = 1; j <= 3; ++j) //i-1, -2, -3 을 더하는 과정
			d[i] = (d[i] + d[i - j]) % mod;

	int t;
	cin >> t;
	while (t--)
	{
		cin >> n;
		cout << d[n] << '\n';
	}
}
{% endhighlight %}