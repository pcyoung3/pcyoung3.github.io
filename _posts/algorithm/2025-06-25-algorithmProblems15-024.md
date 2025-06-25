---
layout: single
title: 백준 1912번 - 연속합
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
---

## 문제
[백준 1912번 문제](https://www.acmicpc.net/problem/1912) <br>
n개의 정수로 이루어진 임의의 수열이 주어진다. 우리는 이 중 연속된 몇 개의 수를 선택해서 구할 수 있는 합 중 가장 큰 합을 구하려고 한다. 단, 수는 한 개 이상 선택해야 한다.

예를 들어서 10, -4, 3, 1, 5, 6, -35, 12, 21, -1 이라는 수열이 주어졌다고 하자. 여기서 정답은 12+21인 33이 정답이 된다.

## 입력

첫째 줄에 정수 n(1 ≤ n ≤ 100,000)이 주어지고 둘째 줄에는 n개의 정수로 이루어진 수열이 주어진다. 수는 -1,000보다 크거나 같고, 1,000보다 작거나 같은 정수이다.

## 출력

첫째 줄에 답을 출력한다.

## 예제 입력 1 복사

```
10
10 -4 3 1 5 6 -35 12 21 -1
```

## 예제 출력 1 복사

```
33
```

## 예제 입력 2 복사

```
10
2 1 -4 3 4 -4 6 5 -5 1
```

## 예제 출력 2 복사

```
14
```

## 예제 입력 3 복사

```
5
-1 -2 -3 -4 -5
```

## 예제 출력 3 복사

```
-1
```

## 풀이
<div class="notice" markdown="1">
**<u>현재 index의 수와 연속합을 서로 비교하고 더 큰쪽을 테이블에 채우는 식으로 해결</u>** <br>
![KakaoTalk_20231111_140821980.jpg](/assets/images/algorithm/KakaoTalk_20231111_140821980.jpg) <br>
문제에서 정의하는 연속합은 최소 한개이므로 해당 index와 이전 인덱스까지의 합을 비교
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int dp[100005]; //n번째 수를 포함한 연속합의 최댓값
int arr[100005]; //수열

int main(void)
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    int mx = -1005;
    int sum = 0;
    int n;
    cin >> n;
    for (int i = 1; i <= n; i++)
        cin >> arr[i];
    
    //초기값
    dp[1] = arr[1];

    //점화식
    for (int i = 2; i <= n; i++)
        dp[i] = max(arr[i], dp[i - 1] + arr[i]);
    
    cout << *max_element(dp + 1, dp + n + 1);

    return 0;
}
{% endhighlight %}