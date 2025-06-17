---
layout: single
title: 백준 15654번 - N과 M (5)
categories: algorithmProblems
tags: [algorithm, backtracking]
toc: true
toc_sticky: true
---

## 문제
[백준 15654번 문제](https://www.acmicpc.net/problem/15654)

N개의 자연수와 자연수 M이 주어졌을 때, 아래 조건을 만족하는 길이가 M인 수열을 모두 구하는 프로그램을 작성하시오. N개의 자연수는 모두 다른 수이다.

- N개의 자연수 중에서 M개를 고른 수열

## 입력

첫째 줄에 N과 M이 주어진다. (1 ≤ M ≤ N ≤ 8)

둘째 줄에 N개의 수가 주어진다. 입력으로 주어지는 수는 10,000보다 작거나 같은 자연수이다.

## 출력

한 줄에 하나씩 문제의 조건을 만족하는 수열을 출력한다. 중복되는 수열을 여러 번 출력하면 안되며, 각 수열은 공백으로 구분해서 출력해야 한다.

수열은 사전 순으로 증가하는 순서로 출력해야 한다.

## 예제 입력 1 복사

3 1
4 5 2

## 예제 출력 1 복사
```
2
4
5
```

## 예제 입력 2 복사

4 2
9 8 7 1

## 예제 출력 2 복사
```
1 7
1 8
1 9
7 1
7 8
7 9
8 1
8 7
8 9
9 1
9 7
9 8
```

## 예제 입력 3 복사

4 4
1231 1232 1233 1234

## 예제 출력 3 복사
```
1231 1232 1233 1234
1231 1232 1234 1233
1231 1233 1232 1234
1231 1233 1234 1232
1231 1234 1232 1233
1231 1234 1233 1232
1232 1231 1233 1234
1232 1231 1234 1233
1232 1233 1231 1234
1232 1233 1234 1231
1232 1234 1231 1233
1232 1234 1233 1231
1233 1231 1232 1234
1233 1231 1234 1232
1233 1232 1231 1234
1233 1232 1234 1231
1233 1234 1231 1232
1233 1234 1232 1231
1234 1231 1232 1233
1234 1231 1233 1232
1234 1232 1231 1233
1234 1232 1233 1231
1234 1233 1231 1232
1234 1233 1232 1231
```

## 풀이
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int n, m;
int val[10];
int arr[10];
bool isUsed[10];

void BackTracking(int length)
{
	if (length == m)
	{
		for (int i = 0; i < m; ++i)
			cout << arr[i] << " ";
		cout << "\n";
		return;
	}

	//val을 arr에 넣으면서 진행
	for (int i = 0; i < n; i++)
	{
		if (isUsed[i])
			continue;
		arr[length] = val[i];
		isUsed[i] = 1;
		BackTracking(length + 1);
		isUsed[i] = 0;
	}
}

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	
	cin >> n >> m;
	for (int i = 0; i < n; ++i)
		cin >> val[i];

	//오름차순으로 출력해야되기 때문에 입력값을 sorting
	sort(val, val + n, [](int a, int b)
		{
			return a < b;
		});

	BackTracking(0);
}
{% endhighlight %}
