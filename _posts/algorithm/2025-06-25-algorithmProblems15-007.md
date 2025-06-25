---
layout: single
title: 백준 12852번 - 1로 만들기 2
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
use_math: true
---

## 문제
[백준 12852번 문제](https://www.acmicpc.net/problem/12852) <br>
정수 X에 사용할 수 있는 연산은 다음과 같이 세 가지 이다.

1. X가 3으로 나누어 떨어지면, 3으로 나눈다.
2. X가 2로 나누어 떨어지면, 2로 나눈다.
3. 1을 뺀다.

정수 N이 주어졌을 때, 위와 같은 연산 세 개를 적절히 사용해서 1을 만들려고 한다. 연산을 사용하는 횟수의 최솟값을 출력하시오.

## 입력

첫째 줄에 1보다 크거나 같고, $10^6$보다 작거나 같은 자연수 N이 주어진다.

## 출력

첫째 줄에 연산을 하는 횟수의 최솟값을 출력한다.

둘째 줄에는 N을 1로 만드는 방법에 포함되어 있는 수를 공백으로 구분해서 순서대로 출력한다. 정답이 여러 가지인 경우에는 아무거나 출력한다.

## 예제 입력 1 복사

```
2
```

## 예제 출력 1 복사

```
1
2 1
```

## 예제 입력 2 복사

```
10
```

## 예제 출력 2 복사

```
3
10 9 3 1
```

## 풀이
<div class="notice" markdown="1">
**<u>최소회수 연산 경로를 띄우기 위해서는 경로를 표시할 배열이 하나 더 필요</u>** <br>
dp를 점화식으로 계산할 때 현재 숫자의 dp를 구할 때 경로복원용 테이블의 값도 같이 구한다 <br>

경로 복원용 테이블은 연결리스트에서 사용한 야메연결리스트 처럼 인덱스를 저장 <br>
인덱스는 1부터 시작하는 문제에서 주어진 N을 표현 <br>
값은 해당 인덱스를 1로 만들기 위한 최적의 연산을 한 결과를 나타냄. 그대로 따라가면 최소연산의 경로가 표시됨 <br>
![Pasted image 20231104175155.png](/assets/images/algorithm/Pasted image 20231104175155.png)
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int dp[1000005];	//dp[i] -> i에서 1이 되려면 몇번의 연산이 필요한지
int pre[1000005];	//pre -> 현재 인덱스 숫자부터 1로 가기까지의 과정에 있는 숫자들을 저장
					//pre[i] -> i에서 1까지 최단거리로 가기 위한 다음 연산이 있는 인덱스

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int n;
	cin >> n;
	
	dp[1] = 0;
	pre[1] = 0;
	for (int i = 2; i <= n; i++)
	{
		//1을 뺄 경우
		dp[i] = dp[i - 1] + 1;
		pre[i] = i - 1;

		//2로 나눌 경우
		if (i % 2 == 0 && dp[i / 2] + 1 < dp[i])
		{
			dp[i] = dp[i / 2] + 1;
			pre[i] = i / 2;
		}

		//3으로 나눌 경우
		if (i % 3 == 0 && dp[i / 3] + 1 < dp[i])
		{
			dp[i] = dp[i / 3] + 1;
			pre[i] = i / 3;
		}
	}

	cout << dp[n] << "\n";

	int cur = n;
	while (1)
	{
		cout << cur << ' ';
		if(pre[cur] == 0)
			break;
		cur = pre[cur];	//연결리스트처럼 순회하면서 다음 인덱스를 찾음
	}
}
{% endhighlight %}