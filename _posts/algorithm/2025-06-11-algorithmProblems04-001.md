---
layout: single
title: 백준 3273번 - 두 수의 합
categories: algorithmProblems
tags: [algorithm, array]
toc: true
toc_sticky: true
---

<div class="notice--warning" markdown="1">
**<u>공간복잡도를 올리고 시간복잡도를 내리는 방법</u>** <br>
결과와 매칭되는 큰 배열을 추가로 선언해서 시간복잡도 O(n^2)에서 O(n)으로 변경
</div>

## 문제
[백준 3273번 문제](https://www.acmicpc.net/problem/3273)

n개의 서로 다른 양의 정수 a1, a2, ..., an으로 이루어진 수열이 있다. ai의 값은 1보다 크거나 같고, 1000000보다 작거나 같은 자연수이다. 자연수 x가 주어졌을 때, ai + aj = x (1 ≤ i < j ≤ n)을 만족하는 (ai, aj)쌍의 수를 구하는 프로그램을 작성하시오.
   
## 입력
   
첫째 줄에 수열의 크기 n이 주어진다. 다음 줄에는 수열에 포함되는 수가 주어진다. 셋째 줄에는 x가 주어진다. (1 ≤ n ≤ 100000, 1 ≤ x ≤ 2000000)
   
## 출력
   
문제의 조건을 만족하는 쌍의 개수를 출력한다.

## 예제 입력 1 복사
```
9
5 12 7 10 9 1 2 3 11
13
```

## 예제 출력 1 복사
```
3
```

# 풀이
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

int arr[100005] = { 0, };
int num[2000011] = { 0, };
int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int n, x;
	cin >> n;
	for (int i = 0; i < n; i++)
		cin >> arr[i];
	cin >> x;

	int result = 0;
	for (int i = 0; i < n; i++)
	{
		//들어오는 수열을 차례대로 입력받아서 해당 수를 더해 x를 만들 수 있는 인덱스에 값이 들어왔는지 검사
		//1. 두 수의 합이므로 x이상의 값은 볼 필요 없음
		//2. x(합) - arr[i](현재 수) 이 전에 들어왔으면 매칭되는 수가 존재
		if (x - arr[i] >= 0 && num[x - arr[i]] != 0)
			result++;
		num[arr[i]]++; //수열이 들어왔다면 해당 수 체크
	}

	cout << result;

}
{% endhighlight %}

![KakaoTalk_20230501_125211186.jpg](/assets/images/algorithm/KakaoTalk_20230501_125211186.jpg)
