---
layout: single
title: 백준 2293번 - 동전 1
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
use_math: true
---

## 문제
[백준 2293번 문제](https://www.acmicpc.net/problem/2293) <br>
n가지 종류의 동전이 있다. 각각의 동전이 나타내는 가치는 다르다. 이 동전을 적당히 사용해서, 그 가치의 합이 k원이 되도록 하고 싶다. 그 경우의 수를 구하시오. 각각의 동전은 몇 개라도 사용할 수 있다.

사용한 동전의 구성이 같은데, 순서만 다른 것은 같은 경우이다.

## 입력

첫째 줄에 n, k가 주어진다. (1 ≤ n ≤ 100, 1 ≤ k ≤ 10,000) 다음 n개의 줄에는 각각의 동전의 가치가 주어진다. 동전의 가치는 100,000보다 작거나 같은 자연수이다.

## 출력

첫째 줄에 경우의 수를 출력한다. 경우의 수는 $2^{31}$보다 작다.

## 예제 입력 1 복사

```
3 10
1
2
5
```

## 예제 출력 1 복사

```
10
```

## 풀이
<div class="notice--info" markdown="1">
**<u>테이블을 여러번 나누어서 갱신하는 방법</u>** <br>
특정 숫자 k를 만족하는 조합은 기본적으로 무한대여서 해당 접근으로 실패 <br>
-> 하지만 동전의 종류가 정해져 있으므로 조합은 무한대가 아니게 됨 <br>
    
`n, i` : 동전의 종류 <br>
`k, j` : k는 동전 가치의 합 <br>
`a[ ]` : 동전종류의 input 값 <br>
![KakaoTalk_20231220_124732974.jpg](/assets/images/algorithm/KakaoTalk_20231220_124732974.jpg) <br>
    
테이블이 채워지는 순서 (1, 2, 5일 때) <br>
1. i == 1 (1) <br>
	![스크린샷 2023-12-20 130055.png](/assets/images/algorithm/스크린샷 2023-12-20 130055.png) <br>
2. i == 2 (2) <br>
	![스크린샷 2023-12-20 130110.png](/assets/images/algorithm/스크린샷 2023-12-20 130110.png) <br>
3. i == 3 (5) <br>
	![스크린샷 2023-12-20 130123.png](/assets/images/algorithm/스크린샷 2023-12-20 130123.png) <br>
다음과 같이 테이블이 여러번 돌면서 값이 계속 누적되는 형식
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;
int n, k;
int a[10005], d[10005];

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n >> k;
	for (int i = 0; i < n; i++)
		cin >> a[i];

	d[0] = 1;	//0을 만드는 경우는 아무것도 쓰지 않는 경우밖에 없음
	for (int i = 0; i < n; i++)
	{
		for (int j = a[i]; j <= k; j++)	//시작을 a[i]로 함. 5가 들어왔다면 5로 5아래 숫자는 만들 수 없음
			d[j] += d[j - a[i]];
	}

	cout << d[k] << '\n';
}
{% endhighlight %}