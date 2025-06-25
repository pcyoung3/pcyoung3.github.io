---
layout: single
title: 백준 2011번 - 암호코드
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
---

## 문제
[백준 2011번 문제](https://www.acmicpc.net/problem/2011) <br>
상근이와 선영이가 다른 사람들이 남매간의 대화를 듣는 것을 방지하기 위해서 대화를 서로 암호화 하기로 했다. 그래서 다음과 같은 대화를 했다.

- 상근: 그냥 간단히 암호화 하자. A를 1이라고 하고, B는 2로, 그리고 Z는 26으로 하는거야.
- 선영: 그럼 안돼. 만약, "BEAN"을 암호화하면 25114가 나오는데, 이걸 다시 글자로 바꾸는 방법은 여러 가지가 있어.
- 상근: 그렇네. 25114를 다시 영어로 바꾸면, "BEAAD", "YAAD", "YAN", "YKD", "BEKD", "BEAN" 총 6가지가 나오는데, BEAN이 맞는 단어라는건 쉽게 알수 있잖아?
- 선영: 예가 적절하지 않았네 ㅠㅠ 만약 내가 500자리 글자를 암호화 했다고 해봐. 그 때는 나올 수 있는 해석이 정말 많은데, 그걸 언제 다해봐?
- 상근: 얼마나 많은데?
- 선영: 구해보자!

어떤 암호가 주어졌을 때, 그 암호의 해석이 몇 가지가 나올 수 있는지 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 5000자리 이하의 암호가 주어진다. 암호는 숫자로 이루어져 있다.

## 출력

나올 수 있는 해석의 가짓수를 구하시오. 정답이 매우 클 수 있으므로, 1000000으로 나눈 나머지를 출력한다.

암호가 잘못되어 암호를 해석할 수 없는 경우에는 0을 출력한다.

## 예제 입력 1 복사

```
25114
```

## 예제 출력 1 복사

```
6
```

## 예제 입력 2 복사

```
1111111111
```

## 예제 출력 2 복사

```
89
```

## 풀이
<div class="notice" markdown="1">
**<u>경우의 수가 어떻게 증가하는 지가 포인트</u>** <br>
![KakaoTalk_20240110_123806719.jpg](/assets/images/algorithm/KakaoTalk_20240110_123806719.jpg) <br>
점화식은 다음과 같이 구할 수 있다. <br>

문제는 예외처리 할 것이 좀 많다. <br>
10, 20 같은 단일 숫자인데 경우의 수가 1개일 경우와 <br>
불가능한 숫자가 왔을 때 어떻게 판별하고 출력하는 지 예외처리가 많다.
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

using ul = unsigned long;

int arr[5005];
ul dp[5005];

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	string input;
	cin >> input;

	int n = input.length();
	for (int i = 0; i < n; i++)
	{
		arr[i + 1] = input[i] - '0';
		if (arr[i] == 0 && arr[i + 1] == 0)
		{
			cout << "0";
			return 0;
		}
	}
	
	dp[1] = 1;	//1자리수는 무조건 1개의 경우의 수
	int value = arr[1] * 10 + arr[2];	//앞자리수 포함해서 숫자계산
	if (arr[2] == 0 && n > 1)	//끝자리가 0이고 자리수가 2자리수 이상일 때
	{
		if (arr[1] < 3)	//10, 20의 경우
			dp[2] = 1;
		else	//30, 40, 50의 경우 있을 수 없는 숫자
		{
			cout << "0";
			return 0;
		}
	}
	else	//끝자리가 0이 아닐 경우
	{
		if (value < 27 && value > 10)	//11~26의 경우 2가지의 경우의 수
			dp[2] = 2;
		else	//그 이외는 1개의 경우의 수
			dp[2] = 1;
	}

	for (int i = 3; i <= n; i++)
	{
		int value = arr[i - 1] * 10 + arr[i];
		if (arr[i] == 0)	//끝자리가 0일 경우
		{
			if (arr[i - 1] < 3)		//10, 20의 경우
				dp[i] = dp[i - 2];	//20은 2숫자가 1가지의 경우의 수밖에 못가지므로 i-2를 그대로 가져옴
			else	//30, 40.. 같은 경우 있을 수 있는 숫자가 아님
			{
				cout << "0";
				return 0;
			}
		}
		else	//끝자리가 0이 아닐 경우
		{
			if (value < 27 && value > 10)	//숫자가 11~26이면 1자리수일 수도 있고 2자리수일 수도 있음
				dp[i] = dp[i - 1] + dp[i - 2];	//따라서 i-1의 경우와 i-2의 경우를 더해준다
			else	//한자리 숫자일 경우
				dp[i] = dp[i - 1];		//이전 자리수의 경우의 수를 그대로 가져옴
		}
			
		dp[i] %= 1000000;
	}

	cout << dp[n];
	return 0;
}
{% endhighlight %}

## 다른풀이
<div class="notice--info" markdown="1">
**<u>간단한 코드로 정리가능</u>** <br>
i번째 자리가 0이 아니면 i-1번째 자리의 경우의 수를 가져올 수 있고 <br>
i번째자리와 i-1번째 자리의 합산이 2자리수의 알파벳 조건을 만족한다면 i-1번째 자리의 경우의 수를 가져올 수 있다. <br>

그리고 그 2개를 따로 더해주면 많은 예외처리를 생략할 수 있음
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int n;
int a[5010], d[5010];
int mod = 1000000;

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	string input; 
	cin >> input;

	n = input.size();
	for (int i = 1; i <= n; ++i)
		a[i] = input[i - 1] - '0';

	d[0] = 1;
	for (int i = 1; i <= n; ++i)
	{
		if (a[i] > 0)	//0이 아니라면 1자리수 전의 경우의 수를 더할 수 있음
			d[i] = (d[i] + d[i - 1]) % mod;	//d[i] = d[i - 1] % mod; 와 동일한데 가시성을 위해 이렇게 적음

		int x = a[i - 1] * 10 + a[i];
		if (x >= 10 && x <= 26)		//10~26 사이라면 2자리수 전의 가짓수를 더할 수 있음
			d[i] = (d[i] + d[i - 2]) % mod;
	}

	cout << d[n];
}
{% endhighlight %}