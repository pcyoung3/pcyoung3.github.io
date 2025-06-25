---
layout: single
title: 백준 2156번 - 포도주 시식
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
---

## 문제
[백준 2156번 문제](https://www.acmicpc.net/problem/2156) <br>
효주는 포도주 시식회에 갔다. 그 곳에 갔더니, 테이블 위에 다양한 포도주가 들어있는 포도주 잔이 일렬로 놓여 있었다. 효주는 포도주 시식을 하려고 하는데, 여기에는 다음과 같은 두 가지 규칙이 있다.

1. 포도주 잔을 선택하면 그 잔에 들어있는 포도주는 모두 마셔야 하고, 마신 후에는 원래 위치에 다시 놓아야 한다.
2. 연속으로 놓여 있는 3잔을 모두 마실 수는 없다.

효주는 될 수 있는 대로 많은 양의 포도주를 맛보기 위해서 어떤 포도주 잔을 선택해야 할지 고민하고 있다. 1부터 n까지의 번호가 붙어 있는 n개의 포도주 잔이 순서대로 테이블 위에 놓여 있고, 각 포도주 잔에 들어있는 포도주의 양이 주어졌을 때, 효주를 도와 가장 많은 양의 포도주를 마실 수 있도록 하는 프로그램을 작성하시오. 

예를 들어 6개의 포도주 잔이 있고, 각각의 잔에 순서대로 6, 10, 13, 9, 8, 1 만큼의 포도주가 들어 있을 때, 첫 번째, 두 번째, 네 번째, 다섯 번째 포도주 잔을 선택하면 총 포도주 양이 33으로 최대로 마실 수 있다.

## 입력

첫째 줄에 포도주 잔의 개수 n이 주어진다. (1 ≤ n ≤ 10,000) 둘째 줄부터 n+1번째 줄까지 포도주 잔에 들어있는 포도주의 양이 순서대로 주어진다. 포도주의 양은 1,000 이하의 음이 아닌 정수이다.

## 출력

첫째 줄에 최대로 마실 수 있는 포도주의 양을 출력한다.

## 예제 입력 1 복사

```
6
6
10
13
9
8
1
```

## 예제 출력 1 복사

```
33
```

## 풀이
<div class="notice--danger" markdown="1">
**<u>반례에 주의</u>** <br>
```
6
100
400
2
1
4
200
```
다음과 같이 중간에 2번 건너뛰어야 하는 반례가 존재
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int dp[10005];	//dp[i] : i번째 포도주를 포함한 포도주 총합의 최댓값
int arr[10005];

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int n;
	cin >> n;
	for (int i = 1; i <= n; i++)
		cin >> arr[i];

	//초기값 설정
	dp[1] = arr[1];
	dp[2] = arr[1] + arr[2];
	dp[3] = max(arr[1], arr[2]) + arr[3];
	dp[4] = max(dp[2], dp[1] + arr[3]) + arr[4];
	for (int i = 5; i <= n; i++)
	{
		/*
		* 점화식
		* i번째를 무조건 마시는 전제하에 3가지 경우의 수 존재
		* 1. i-2번째를 마실 경우
		* 2. i-1번째와 i-3번째를 마실 경우
		* 3. i-1번째와 i-4번째를 마실 경우 -> 2번 건너뛸 수 있다. 3번 건너뛰는 것은 의미없음
		*/
		dp[i] = max(dp[i - 2], dp[i - 3] + arr[i - 1]) + arr[i];
		dp[i] = max(dp[i], dp[i - 4] + arr[i - 1] + arr[i]);
	}

	cout << *max_element(dp + 1, dp + n + 1);
}
{% endhighlight %}

## 다른풀이
<div class="notice--info" markdown="1">
**<u>점화식을 세우는 다른 방법</u>** <br>
![KakaoTalk_20231202_111608569.jpg](/assets/images/algorithm/KakaoTalk_20231202_111608569.jpg)
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int n;
int a[10010], d[10010];	//d[i] : i번째일때 포도주의 최댓값

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n;
	for (int i = 1; i <= n; ++i) 
		cin >> a[i];

	//초기값 정하기
	d[1] = a[1]; 
	d[2] = a[1] + a[2];
	
	//점화식
	for (int i = 3; i <= n; ++i)
		d[i] = max({ d[i - 1], d[i - 2] + a[i], d[i - 3] + a[i - 1] + a[i] });

	cout << d[n];
}
{% endhighlight %}

## 다른풀이
<div class="notice--info" markdown="1">
**<u>2차원 배열로 점화식을 세우는 방법</u>** <br>
현재 마시는 포도주가 몇번 연속인지 를 표현해서 2차원배열로 접근 가능하다
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int n;
int a[10010], d[10010][3];
/*
* d[i][0] : a[i]번 포도주를 마시지 않을 경우의 최댓값
* d[i][1] : a[i]번 포도주를 1병 마셨을 경우의 최댓값
* d[i][2] : a[i - 1]번과 a[i]번 포도주를 합해 2병 마셨을 때의 최댓값
*/

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n;
	for (int i = 1; i <= n; ++i) 
		cin >> a[i];

	//초기값 정하기
	d[1][1] = a[1];
	for (int i = 2; i <= n; ++i)
	{
		d[i][0] = max({ d[i - 1][0], d[i - 1][1], d[i - 1][2] });
		d[i][1] = d[i - 1][0] + a[i];
		d[i][2] = d[i - 1][1] + a[i];
	}

	cout << max({ d[n][0], d[n][1], d[n][2] });
}
{% endhighlight %}