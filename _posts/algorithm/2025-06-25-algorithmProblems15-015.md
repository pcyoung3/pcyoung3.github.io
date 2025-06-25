---
layout: single
title: 백준 11727번 - 2×n 타일링 2
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
---

## 문제
[백준 11727번 문제](https://www.acmicpc.net/problem/11727) <br>
2×n 직사각형을 1×2, 2×1과 2×2 타일로 채우는 방법의 수를 구하는 프로그램을 작성하시오.

아래 그림은 2×17 직사각형을 채운 한가지 예이다.

![11727_p_01.gif](/assets/images/algorithm/11727_p_01.gif)

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
3
```

## 예제 입력 2 복사

```
8
```

## 예제 출력 2 복사

```
171
```

## 예제 입력 3 복사

```
12
```

## 예제 출력 3 복사

```
2731
```

## 풀이
<div class="notice--info" markdown="1">
**<u>기존 2xn 타일링에서 타일이 하나 추가된 형태</u>** <br>
[2×n 타일링 문제](/algorithmproblems/algorithmProblems15-005/)를 참고하고 해당 문제가 이해됐다면 <br>
2x2 형태가 하나 추가되었으므로 그 부분만 처리하면 해결할 수 있다
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int dp[1005];   //dp[i] : 2 x i 크기의 직사각형을 타일로 채우는 방법의 수

int main(void) 
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    int n;
    cin >> n;

    //초기값
    dp[1] = 1;
    dp[2] = 3;
    for (int i = 3; i <= n; i++)
    {
        //왼쪽 끝 부분이 2x1일 경우 + 1x2일 경우 + 2x2일 경우
        dp[i] = dp[i - 1] + dp[i - 2] + dp[i - 2];
        dp[i] %= 10007; //숫자가 커지기 때문에 매번 연산 필요
    }

    cout << dp[n];

    return 0;
}
{% endhighlight %}