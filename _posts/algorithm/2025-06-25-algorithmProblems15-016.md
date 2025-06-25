---
layout: single
title: 백준 12865번 - 평범한 배낭
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
---

## 문제
[백준 12865번 문제](https://www.acmicpc.net/problem/12865) <br>
이 문제는 아주 평범한 배낭에 관한 문제이다.

한 달 후면 국가의 부름을 받게 되는 준서는 여행을 가려고 한다. 세상과의 단절을 슬퍼하며 최대한 즐기기 위한 여행이기 때문에, 가지고 다닐 배낭 또한 최대한 가치 있게 싸려고 한다.

준서가 여행에 필요하다고 생각하는 N개의 물건이 있다. 각 물건은 무게 W와 가치 V를 가지는데, 해당 물건을 배낭에 넣어서 가면 준서가 V만큼 즐길 수 있다. 아직 행군을 해본 적이 없는 준서는 최대 K만큼의 무게만을 넣을 수 있는 배낭만 들고 다닐 수 있다. 준서가 최대한 즐거운 여행을 하기 위해 배낭에 넣을 수 있는 물건들의 가치의 최댓값을 알려주자.

## 입력

첫 줄에 물품의 수 N(1 ≤ N ≤ 100)과 준서가 버틸 수 있는 무게 K(1 ≤ K ≤ 100,000)가 주어진다. 두 번째 줄부터 N개의 줄에 거쳐 각 물건의 무게 W(1 ≤ W ≤ 100,000)와 해당 물건의 가치 V(0 ≤ V ≤ 1,000)가 주어진다.

입력으로 주어지는 모든 수는 정수이다.

## 출력

한 줄에 배낭에 넣을 수 있는 물건들의 가치합의 최댓값을 출력한다.

## 예제 입력 1 복사

```
4 7
6 13
4 8
3 6
5 12
```

## 예제 출력 1 복사

```
14
```

## 풀이
<div class="notice" markdown="1">
**<u>짐의 무게, 짐의 value를 동시에 고려해야 함</u>** <br>
참고문제: [동전 1](/algorithmproblems/algorithmProblems15-031/) <br>

고려해야 할 게 2개(무게, value)여서 2차원 배열이기도 하고 <br>
각 짐을 1번만 사용할 수 있기 때문에 2차원 배열을 사용한 것이기도 함 <br>
![KakaoTalk_20240129_115818146.jpg](/assets/images/algorithm/KakaoTalk_20240129_115818146.jpg)
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int n, k;
int w[104];	//무게
int v[104];	//value
int d[101][100004];	//d[i][j] : i개의 짐 사용, j의 무게 일때의 value의 최댓값

int main(void)
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n >> k;
	for (int i = 1; i <= n; i++)
		cin >> w[i] >> v[i];
	
	for (int i = 1; i <= n; i++)
	{
		for (int j = 1; j <= k; j++)
		{
			if (w[i] <= j)	//짐의 무게가 현재 무게보다 작을 때만
				d[i][j] = max(d[i - 1][j - w[i]] + v[i], d[i - 1][j]);
			else	//짐을 한 개 덜 사용했을 때의 최댓값으로 테이블을 갱신
				d[i][j] = d[i - 1][j];
		}
	}

	cout << d[n][k];
}
{% endhighlight %}

<div class="notice--danger" markdown="1">
**<u>Greedy로 보일 수도 있지만...?</u>** <br>
문제를 처음에 보면 무게 대비 가치 비율이 높은 물건들을 고르는 Greedy 방식을 떠올릴 수 있지만 <br>
![Pasted image 20240129120330.png](/assets/images/algorithm/Pasted image 20240129120330.png) <br>
다음 상황에서 무게 10에서 최대 가치를 고르려고 한다면 <br>
Greedy로 풀이할 경우 무게6의 가치7의 짐을 골라서 최댓값이 7로 끝나는 반면에 <br>
사실 무게 5짜리 짐을 2개골라서 가치를 9를 만드는 것이 최댓값임을 알 수 있다. <br>

따라서 Greedy로 처음부터 접근하려고 하면 낭패를 보는 경우이다.
</div>