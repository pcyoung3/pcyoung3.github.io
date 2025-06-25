---
layout: single
title: 백준 11659번 - 구간 합 구하기 4
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
use_math: true
---

## 문제
[백준 11659번 문제](https://www.acmicpc.net/problem/11659) <br>
수 N개가 주어졌을 때, i번째 수부터 j번째 수까지 합을 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 수의 개수 N과 합을 구해야 하는 횟수 M이 주어진다. 둘째 줄에는 N개의 수가 주어진다. 수는 1,000보다 작거나 같은 자연수이다. 셋째 줄부터 M개의 줄에는 합을 구해야 하는 구간 i와 j가 주어진다.

## 출력

총 M개의 줄에 입력으로 주어진 i번째 수부터 j번째 수까지 합을 출력한다.

## 제한

- 1 ≤ N ≤ 100,000
- 1 ≤ M ≤ 100,000
- 1 ≤ i ≤ j ≤ N

## 예제 입력 1 복사

```
5 3
5 4 3 2 1
1 3
2 4
5 5
```

## 예제 출력 1 복사

```
12
9
1
```

## 풀이
<div class="notice--primary" markdown="1">
**<u>구간합을 구하는 Prefix Sum 구현 문제</u>** <br>
일반적으로 2중 for문을 써서 해결하면 문제의 범위인 $100,000^2$ 이 되어버리기 때문에 시간복잡도 때문에 해결 불가능 <br>

* `D[i] = A[1] ~ A[i] 까지의 합` <br>
![Pasted image 20231101081641.png](/assets/images/algorithm/Pasted image 20231101081641.png) <br>
* `A[i] ~ A[j] 의 구간합` <br>
![Pasted image 20231101081718.png](/assets/images/algorithm/Pasted image 20231101081718.png)
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int dp[100005];   //dp[n] = dp[1] ~ dp[n] 까지의 합
int arr[100005];  //들어오는 숫자 저장 배열

int main(void) 
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    int n, m;
    cin >> n >> m;

    for (int i = 1; i <= n; i++)
        cin >> arr[i];

    //초기값 설정(dp[0]는 전역변수에서 선언해서 자동 0 초기화)
    dp[1] = arr[1];

    //점화식
    for (int i = 2; i <= n; i++)
        dp[i] = dp[i - 1] + arr[i];
    
    int mn, mx;
    while (m--)
    {
        cin >> mn >> mx;
        cout << dp[mx] - dp[mn - 1] << "\n";
    }

    return 0;
}
{% endhighlight %}