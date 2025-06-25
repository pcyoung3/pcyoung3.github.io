---
layout: single
title: 백준 11726번 - 2×n 타일링
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
---

## 문제
[백준 11726번 문제](https://www.acmicpc.net/problem/11726) <br>
2×n 크기의 직사각형을 1×2, 2×1 타일로 채우는 방법의 수를 구하는 프로그램을 작성하시오.

아래 그림은 2×5 크기의 직사각형을 채운 한 가지 방법의 예이다.
![11726_P_01.png](/assets/images/algorithm/11726_P_01.png)

## 입력

첫째 줄에 n이 주어진다. (1 ≤ n ≤ 1,000)

## 출력

첫째 줄에 2×n 크기의 직사각형을 채우는 방법의 수를 10,007로 나눈 나머지를 출력한다.

## 예제 입력 1 복사

```
2
```

## 예제 출력 1 복사

```
2
```

## 예제 입력 2 복사

```
9
```

## 예제 출력 2 복사

```
55
```

## 풀이
<div class="notice--danger" markdown="1">
**<u>DP 문제임을 파악하는 것이 중요</u>** <br>
처음에 보면 DP 문제인지도 알기 힘듦 <br>
점화식을 정의할 때 가장 왼쪽 위의 사각형을 어느 방식으로 채우느냐에 따라서 나머지를 채울 수 있는 경우의 수가 달라지게 됨 <br>
그리고 가장 왼쪽 위의 칸을 채울 수 있는 경우의 수는 2x1, 1x2 두 가지밖에 없으므로 <br>
결국 2경우만 점화식 항으로 나타내면 됨 <br>
![Pasted image 20231030080156.png](/assets/images/algorithm/Pasted image 20231030080156.png) <br>

사실 그렇게 쉽지는 않은데 많이 풀어서 익숙해져야 함
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int dp[1005];   //(2 * i) 사각형에 사각형을 배치할 수 있는 경우의 수

int main(void) 
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    
    int n;
    cin >> n;

    //초기값
    dp[1] = 1;
    dp[2] = 2;

    //점화식
    for (int i = 3; i <= n; i++)
        dp[i] = (dp[i - 1] + dp[i - 2]) % 10007;

    cout << dp[n];

    return 0;
}
{% endhighlight %}