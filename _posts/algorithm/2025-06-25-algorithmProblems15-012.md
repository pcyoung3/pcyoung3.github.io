---
layout: single
title: 백준 11053번 - 가장 긴 증가하는 부분 수열
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
---

## 문제
[백준 11053번 문제](https://www.acmicpc.net/problem/11053) <br>
수열 A가 주어졌을 때, 가장 긴 증가하는 부분 수열을 구하는 프로그램을 작성하시오.

예를 들어, 수열 A = {10, 20, 10, 30, 20, 50} 인 경우에 가장 긴 증가하는 부분 수열은 A = {**10**, **20**, 10, **30**, 20, **50**} 이고, 길이는 4이다.

## 입력

첫째 줄에 수열 A의 크기 N (1 ≤ N ≤ 1,000)이 주어진다.

둘째 줄에는 수열 A를 이루고 있는 Ai가 주어진다. (1 ≤ Ai ≤ 1,000)

## 출력

첫째 줄에 수열 A의 가장 긴 증가하는 부분 수열의 길이를 출력한다.

## 예제 입력 1 복사

```
6
10 20 10 30 20 50
```

## 예제 출력 1 복사

```
4
```

## 풀이
<div class="notice--primary" markdown="1">
**<u>가장 큰 부분수열과 동일한 문제</u>** <br>
[가장 큰 증가하는 부분 수열](/algorithmproblems/algorithmProblems15-013/)과 동일한 패턴
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int dp[1005]; //n번째 수를 포함한 가장 긴 증가하는 부분수열의 길이
int arr[1005];

int main(void)
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    int n;
    cin >> n;
    
    for (int i = 0; i < n; i++)
    {
        cin >> arr[i];
        dp[i] = 1;  //초기값 설정 -> 모든 수열의 길이는 자기자신만 포함한 1로 초기화
    }

    for (int i = 0; i < n; i++)
    {
        for (int j = 0; j < i; j++)
        {
            if (arr[i] > arr[j])
                dp[i] = max(dp[i], dp[j] + 1);  //연속하는 수열일 경우 전 수열중에서 가장 길이가 긴 수열 + 1
        }
    }

    cout << *max_element(dp, dp + n);

    return 0;
}
{% endhighlight %}