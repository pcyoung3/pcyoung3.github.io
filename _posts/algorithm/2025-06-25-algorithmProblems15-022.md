---
layout: single
title: 백준 1788번 - 피보나치 수의 확장
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
use_math: true
---

## 문제
[백준 1788번 문제](https://www.acmicpc.net/problem/1788) <br>

<div>
$$F(n) := \begin{cases}0 & \text{if }n = 0\text{;} \\ 1 & \text{if }n = 1\text{;} \\ F(n-1) + F(n-2) & \text{if }n > 1\text{.} \end{cases}$$
</div>

수학에서, 피보나치 수는 위의 점화식과 같이 귀납적으로 정의되는 수열이다. 위의 식에서도 알 수 있듯이, 피보나치 수 F(n)은 0 이상의 n에 대해서만 정의된다.

하지만 피보나치 수 F(n)을 n이 음수인 경우로도 확장시킬 수 있다. 위의 식에서 n > 1인 경우에만 성립하는 F(n) = F(n-1) + F(n-2)를 n ≤ 1일 때도 성립되도록 정의하는 것이다. 예를 들어 n = 1일 때 F(1) = F(0) + F(-1)이 성립되어야 하므로, F(-1)은 1이 되어야 한다.

n이 주어졌을 때, 피보나치 수 F(n)을 구하는 프로그램을 작성하시오. n은 음수로 주어질 수도 있다.

## 입력

첫째 줄에 n이 주어진다. n은 절댓값이 1,000,000을 넘지 않는 정수이다.

## 출력

첫째 줄에 F(n)이 양수이면 1, 0이면 0, 음수이면 -1을 출력한다. 둘째 줄에는 F(n)의 절댓값을 출력한다. 이 수가 충분히 커질 수 있으므로, 절댓값을 1,000,000,000으로 나눈 나머지를 출력한다.

## 예제 입력 1 복사

```
-2
```

## 예제 출력 1 복사

```
-1
1
```

## 예제 입력 2 복사

```
0
```

## 예제 출력 2 복사

```
0
0
```

## 예제 입력 3 복사

```
10
```

## 예제 출력 3 복사

```
1
55
```

## 예제 입력 4 복사

```
-7
```

## 예제 출력 4 복사

```
1
13
```

## 풀이
<div class="notice--info" markdown="1">
**<u>음수의 피보나치 수를 계산해보면 법칙을 알 수 있다</u>** <br>
![KakaoTalk_20231223_110154749.jpg](/assets/images/algorithm/KakaoTalk_20231223_110154749.jpg) <br>
음수로 내려갈 경우 양수의 피보나치 수열과 절대값은 똑같지만 짝수항만 음수인 것을 알 수 있다
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

using ll = long long;
ll d[1000003];
int n;

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n;
	int absN = abs(n);

	d[0] = 0;
	d[1] = 1;
	d[2] = 1;
	for (int i = 3; i <= absN; i++)
		d[i] = (d[i - 2] + d[i - 1]) % 1000000000ll;

	if (n >= 0)
	{
		if (d[absN] == 0)
			cout << 0 << "\n";
		else
			cout << 1 << "\n";
		cout << d[absN];
	}
	else
	{
		if (n % 2 == 0)
			cout << -1 << "\n";
		else
			cout << 1 << "\n";
		cout << d[absN];
	}
}
{% endhighlight %}