---
layout: single
title: 백준 9655번 - 돌 게임
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
---

## 문제
[백준 9655번 문제](https://www.acmicpc.net/problem/9655) <br>
돌 게임은 두 명이서 즐기는 재밌는 게임이다.

탁자 위에 돌 N개가 있다. 상근이와 창영이는 턴을 번갈아가면서 돌을 가져가며, 돌은 1개 또는 3개 가져갈 수 있다. 마지막 돌을 가져가는 사람이 게임을 이기게 된다.

두 사람이 완벽하게 게임을 했을 때, 이기는 사람을 구하는 프로그램을 작성하시오. 게임은 상근이가 먼저 시작한다.

## 입력

첫째 줄에 N이 주어진다. (1 ≤ N ≤ 1000)

## 출력

상근이가 게임을 이기면 SK를, 창영이가 게임을 이기면 CY을 출력한다.

## 예제 입력 1 복사

```
5
```

## 예제 출력 1 복사

```
SK
```

## 풀이
<div class="notice--info" markdown="1">
**<u>직접 계산해보면 N이 홀수일때는 SK이고 짝수일때는 CY가 이긴다.</u>**
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int n;
	cin >> n;

	if (n % 2 != 0)
		cout << "SK";
	else
		cout << "CY";
}
{% endhighlight %}

## 다른풀이
<div class="notice--info" markdown="1">
**<u>이 문제는 DP로도 접근할 수 있다.</u>** <br>
![KakaoTalk_20240108_100906790.jpg](/assets/images/algorithm/KakaoTalk_20240108_100906790.jpg) <br>
1개 또는 3개를 가져갔을 때 나머지 결과를 DP를 이용해서 빠르게 구할 수 있다.
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int dp[1005];	//dp[i] 가 1일 경우 상근이 승, 0일 경우 창영이 승

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int n;
	cin >> n;

	dp[1] = 1;	//첫 사람은 무조건 1개를 집을 수 있고 상근이부터 시작이므로 상근이가 무조건 이김
	dp[2] = 0;	//첫 사람이 2개를 집을 수 없고 1개만 집을 수 있으므로 두번째 사람이 무조건 이김
	dp[3] = 1;	//첫 사람이 3개를 집을 수 있으므로 첫번째 사람이 이김
	for (int i = 4; i <= n; i++)	//i-1번째 또는 i-3번째를 이긴 사람이 지게 됨
		dp[i] = !((dp[i - 1] == 1) && (dp[i - 3] == 1));
	
	if (dp[n] == 1)
		cout << "SK";
	else
		cout << "CY";
}
{% endhighlight %}
