---
layout: single
title: 백준 9461번 - 파도반 수열
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
---

## 문제
[백준 9461번 문제](https://www.acmicpc.net/problem/9461) <br>
오른쪽 그림과 같이 삼각형이 나선 모양으로 놓여져 있다. 첫 삼각형은 정삼각형으로 변의 길이는 1이다. 그 다음에는 다음과 같은 과정으로 정삼각형을 계속 추가한다. 나선에서 가장 긴 변의 길이를 k라 했을 때, 그 변에 길이가 k인 정삼각형을 추가한다.

![9461_p_01.png](/assets/images/algorithm/9461_p_01.png)

파도반 수열 P(N)은 나선에 있는 정삼각형의 변의 길이이다. P(1)부터 P(10)까지 첫 10개 숫자는 1, 1, 1, 2, 2, 3, 4, 5, 7, 9이다.

N이 주어졌을 때, P(N)을 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 테스트 케이스의 개수 T가 주어진다. 각 테스트 케이스는 한 줄로 이루어져 있고, N이 주어진다. (1 ≤ N ≤ 100)

## 출력

각 테스트 케이스마다 P(N)을 출력한다.

## 예제 입력 1 복사

```
2
6
12
```

## 예제 출력 1 복사

```
3
16
```

## 풀이
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

using ul = unsigned long; //자료형이 int의 범위를 넘어갈 수 있다
ul dp[105]; //n번째 삼각형의 한변 길이

int main(void)
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    int t;
    cin >> t;

    //초기값 설정
    dp[1] = 1;
    dp[2] = 1;
    dp[3] = 1;

    while (t--)
    {
        int n;
        cin >> n;

        //점화식
        for (int i = 4; i <= n; i++)
            dp[i] = dp[i - 3] + dp[i - 2];

        cout << dp[n] << "\n";
    }
    
    return 0;
}
{% endhighlight %}
