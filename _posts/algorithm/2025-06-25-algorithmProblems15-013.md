---
layout: single
title: 백준 11055번 - 가장 큰 증가하는 부분 수열
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
---

## 문제
[백준 11055번 문제](https://www.acmicpc.net/problem/11055) <br>
수열 A가 주어졌을 때, 그 수열의 증가하는 부분 수열 중에서 합이 가장 큰 것을 구하는 프로그램을 작성하시오.

예를 들어, 수열 A = {1, 100, 2, 50, 60, 3, 5, 6, 7, 8} 인 경우에 합이 가장 큰 증가하는 부분 수열은 A = {**1**, 100, **2**, **50**, **60**, 3, 5, 6, 7, 8} 이고, 합은 113이다.

## 입력

첫째 줄에 수열 A의 크기 N (1 ≤ N ≤ 1,000)이 주어진다.

둘째 줄에는 수열 A를 이루고 있는 Ai가 주어진다. (1 ≤ Ai ≤ 1,000)

## 출력

첫째 줄에 수열 A의 합이 가장 큰 증가하는 부분 수열의 합을 출력한다.

## 예제 입력 1 복사

```
10
1 100 2 50 60 3 5 6 7 8
```

## 예제 출력 1 복사

```
113
```

## 풀이
<div class="notice" markdown="1">
**<u>증가하는 부분 수열을 어떻게 알아낼 수 있을지가 포인트</u>** <br>
![KakaoTalk_20231113_090814385.jpg](/assets/images/algorithm/KakaoTalk_20231113_090814385.jpg)
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int dp[1005]; //n번째 수를 포함한 가장 큰 증가하는 부분수열
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
        dp[i] = arr[i]; //입력과 동시에 해당 원소값으로 초기화
    }
        
    for (int i = 0; i < n; i++) //0 ~ n 까지 진행
    {
        for (int j = 0; j < i; j++) //0 ~ i - 1 까지 진행
        {
            if (arr[i] > arr[j]) //증가하는 부분수열이므로 클 때만 확인
                dp[i] = max(dp[i], dp[j] + arr[i]); //현재 원소값 vs j번째 수열의 값 + 현재 원소값
        }
    }

    cout << *max_element(dp, dp + n);

    return 0;
}
{% endhighlight %}