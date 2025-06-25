---
layout: single
title: 백준 14002번 - 가장 긴 증가하는 부분 수열 4
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
---

## 문제
[백준 14002번 문제](https://www.acmicpc.net/problem/14002) <br>
수열 A가 주어졌을 때, 가장 긴 증가하는 부분 수열을 구하는 프로그램을 작성하시오.

예를 들어, 수열 A = {10, 20, 10, 30, 20, 50} 인 경우에 가장 긴 증가하는 부분 수열은 A = {**10**, **20**, 10, **30**, 20, **50**} 이고, 길이는 4이다.

## 입력

첫째 줄에 수열 A의 크기 N (1 ≤ N ≤ 1,000)이 주어진다.

둘째 줄에는 수열 A를 이루고 있는 Ai가 주어진다. (1 ≤ Ai ≤ 1,000)

## 출력

첫째 줄에 수열 A의 가장 긴 증가하는 부분 수열의 길이를 출력한다.

둘째 줄에는 가장 긴 증가하는 부분 수열을 출력한다. 그러한 수열이 여러가지인 경우 아무거나 출력한다.

## 예제 입력 1 복사

```
6
10 20 10 30 20 50
```

## 예제 출력 1 복사

```
4
10 20 30 50
```

## 풀이
<div class="notice" markdown="1">
**<u>이전 배열을 출력해야되는 업그레이드 유형 문제</u>** <br>
[가장 긴 증가하는 부분 수열](/algorithmproblems/algorithmProblems15-012/) 문제의 업그레이드 버전이며 <br>
[1로 만들기 2](/algorithmproblems/algorithmProblems15-007/) 문제에서 사용하는 개념을 합치면 됨
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int dp[1005];
int pre[1005];
int arr[1005];

int main(void)
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	
	int n;
	cin >> n;
	
	for (int i = 0; i < n; i++)
	{
		cin >> arr[i];
		dp[i] = 1;
	}

	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < i; j++)
		{
			if (arr[i] > arr[j])
			{
				if (dp[i] < dp[j] + 1)
				{
					dp[i] = dp[j] + 1;
					pre[i] = j;	//이전 인덱스를 저장
				}
			}
		}
	}
	
	int* ans = 0;
	ans = max_element(dp, dp + n);	//우선 최장길이를 뽑아냄
	cout << *ans << "\n";

	stack<int> st;	//역순으로 출력하기 위한 STL
	int index = ans - dp;	//포인터 연산으로 최장길이의 인덱스를 뽑아냄
	int beforeIndex = -1;
	while (true)
	{
		if (beforeIndex == index)	//같은 인덱스가 2번 반복되면 싸이클이므로 탈출
			break;

		st.push(arr[index]);
		beforeIndex = index;
		index = pre[index];
	}
	
	while (!st.empty())
	{
		cout << st.top() << " ";
		st.pop();
	}
}
{% endhighlight %}

## 다른풀이
<div class="notice--info" markdown="1">
**<u>출력값을 다른 방식으로 띄울 수 있다</u>**
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int n;
int a[1010], d[1010], pre[1010];

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n;
	for (int i = 1; i <= n; ++i) 
		cin >> a[i];
	fill(d, d + n, 1);

	for (int i = 1; i <= n; ++i) 
	{
		for (int j = 1; j < i; ++j) 
		{
			if (a[j] < a[i] && d[i] < d[j] + 1) 
			{
				d[i] = d[j] + 1;
				pre[i] = j;
			}
		}
	}

	int maxi = 1, maxd = d[1];
	for (int i = 2; i <= n; ++i)	//제일 긴 수열의 마지막 인덱스와 길이를 찾음
	{
		if (maxd < d[i]) 
		{
			maxi = i;
			maxd = d[i];
		}
	}

	deque<int> ans;	//출력용 덱
	int cur = maxi;
	while (cur) 
	{
		ans.push_front(a[cur]);	//덱에 가장 앞에 쌓기 때문에 마지막것부터 넣어도 뒤로 밀리게 됨
		cur = pre[cur];
	}
	cout << ans.size() << '\n';

	for (auto x : ans) 
		cout << x << ' ';
}
{% endhighlight %}