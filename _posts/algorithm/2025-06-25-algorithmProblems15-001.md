---
layout: single
title: 백준 1463번 - 1로 만들기
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
use_math: true
---

## 문제
[백준 1463번 문제](https://www.acmicpc.net/problem/1463) <br>
정수 X에 사용할 수 있는 연산은 다음과 같이 세 가지 이다.

1. X가 3으로 나누어 떨어지면, 3으로 나눈다.
2. X가 2로 나누어 떨어지면, 2로 나눈다.
3. 1을 뺀다.

정수 N이 주어졌을 때, 위와 같은 연산 세 개를 적절히 사용해서 1을 만들려고 한다. 연산을 사용하는 횟수의 최솟값을 출력하시오.

## 입력

첫째 줄에 1보다 크거나 같고, $10^6$보다 작거나 같은 정수 N이 주어진다.

## 출력

첫째 줄에 연산을 하는 횟수의 최솟값을 출력한다.

## 예제 입력 1 복사

```
2
```

## 예제 출력 1 복사

```
1
```

## 예제 입력 2 복사

```
10
```

## 예제 출력 2 복사

```
3
```

## 풀이
<div class="notice--warning" markdown="1">
**<u>추가설명</u>** <br>

|                    |                    |
| ------------------ | ------------------ |
| ![1463_S_01.png](/assets/images/algorithm/1463_S_01.png) | ![1463_S_02.png](/assets/images/algorithm/1463_S_02.png) |

![KakaoTalk_20231023_225347019.jpg](/assets/images/algorithm/KakaoTalk_20231023_225347019.jpg)
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

//dp[index] : index가 1로 나누어 떨어지기 위한 최소횟수를 저장하는 테이블
int dp[1000005];

int main(void) 
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    int n;
    cin >> n;

    dp[0] = 0;  //초기값 셋팅
    dp[1] = 0;
    for (int i = 2; i <= n; i++)
    {
        dp[i] = dp[i - 1] + 1;  // -1의 경우
        if (i % 3 == 0) // 3으로 나누어떨어질 경우
            dp[i] = min(dp[i], dp[i / 3] + 1);
        if (i % 2 == 0) // 2로 나누어떨어질 경우
            dp[i] = min(dp[i], dp[i / 2] + 1);
    }

    cout << dp[n];
}
{% endhighlight %}