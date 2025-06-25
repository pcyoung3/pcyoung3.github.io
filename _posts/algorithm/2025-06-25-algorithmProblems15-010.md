---
layout: single
title: 백준 10942번 - 팰린드롬
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
use_math: true
---

## 문제
[백준 10942번 문제](https://www.acmicpc.net/problem/10942) <br>
명우는 홍준이와 함께 팰린드롬 놀이를 해보려고 한다.

먼저, 홍준이는 자연수 N개를 칠판에 적는다. 그 다음, 명우에게 질문을 총 M번 한다.

각 질문은 두 정수 S와 E(1 ≤ S ≤ E ≤ N)로 나타낼 수 있으며, S번째 수부터 E번째 까지 수가 팰린드롬을 이루는지를 물어보며, 명우는 각 질문에 대해 팰린드롬이다 또는 아니다를 말해야 한다.

예를 들어, 홍준이가 칠판에 적은 수가 1, 2, 1, 3, 1, 2, 1라고 하자.

- S = 1, E = 3인 경우 1, 2, 1은 팰린드롬이다.
- S = 2, E = 5인 경우 2, 1, 3, 1은 팰린드롬이 아니다.
- S = 3, E = 3인 경우 1은 팰린드롬이다.
- S = 5, E = 7인 경우 1, 2, 1은 팰린드롬이다.

자연수 N개와 질문 M개가 모두 주어졌을 때, 명우의 대답을 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 수열의 크기 N (1 ≤ N ≤ 2,000)이 주어진다.

둘째 줄에는 홍준이가 칠판에 적은 수 N개가 순서대로 주어진다. 칠판에 적은 수는 100,000보다 작거나 같은 자연수이다.

셋째 줄에는 홍준이가 한 질문의 개수 M (1 ≤ M ≤ 1,000,000)이 주어진다.

넷째 줄부터 M개의 줄에는 홍준이가 명우에게 한 질문 S와 E가 한 줄에 하나씩 주어진다.

## 출력

총 M개의 줄에 걸쳐 홍준이의 질문에 대한 명우의 답을 입력으로 주어진 순서에 따라서 출력한다. 팰린드롬인 경우에는 1, 아닌 경우에는 0을 출력한다.

## 예제 입력 1 복사

```
7
1 2 1 3 1 2 1
4
1 3
2 5
3 3
5 7
```

## 예제 출력 1 복사

```
1
0
1
1
```

## 틀린답안
<div class="notice--warning" markdown="1">
**<u>시간초과 발생</u>** <br>
최악의 경우 $2000 \times 1000000 = 2,000,000,000$ 번의 연산이 발생하기 때문에 시간초과가 발생
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

typedef struct tQuestion
{
	int s;
	int e;
}Question;

int arr[100005];
Question quest[1000005];
int n, m;

int main(void)
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n;
	for (int i = 1; i <= n; i++)
		cin >> arr[i];
	cin >> m;
	for (int i = 1; i <= m; i++)
		cin >> quest[i].s >> quest[i].e;

	for (int i = 1; i <= m; i++)
	{
		int start = quest[i].s;
		int end = quest[i].e;
		bool IsPalind = true;

		while (start < end)
		{
			if (arr[start] != arr[end])
			{
				IsPalind = false;
				break;
			}
			start++;
			end--;
		}

		if (IsPalind)
			cout << "1\n";
		else
			cout << "0\n";
	}
}
{% endhighlight %}

## 풀이
<div class="notice--info" markdown="1">
**<u>시간복잡도로 인해서 DP 사용</u>** <br>
시작지점과 끝지점에 따라서 팰린드롬이 될 수 있는지가 결정되기 때문에 <br>
테이블을 시작지점과 끝지점을 넣은 2차원 배열로 만들어야 함 <br>

따라서 테이블의 정의는 다음과 같이 잡고 진행 <br>
`d[start][end] = start ~ end 구간이 팰린드롬이라면 1 아니면 0` <br>
![KakaoTalk_20240106_120543952.jpg](/assets/images/algorithm/KakaoTalk_20240106_120543952.jpg) <br>

또한 구현하다 보면 길이가 3인 경우 for문을 적기가 까다롭다. <br>
![KakaoTalk_20240106_120614643.jpg](/assets/images/algorithm/KakaoTalk_20240106_120614643.jpg) <br>
`for (int gap = 2; gap < n; ++gap)` <br>
> 2중 for문의 밖에 있는 for문이기 때문에 더 나중에 실행된다. 즉, gap을 나중에 늘려준다. <br>
> 길이가 1과 2일때는 위에서 구했기 때문에 2부터 시작 <br>
> gap이 2일 때 값들을 먼저 채워 넣어야 3일 때 활용할 수 있기 때문 <br>

`for (int i = 1; i <= n - gap; ++i)` <br>
>안쪽에서 시작지점을 뒤로 진행시키는 for문이다. <br>
>원래대로라면 1~n까지 진행했겠지만 길이가 2인것들은 전부 구했기도 했고 <br>
>i+gap이 end 포인트기 때문에 i+gap<n 을 만족해야 한다.
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int n;
int a[2010];
int d[2010][2010];	//d[startIndex][endIndex] : 1인 경우 startIndex ~ endIndex 까지 팰린드롬

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n;
	for (int i = 1; i <= n; ++i) 
		cin >> a[i];

	for (int i = 1; i <= n; ++i) 
	{
		d[i][i] = 1;	//길이가 1인 경우는 모두 팰린드롬
		if (a[i - 1] == a[i])	//길이가 2인 경우는 같으면 팰린드롬
			d[i - 1][i] = 1;
	}

	//길이가 3부터는 다른방식으로 구해야 함
	//i 부터 j까지 팰린드롬이기 위해서는 i+1 ~ j-1 까지 팰린드롬이여야 함
	//그 뒤 i == j 일 경우 팰린드롬 확정
	for (int gap = 2; gap < n; ++gap)	//이전 연산을 활용하기 위해 gap을 나중에 늘려줌
	{
		for (int i = 1; i <= n - gap; ++i)	//시작 포인트를 먼저 늘려준다
		{
			int start = i;
			int end = i + gap;
			if (a[start] == a[end] && d[start + 1][end - 1])
				d[start][end] = 1;
		}
	}

	int t;
	cin >> t;
	while (t--) 
	{
		int s, e;
		cin >> s >> e;
		cout << d[s][e] << '\n';
	}

	return 0;
}
{% endhighlight %}