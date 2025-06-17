---
layout: single
title: 백준 15666번 - N과 M (12)
categories: algorithmProblems
tags: [algorithm, backtracking]
toc: true
toc_sticky: true
---

## 문제
[백준 15666번 문제](https://www.acmicpc.net/problem/15666)

N개의 자연수와 자연수 M이 주어졌을 때, 아래 조건을 만족하는 길이가 M인 수열을 모두 구하는 프로그램을 작성하시오.

- N개의 자연수 중에서 M개를 고른 수열
- 같은 수를 여러 번 골라도 된다.
- 고른 수열은 비내림차순이어야 한다.
    - 길이가 K인 수열 A가 A1 ≤ A2 ≤ ... ≤ AK-1 ≤ AK를 만족하면, 비내림차순이라고 한다.

## 입력

첫째 줄에 N과 M이 주어진다. (1 ≤ M ≤ N ≤ 8)

둘째 줄에 N개의 수가 주어진다. 입력으로 주어지는 수는 10,000보다 작거나 같은 자연수이다.

## 출력

한 줄에 하나씩 문제의 조건을 만족하는 수열을 출력한다. 중복되는 수열을 여러 번 출력하면 안되며, 각 수열은 공백으로 구분해서 출력해야 한다.

수열은 사전 순으로 증가하는 순서로 출력해야 한다.

## 예제 입력 1 복사

3 1
4 4 2

## 예제 출력 1 복사
```
2
4
```

## 예제 입력 2 복사

4 2
9 7 9 1

## 예제 출력 2 복사
```
1 1
1 7
1 9
7 7
7 9
9 9
```

## 예제 입력 3 복사

4 4
1 1 2 2

## 예제 출력 3 복사
```
1 1 1 1
1 1 1 2
1 1 2 2
1 2 2 2
2 2 2 2
```

## 풀이
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int val[10];
int arr[10];
int n, m;

//수열을 오름차순으로 표시하기 위해 인자값 추가
void BackTracking(int lenght, int idx)
{
	if (lenght == m)
	{
		for (int i = 0; i < m; i++)
			cout << arr[i] << " ";
		cout << "\n";
		return;
	}

	int temp = 0;	//중복된 수열 생략
	for (int i = idx; i < n; i++)
	{
		if(temp == val[i])
			continue;
		//같은 수를 또 고를 수 있으므로 isUsed 없음
		arr[lenght] = val[i];
		temp = arr[lenght];
		BackTracking(lenght + 1, i);
	}
}

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n >> m;
	for (int i = 0; i < n; i++)
		cin >> val[i];
	sort(val, val + n);

	BackTracking(0, 0);
}
{% endhighlight %}
