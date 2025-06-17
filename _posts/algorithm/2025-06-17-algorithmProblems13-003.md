---
layout: single
title: 백준 1182번 - 부분수열의 합
categories: algorithmProblems
tags: [algorithm, backtracking]
toc: true
toc_sticky: true
---

## 문제
[백준 1182번 문제](https://www.acmicpc.net/problem/1182)

N개의 정수로 이루어진 수열이 있을 때, 크기가 양수인 부분수열 중에서 그 수열의 원소를 다 더한 값이 S가 되는 경우의 수를 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 정수의 개수를 나타내는 N과 정수 S가 주어진다. (1 ≤ N ≤ 20, |S| ≤ 1,000,000) 둘째 줄에 N개의 정수가 빈 칸을 사이에 두고 주어진다. 주어지는 정수의 절댓값은 100,000을 넘지 않는다.

## 출력

첫째 줄에 합이 S가 되는 부분수열의 개수를 출력한다.

## 예제 입력 1 복사
```
5 0
-7 -3 -2 5 8
```

## 예제 출력 1 복사
```
1
```
   
### 3-1. 풀이 및 정답코드

![Pasted image 20230706150243.png](/assets/images/algorithm/Pasted image 20230706150243.png)

풀이는 어떤 주어진 숫자를 더할 것인지 뺄 것인지를 전부 순회하면 된다.
더한 것과 더하지 않는 부분을 재귀로 전부 태우는 형식으로 진행하며 
속칭 백트레킹의 가지치기(재귀를 타고 가다가 조건이 안맞아서 그 하위 부분은 실행되지 않는 것)는 되지 않는다
따라서 브루트포스(전위순회)로도 풀 수 있음
   
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int n, s;
int arr[30];
int cnt = 0;
void BackTracking(int cur, int tot)
{
	//탈출조건
	if (cur == n)	//더하거나 더하지 않은 원소들의 합이 입력값과 같음
	{
		if (tot == s) //원소들의 합이 s와 같을 경우
			cnt++;
		return;
	}

	BackTracking(cur + 1, tot);	//현재 원소를 더하지 않는 경우
	BackTracking(cur + 1, tot + arr[cur]);	//현재 원소를 더하는 경우
}

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n >> s;
	for (int i = 0; i < n; ++i)
		cin >> arr[i];
	BackTracking(0, 0);
	if (s == 0) //부분집합중에 공집합은 문제에서 포함하지 않으므로 하나 뺀다
		cnt--;
	cout << cnt;
}
{% endhighlight %}
