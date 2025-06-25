---
layout: single
title: 백준 1932번 - 정수 삼각형
categories: algorithmProblems
tags: [algorithm, DP]
toc: true
toc_sticky: true
---

## 문제
[백준 1932번 문제](https://www.acmicpc.net/problem/1932) <br>
```
        7
      3   8
    8   1   0
  2   7   4   4
4   5   2   6   5
```

위 그림은 크기가 5인 정수 삼각형의 한 모습이다.

맨 위층 7부터 시작해서 아래에 있는 수 중 하나를 선택하여 아래층으로 내려올 때, 이제까지 선택된 수의 합이 최대가 되는 경로를 구하는 프로그램을 작성하라. 아래층에 있는 수는 현재 층에서 선택된 수의 대각선 왼쪽 또는 대각선 오른쪽에 있는 것 중에서만 선택할 수 있다.

삼각형의 크기는 1 이상 500 이하이다. 삼각형을 이루고 있는 각 수는 모두 정수이며, 범위는 0 이상 9999 이하이다.

## 입력

첫째 줄에 삼각형의 크기 n(1 ≤ n ≤ 500)이 주어지고, 둘째 줄부터 n+1번째 줄까지 정수 삼각형이 주어진다.

## 출력

첫째 줄에 합이 최대가 되는 경로에 있는 수의 합을 출력한다.

## 예제 입력 1 복사

```
5
7
3 8
8 1 0
2 7 4 4
4 5 2 6 5
```

## 예제 출력 1 복사

```
30
```

## 풀이
<div class="notice" markdown="1">
**<u>삼각형 구조는 2차원배열로 받음</u>** <br>
![KakaoTalk_20231106_201645758.jpg](/assets/images/algorithm/KakaoTalk_20231106_201645758.jpg)
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int arr[502][502];  //정수삼각형이 들어오는 배열
int dp[502][502];   //현재 위치에서 최대값

int main(void) 
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    int n;
    cin >> n;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= i; j++)
            cin >> arr[i][j];

    //초기값 셋팅
    dp[1][1] = arr[1][1];

    //점화식
    for (int i = 2; i <= n; i++)
        for (int j = 1; j <= i; j++)
            dp[i][j] = max(dp[i - 1][j - 1], dp[i - 1][j]) + arr[i][j];
	//이 경우 dp[1][0]이나 dp[1][2]과 같은 아직 안채워진 값들을 참조
	//즉, 삼각형의 양쪽 끝단은 항상 값이 한 개 비워지게 됨
    //하지만 삼각형의 양 끝단이 전역변수로 0으로 초기화되어있는 상황이라 그냥 더해도 상관없음


    //마지막줄에서 최대값
    cout << *max_element(dp[n] + 1, dp[n] + n + 1);

    return 0;
}
{% endhighlight %}