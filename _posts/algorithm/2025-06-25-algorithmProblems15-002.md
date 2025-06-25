---
layout: single
title: 백준 9095번 - 1, 2, 3 더하기
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
---

## 문제
[백준 9095번 문제](https://www.acmicpc.net/problem/9095) <br>
정수 4를 1, 2, 3의 합으로 나타내는 방법은 총 7가지가 있다. 합을 나타낼 때는 수를 1개 이상 사용해야 한다.

- 1+1+1+1
- 1+1+2
- 1+2+1
- 2+1+1
- 2+2
- 1+3
- 3+1

정수 n이 주어졌을 때, n을 1, 2, 3의 합으로 나타내는 방법의 수를 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 테스트 케이스의 개수 T가 주어진다. 각 테스트 케이스는 한 줄로 이루어져 있고, 정수 n이 주어진다. n은 양수이며 11보다 작다.

## 출력

각 테스트 케이스마다, n을 1, 2, 3의 합으로 나타내는 방법의 수를 출력한다.

## 예제 입력 1 복사

```
3
4
7
10
```

## 예제 출력 1 복사

```
7
44
274
```

## 풀이
<div class="notice--warning" markdown="1">
**<u>DP 문제</u>** <br>
1. 테이블 정의하기 <br>
2. 점화식 구하기 <br>
3. 초기값 정하기 <br>
의 순서로 문제 접근을 하면 됨 <br>
![KakaoTalk_20231024_213026767.jpg](/assets/images/algorithm/KakaoTalk_20231024_213026767.jpg)
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int dp[20];

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int t, n;
	cin >> t;

	//초기값 정의
	dp[1] = 1;
	dp[2] = 2;
	dp[3] = 4;

	//DP를 이용해서 계산식
	for (int i = 4; i < 15; i++)
		dp[i] = dp[i - 1] + dp[i - 2] + dp[i - 3];

	while (t--)
	{
		cin >> n;
		cout << dp[n] << "\n";
	}
}
{% endhighlight %}

## 다른풀이
<div class="notice--info" markdown="1">
**<u>시간복잡도의 범위가 널널해서 재귀로도 접근할 수 있다</u>** <br>
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int func(int n);
int main(void)
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    int t, n;
    cin >> t;

    while (t--)
    {
        cin >> n;
        int result = func(n);
        cout << result << "\n";
    }
}

int func(int n)
{
    if (n < 0)  //0보다 작으면 옳바른 상황이 아니기 때문에 0
        return 0;
    if (n == 0) //0일 경우 정답이기 때문에 1을 return
        return 1;
    
    return func(n - 1) + func(n - 2) + func(n - 3);
}
/*
    위의 base condition에서 return 0이나 1로 나왔어도
    아래의 재귀호출 구문에서 3개 호출식을 더하니 return값이
    1이상 될 수있다. 따라서 정상작동함
*/
{% endhighlight %}