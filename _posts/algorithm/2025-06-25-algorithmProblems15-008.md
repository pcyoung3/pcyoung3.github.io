---
layout: single
title: 백준 1003번 - 피보나치 함수
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
---

## 문제
[백준 1003번 문제](https://www.acmicpc.net/problem/1003) <br>
다음 소스는 N번째 피보나치 수를 구하는 C++ 함수이다.

{% highlight cpp linenos%}
int fibonacci(int n) {
    if (n == 0) {
        printf("0");
        return 0;
    } else if (n == 1) {
        printf("1");
        return 1;
    } else {
        return fibonacci(n‐1) + fibonacci(n‐2);
    }
}
{% endhighlight %}

`fibonacci(3)`을 호출하면 다음과 같은 일이 일어난다.

- `fibonacci(3)`은 `fibonacci(2)`와 `fibonacci(1)` (첫 번째 호출)을 호출한다.
- `fibonacci(2)`는 `fibonacci(1)` (두 번째 호출)과 `fibonacci(0)`을 호출한다.
- 두 번째 호출한 `fibonacci(1)`은 1을 출력하고 1을 리턴한다.
- `fibonacci(0)`은 0을 출력하고, 0을 리턴한다.
- `fibonacci(2)`는 `fibonacci(1)`과 `fibonacci(0)`의 결과를 얻고, 1을 리턴한다.
- 첫 번째 호출한 `fibonacci(1)`은 1을 출력하고, 1을 리턴한다.
- `fibonacci(3)`은 `fibonacci(2)`와 `fibonacci(1)`의 결과를 얻고, 2를 리턴한다.

1은 2번 출력되고, 0은 1번 출력된다. N이 주어졌을 때, `fibonacci(N)`을 호출했을 때, 0과 1이 각각 몇 번 출력되는지 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 테스트 케이스의 개수 T가 주어진다.

각 테스트 케이스는 한 줄로 이루어져 있고, N이 주어진다. N은 40보다 작거나 같은 자연수 또는 0이다.

## 출력

각 테스트 케이스마다 0이 출력되는 횟수와 1이 출력되는 횟수를 공백으로 구분해서 출력한다.

## 예제 입력 1 복사

```
3
0
1
3
```

## 예제 출력 1 복사

```
1 0
0 1
1 2
```

## 예제 입력 2 복사

```
2
6
22
```

## 예제 출력 2 복사

```
5 8
10946 17711
```

## 풀이
<div class="notice" markdown="1">
**<u>2차원 배열로 0과 1의 케이스를 분리</u>** <br>
![KakaoTalk_20231104_185915819.jpg](/assets/images/algorithm/KakaoTalk_20231104_185915819.jpg)
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int dp[42][2];//dp[i][0] : fibonacci(i)에서 0이 나오는 횟수, dp[i][1] : fibonacci(i)에서 1이 나오는 횟수
int t;
int mx; //이전 testcase에서 연산한 것은 또 연산하지 않도록 예외처리(없어도 되긴 함)

int main(void) 
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    cin >> t;

    //초기값 채우기
    dp[0][0] = 1;
    dp[0][1] = 0;
    dp[1][0] = 0;
    dp[1][1] = 1;
    mx = 1; //채워져있는 인덱스는 1

    while (t--)
    {
        int n;
        cin >> n;

        if (n > mx)
        {
            //점화식 찾기
            for (int i = mx + 1; i <= n; i++)   //mx 다음 인덱스부터 실행
            {
                dp[i][0] = dp[i - 1][0] + dp[i - 2][0];
                dp[i][1] = dp[i - 1][1] + dp[i - 2][1];
            }
			mx = n;
		}

        cout << dp[n][0] << ' ' << dp[n][1] << "\n";
    }

    return 0;
}
{% endhighlight %}