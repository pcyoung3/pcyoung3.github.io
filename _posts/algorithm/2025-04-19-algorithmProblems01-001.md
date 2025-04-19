---
layout: single
title: 배열_백준3273 두 수의 합
categories: algorithmProblems
tag: [algorithm, array]

toc: true
toc_sticky: true
---
<div class="notice--warning" markdown="1">
**<u>공간복잡도를 올리고 시간복잡도를 내리는 방법</u>** 

결과와 매칭되는 큰 배열을 추가로 선언해서 시간복잡도 O(n^2)에서 O(n)으로 변경
</div>


## 문제
https://www.acmicpc.net/problem/3273

---

## 풀이
```cpp
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
```
   

![Image](https://github.com/user-attachments/assets/66b484f4-12d3-4962-8ca8-5a98f22049ed)

