---
layout: single
title: 백준 1629번 - 곱셈
categories: algorithmProblems
tags: [algorithm, recursive]
toc: true
toc_sticky: true
---

## 문제
[백준 1629번 문제](https://www.acmicpc.net/problem/1629)

자연수 A를 B번 곱한 수를 알고 싶다. 단 구하려는 수가 매우 커질 수 있으므로 이를 C로 나눈 나머지를 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 A, B, C가 빈 칸을 사이에 두고 순서대로 주어진다. A, B, C는 모두 2,147,483,647 이하의 자연수이다.

## 출력

첫째 줄에 A를 B번 곱한 수를 C로 나눈 나머지를 출력한다.

## 예제 입력 1 복사

```
10 11 12
```

## 예제 출력 1 복사

```
4
```

### 4-1. 정답코드

<div class="notice--warning" markdown="1">
**<u>미리 알아야 할 개념</u>** <br>
1. 지수법칙  <br>
![Pasted image 20230609164904.png](/assets/images/algorithm/Pasted image 20230609164904.png) <br>
 <br>
2. 모듈러 성질 중 분배법칙 <br>
![Pasted image 20230609164956.png](/assets/images/algorithm/Pasted image 20230609164956.png)
</div>

<div class="notice--warning" markdown="1">
**<u>구현 상 알아야 하는 부분</u>** <br>
![KakaoTalk_20230609_170456147.jpg](/assets/images/algorithm/KakaoTalk_20230609_170456147.jpg)
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

using ll = long long;

ll pow(ll a, ll b, ll m)
{
    if (b == 1) //Base Condition 탈출구문
        return a % m;

    //분할정복
    ll val = pow(a, b / 2, m);
    val = val * val % m;    //모듈러 성질
    if (b % 2 == 0) //짝수, 홀수 분할
        return val;
    return val * a % m;
}

int main(void)
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    ll a, b, c;
    cin >> a >> b >> c;
    cout << pow(a, b, c);
}
{% endhighlight %}
