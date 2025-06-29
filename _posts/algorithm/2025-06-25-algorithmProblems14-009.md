---
layout: single
title: 백준 14888번 - 연산자 끼워넣기
categories: algorithmProblems
tags: [algorithm, simulation]
toc: true
toc_sticky: true
---

## 문제
[백준 14888번 문제](https://www.acmicpc.net/problem/14888) <br>
N개의 수로 이루어진 수열 A1, A2, ..., AN이 주어진다. 또, 수와 수 사이에 끼워넣을 수 있는 N-1개의 연산자가 주어진다. 연산자는 덧셈(+), 뺄셈(-), 곱셈(×), 나눗셈(÷)으로만 이루어져 있다.

우리는 수와 수 사이에 연산자를 하나씩 넣어서, 수식을 하나 만들 수 있다. 이때, 주어진 수의 순서를 바꾸면 안 된다.

예를 들어, 6개의 수로 이루어진 수열이 1, 2, 3, 4, 5, 6이고, 주어진 연산자가 덧셈(+) 2개, 뺄셈(-) 1개, 곱셈(×) 1개, 나눗셈(÷) 1개인 경우에는 총 60가지의 식을 만들 수 있다. 예를 들어, 아래와 같은 식을 만들 수 있다.

- 1+2+3-4×5÷6
- 1÷2+3+4-5×6
- 1+2÷3×4-5+6
- 1÷2×3-4+5+6

식의 계산은 연산자 우선 순위를 무시하고 앞에서부터 진행해야 한다. 또, 나눗셈은 정수 나눗셈으로 몫만 취한다. 음수를 양수로 나눌 때는 C++14의 기준을 따른다. 즉, 양수로 바꾼 뒤 몫을 취하고, 그 몫을 음수로 바꾼 것과 같다. 이에 따라서, 위의 식 4개의 결과를 계산해보면 아래와 같다.

- 1+2+3-4×5÷6 = 1
- 1÷2+3+4-5×6 = 12
- 1+2÷3×4-5+6 = 5
- 1÷2×3-4+5+6 = 7

N개의 수와 N-1개의 연산자가 주어졌을 때, 만들 수 있는 식의 결과가 최대인 것과 최소인 것을 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 수의 개수 N(2 ≤ N ≤ 11)가 주어진다. 둘째 줄에는 A1, A2, ..., AN이 주어진다. (1 ≤ Ai ≤ 100) 셋째 줄에는 합이 N-1인 4개의 정수가 주어지는데, 차례대로 덧셈(+)의 개수, 뺄셈(-)의 개수, 곱셈(×)의 개수, 나눗셈(÷)의 개수이다.

## 출력

첫째 줄에 만들 수 있는 식의 결과의 최댓값을, 둘째 줄에는 최솟값을 출력한다. 연산자를 어떻게 끼워넣어도 항상 -10억보다 크거나 같고, 10억보다 작거나 같은 결과가 나오는 입력만 주어진다. 또한, 앞에서부터 계산했을 때, 중간에 계산되는 식의 결과도 항상 -10억보다 크거나 같고, 10억보다 작거나 같다.

## 예제 입력 1 복사

```
2
5 6
0 0 1 0
```
## 예제 출력 1 복사

30
30

## 예제 입력 2 복사

```
3
3 4 5
1 0 1 0
```

## 예제 출력 2 복사

35
17

## 예제 입력 3 복사

```
6
1 2 3 4 5 6
2 1 1 1
```

## 예제 출력 3 복사

54
-24

## 풀이

<div class="notice--info" markdown="1">
**<u>백트레킹을 이용해서 해결</u>**
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int sequence[102];
int operators[5];
int n;

int mx, mn;

void BackTracking(int number, int cnt);

int main() 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n;
	for (int i = 0; i < n; i++)
		cin >> sequence[i];
	for (int i = 0; i < 4; i++)
		cin >> operators[i];

	mx = -0x7f7f7f7f;	//가장 최소값으로 잡아놓음
	mn = 0x7f7f7f7f;	//가장 최대값으로 잡아놓음 -> 이래야 max, min 함수가 작동
	BackTracking(sequence[0], 0);
	cout << mx << "\n";
	cout << mn;
}

void BackTracking(int number, int cnt)
{
	if (cnt >= n - 1)
	{
		mx = max(number, mx);
		mn = min(number, mn);
		return;
	}

	int nextNumber = 0;
	for (int i = 0; i < 4; i++)
	{
		if(operators[i] <= 0)
			continue;
		operators[i]--;	//사용할 연산자 감소
		switch (i)
		{
		case 0 :
			nextNumber = number + sequence[cnt + 1];
			break;
		case 1:
			nextNumber = number - sequence[cnt + 1];
			break;
		case 2:
			nextNumber = number * sequence[cnt + 1];
			break;
		case 3:
			nextNumber = number / sequence[cnt + 1];
			break;
		default:
			break;
		}
		BackTracking(nextNumber, cnt + 1);
		operators[i]++;	//사용한 연산자 복구
	}
}
{% endhighlight %}

## 다른풀이

<div class="notice--info" markdown="1">
**<u>next_permutation 을 이용해서 해결</u>**
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int nums[12];
int ops[12];  // ops[0]은 항상 더하기
int n;
int mn = 0x7f7f7f7f, mx = -0x7f7f7f7f;

int main() 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n;
	for (int i = 0; i < n; i++)
		cin >> nums[i];
	for (int op = 0, idx = 1; op < 4; op++)	//idx 0번은 항상 0으로 나둬서 가장 처음은 +로 고정
	{
		int x; 
		cin >> x;
		while (x--)	//예를들어 *가 3개 들어오면 index 3개에 0을 채움
			ops[idx++] = op;
		// ex) 2 1 3 4 가 입력값이라면 : + + - * * * / / / /
		// ops[] = { 00012223333 } -> 가장 앞은 +로 고정시킴
	}

	do {
		int res = 0;
		for (int i = 0; i < n; i++)		//이번 회차의 연산자 계산
		{
			if (ops[i] == 0) 
				res += nums[i];
			else if (ops[i] == 1) 
				res -= nums[i];
			else if (ops[i] == 2) 
				res *= nums[i];
			else 
				res /= nums[i];
		}

		mx = max(mx, res);
		mn = min(mn, res);
	} while (next_permutation(ops + 1, ops + n));	//index 0번은 +로 고정시키고 1번부터 변경

	cout << mx << '\n' << mn;
}
{% endhighlight %}