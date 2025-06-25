---
layout: single
title: 백준 10844번 - 쉬운 계단 수
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
---

## 문제
[백준 10844번 문제](https://www.acmicpc.net/problem/10844) <br>
45656이란 수를 보자.

이 수는 인접한 모든 자리의 차이가 1이다. 이런 수를 계단 수라고 한다.

N이 주어질 때, 길이가 N인 계단 수가 총 몇 개 있는지 구해보자. 0으로 시작하는 수는 계단수가 아니다.

## 입력

첫째 줄에 N이 주어진다. N은 1보다 크거나 같고, 100보다 작거나 같은 자연수이다.

## 출력

첫째 줄에 정답을 1,000,000,000으로 나눈 나머지를 출력한다.

## 예제 입력 1 복사

```
1
```

## 예제 출력 1 복사

```
9
```

## 예제 입력 2 복사

```
2
```

## 예제 출력 2 복사

```
17
```

## 풀이
<div class="notice" markdown="1">
**<u>2차원 배열로 생각을 한다면 쉽게 접근 가능</u>**
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

//[n][k]
//n : 자리수
//k : 0~9까지의 숫자를 담는 배열
//테이블정의 : n자리수에서 k 숫자일때의 가능한 수
long long d[101][10];

int main() 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int n;
	cin >> n;

	//초기값
	for (int i = 1; i <= 9; ++i)
		d[1][i] = 1;

	//테이블 채우기
	for (int i = 2; i <= n; ++i) 
	{
		for (int k = 0; k <= 9; ++k) 
		{
			//계단 수는 이전 수에 1차이 나는 것이 법칙이기 때문에 +1, -1의 경우를 따져야 함
			if (k != 0)	//-1의 경우 : 0을 제외하고 다른 숫자는 가능
				d[i][k] += d[i - 1][k - 1];	//점화식 : 예를들어 3일 경우 다음 숫자에 2가 오는 경우
			if (k != 9) //+1의 경우 : 9를 제외하고 다른 숫자는 가능
				d[i][k] += d[i - 1][k + 1]; //점화식 : 예를들어 3일 경우 다음 숫자에 4가 오는 경우
			d[i][k] %= 1000000000;
		}
	}
	
	long long ans = 0;
	for (int i = 0; i <= 9; ++i) //0~9까지의 숫자를 다 더한 것이 n에 관한 계단 수
		ans += d[n][i];

	ans %= 1000000000;
	cout << ans;
}
{% endhighlight %}