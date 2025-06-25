---
layout: single
title: 백준 1699번 - 제곱수의 합
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
use_math: true
---

## 문제
[백준 1699번 문제](https://www.acmicpc.net/problem/1699) <br>
어떤 자연수 N은 그보다 작거나 같은 제곱수들의 합으로 나타낼 수 있다. 예를 들어 $11=3^2+1^2+1^2$(3개 항)이다. 이런 표현방법은 여러 가지가 될 수 있는데, 11의 경우 $11=2^2+2^2+1^2+1^2+1^2$(5개 항)도 가능하다. 이 경우, 수학자 숌크라테스는 "11은 3개 항의 제곱수 합으로 표현할 수 있다."라고 말한다. 또한 11은 그보다 적은 항의 제곱수 합으로 표현할 수 없으므로, 11을 그 합으로써 표현할 수 있는 제곱수 항의 최소 개수는 3이다.

주어진 자연수 N을 이렇게 제곱수들의 합으로 표현할 때에 그 항의 최소개수를 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 자연수 N이 주어진다. (1 ≤ N ≤ 100,000)

## 출력

주어진 자연수를 제곱수의 합으로 나타낼 때에 그 제곱수 항의 최소 개수를 출력한다.

## 예제 입력 1 복사

```
7
```

## 예제 출력 1 복사

```
4
```

## 예제 입력 2 복사

```
1
```

## 예제 출력 2 복사

```
1
```

## 예제 입력 3 복사

```
4
```

## 예제 출력 3 복사

```
1
```

## 예제 입력 4 복사

```
11
```

## 예제 출력 4 복사

```
3
```

## 예제 입력 5 복사

```
13
```

## 예제 출력 5 복사

```
2
```

## 풀이
<div class="notice--primary" markdown="1">
**<u>복잡한 점화식에 주의</u>** <br>
DP_백준2293 [동전 1](/algorithmproblems/algorithmProblems15-031/), [1, 2, 3 더하기](/algorithmproblems/algorithmProblems15-002/) 참고 <br>
![KakaoTalk_20231230_124559119.jpg](/assets/images/algorithm/KakaoTalk_20231230_124559119.jpg) <br>
문제에서 요구하는 제곱수를 구할 때 현재 수를 i라고 한다면 <br>
i와 가장 가까운 제곱근을 구하면 아래의 틀린풀이와 같이 예외상황이 발생해서 틀리게 됨. <br>
따라서 어떤 제곱근이 가장 적은 수의 항인지 모르기 때문에 2중 for문으로 전수조사를 돌려야 함
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int n;
int d[100010];

int main(void)
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n;

	for (int i = 1; i <= n; ++i) 
	{
		d[i] = 0x3f3f3f;	//더미로 최댓값을 잡아줌

		for (int j = 1; j * j <= i; ++j)	//제곱할 숫자가 i보다 크면 의미 없음
			d[i] = min(d[i], d[i - j * j] + 1);
	}
	cout << d[n];
}
{% endhighlight %}

## 틀린풀이
<div class="notice--danger" markdown="1">
**<u>현재 숫자에서 가장 큰 제곱근으로 만드는 것인데 반례를 해결 못함</u>** <br>
반례) <br>
	12(정답 : 3, 코드결과 : 4) <br>
	18(정답 : 2, 코드결과 : 3)
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int n;
	cin >> n;

	int temp = n;
	int result = 0;
	while (temp)
	{
		int middle = sqrt(temp);
		temp -= middle * middle;
		result++;
	}
	cout << result;
}
{% endhighlight %}