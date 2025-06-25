---
layout: single
title: 백준 2579번 - 계단 오르기
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
---

## 문제
[백준 2579번 문제](https://www.acmicpc.net/problem/2579) <br>
계단 오르기 게임은 계단 아래 시작점부터 계단 꼭대기에 위치한 도착점까지 가는 게임이다. <그림 1>과 같이 각각의 계단에는 일정한 점수가 쓰여 있는데 계단을 밟으면 그 계단에 쓰여 있는 점수를 얻게 된다.

![2579_p_1.png](/assets/images/algorithm/2579_p_1.png)
<그림 1>

예를 들어 <그림 2>와 같이 시작점에서부터 첫 번째, 두 번째, 네 번째, 여섯 번째 계단을 밟아 도착점에 도달하면 총 점수는 10 + 20 + 25 + 20 = 75점이 된다.

![2579_p_2.png](/assets/images/algorithm/2579_p_2.png)
<그림 2>

계단 오르는 데는 다음과 같은 규칙이 있다.

1. 계단은 한 번에 한 계단씩 또는 두 계단씩 오를 수 있다. 즉, 한 계단을 밟으면서 이어서 다음 계단이나, 다음 다음 계단으로 오를 수 있다.
2. 연속된 세 개의 계단을 모두 밟아서는 안 된다. 단, 시작점은 계단에 포함되지 않는다.
3. 마지막 도착 계단은 반드시 밟아야 한다.

따라서 첫 번째 계단을 밟고 이어 두 번째 계단이나, 세 번째 계단으로 오를 수 있다. 하지만, 첫 번째 계단을 밟고 이어 네 번째 계단으로 올라가거나, 첫 번째, 두 번째, 세 번째 계단을 연속해서 모두 밟을 수는 없다.

각 계단에 쓰여 있는 점수가 주어질 때 이 게임에서 얻을 수 있는 총 점수의 최댓값을 구하는 프로그램을 작성하시오.

## 입력

입력의 첫째 줄에 계단의 개수가 주어진다.

둘째 줄부터 한 줄에 하나씩 제일 아래에 놓인 계단부터 순서대로 각 계단에 쓰여 있는 점수가 주어진다. 계단의 개수는 300이하의 자연수이고, 계단에 쓰여 있는 점수는 10,000이하의 자연수이다.

## 출력

첫째 줄에 계단 오르기 게임에서 얻을 수 있는 총 점수의 최댓값을 출력한다.

## 예제 입력 1 복사

```
6
10
20
15
25
10
20
```

## 예제 출력 1 복사

```
75
```

## 풀이
<div class="notice--warning" markdown="1">
**<u>2차원 배열로 푸는 방법</u>** <br>
![KakaoTalk_20231025_091030276.jpg](/assets/images/algorithm/KakaoTalk_20231025_091030276.jpg)
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int dp[305][3]; //dp[i][j] : j개의 계단을 연속해서 밟고 i번재 계단까지 올라갔을 때 점수의 최댓값. i번째 계단은 무조건 밟아야 됨
int stair[305];

int main(void) 
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    int n;
    cin >> n;
    for (int i = 1; i <= n; i++)
        cin >> stair[i];

    //초기값
    dp[1][1] = stair[1];
    dp[1][2] = 0;
    dp[2][1] = stair[2];
    dp[2][2] = stair[1] + stair[2];

    for (int i = 1; i <= n; i++)
    {
        //점화식
        dp[i][1] = max(dp[i - 2][1], dp[i - 2][2]) + stair[i];
        dp[i][2] = dp[i - 1][1] + stair[i];
    }

    cout << max(dp[n][1], dp[n][2]);
}
{% endhighlight %}

## 다른풀이
<div class="notice--warning" markdown="1">
**<u>밟지 않는 경우의 최소값을 구해서 1차원 배열로 푸는 방법</u>** <br>
![KakaoTalk_20231025_114858958.jpg](/assets/images/algorithm/KakaoTalk_20231025_114858958.jpg)
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int d[305];	//특정 계단을 밟지 않았을 때 지금까지 밟지 않은 계단의 최소값
int s[305];	//계단의 점수를 저장할 배열

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int n;
	cin >> n;

	// 밟지않은 계단의 최소값을 빼기 위해서 모든 계단의 점수를 합함
	int tot = 0;
	for (int i = 1; i <= n; i++) 
	{
		cin >> s[i];
		tot += s[i];
	}

	// 점화식에 쓰이는 초기값에 접근할 수 있기 때문에 미리 예외처리
	if (n <= 2)
	{
		cout << tot;
		return 0;
	}

	// 초기값 정하기
	d[1] = s[1];
	d[2] = s[2];
	d[3] = s[3];

	// 점화식 세우고 배열 채우기
	for (int i = 4; i <= n - 1; i++)
		d[i] = min(d[i - 2], d[i - 3]) + s[i];

	// 전체점수 - n번째 계단까지 밟지 않았을 때의 최소값
	cout << tot - min(d[n - 1], d[n - 2]);
}
{% endhighlight %}

## 다른풀이
<div class="notice--warning" markdown="1">
**<u>정공법으로 1차원 배열로 푸는 방법</u>** <br>
![KakaoTalk_20231025_163833147.jpg](/assets/images/algorithm/KakaoTalk_20231025_163833147.jpg)
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int dp[305];	//특정계단을 밟았을 때 최대값
int step[305];	//계단의 점수를 저장할 배열

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int n;
	cin >> n;

	for (int i = 1; i <= n; i++)
		cin >> step[i];

	//초기값 정하기
	dp[1] = step[1];
	if (n > 1) 
		dp[2] = step[2] + step[1];
	if (n > 2)
		dp[3] = step[3] + max(step[2], step[1]);

	//점화식 세우기
	for (int i = 4; i <= n; i++)
		dp[i] = step[i] + max(step[i - 1] + dp[i - 3], dp[i - 2]);


	cout << dp[n];
	return 0;
}
{% endhighlight %}