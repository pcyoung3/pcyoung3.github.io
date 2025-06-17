---
layout: single
title: 백준 1074번 - Z
categories: algorithmProblems
tags: [algorithm, recursive]
toc: true
toc_sticky: true
---

## 문제
[백준 1074번 문제](https://www.acmicpc.net/problem/1074)

한수는 크기가 2N × 2N인 2차원 배열을 Z모양으로 탐색하려고 한다. 예를 들어, 2×2배열을 왼쪽 위칸, 오른쪽 위칸, 왼쪽 아래칸, 오른쪽 아래칸 순서대로 방문하면 Z모양이다.

![z_1.png](/assets/images/algorithm/z_1.png)

N > 1인 경우, 배열을 크기가 2N-1 × 2N-1로 4등분 한 후에 재귀적으로 순서대로 방문한다.

다음 예는 22 × 22 크기의 배열을 방문한 순서이다.

![z_2.png](/assets/images/algorithm/z_2.png)

N이 주어졌을 때, r행 c열을 몇 번째로 방문하는지 출력하는 프로그램을 작성하시오.

다음은 N=3일 때의 예이다.

![z_3.png](/assets/images/algorithm/z_3.png)

## 입력

첫째 줄에 정수 N, r, c가 주어진다.

## 출력

r행 c열을 몇 번째로 방문했는지 출력한다.

## 제한

- 1 ≤ N ≤ 15
- 0 ≤ r, c < 2N

## 예제 입력 1 복사

```
2 3 1
```

## 예제 출력 1 복사

```
11
```

## 예제 입력 2 복사

```
3 7 7
```

## 예제 출력 2 복사

```
63
```

## 예제 입력 3 복사

```
1 0 0
```

## 예제 출력 3 복사

```
0
```

## 예제 입력 4 복사

```
4 7 7
```

## 예제 출력 4 복사

```
63
```

## 예제 입력 5 복사

```
10 511 511
```

## 예제 출력 5 복사

```
262143
```

## 예제 입력 6 복사

```
10 512 512
```

## 예제 출력 6 복사

```
786432
```
   
   
### 6-1. 정답코드

<div class="notice--warning" markdown="1">
**<u>n이 k일 때의 결과를 가지고 k+1일 때에 써먹을 수 있다.</u>** <br>
k가 참이라고 가정할 때 k+1도 참 <br>
: 수학적 귀납법 <br>
 <br>
즉, 재귀를 쓰는 문제 
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int func(int n, int r, int c) {
    //Base condition -> 지금까지 더한 half 값이 결과물
    if (n == 0) return 0;

    //현재 사각형의 절반의 크기
    int half = 1 << (n - 1);

    //1  2
    //3  4 사각형일 때
    //r c 모두 사각형 절반보다 작다면 사각형은 1번
    if (r < half && c < half) return func(n - 1, r, c);

    //c가 절반보다 크다면 인덱스로 1번 사각형(half * half)을 더해주고
    //2번 사각형만 보면 되니 나머지 사각형은 볼 필요가 없으므로 c-half를 해줌
    //이럴경우 r와 c이 점점 작은 사각형으로 맞춰들어감
    if (r < half && c >= half) return half * half + func(n - 1, r, c - half);

    //3번 사각형
    if (r >= half && c < half) return 2 * half * half + func(n - 1, r - half, c);

    //4번 사각형
    return 3 * half * half + func(n - 1, r - half, c - half);
}

int main(void) {
    ios::sync_with_stdio(0);
    cin.tie(0);
    int n, r, c;
    cin >> n >> r >> c;
    cout << func(n, r, c);
}
{% endhighlight %}

   
### 6-3. 다른 코드
{% highlight cpp linenos%}
#include <iostream>
using namespace std;

int n, r, c;
int ans;

void Z(int y, int x, int size)
{
    //(r, c) 칸을 찾음 -> Base Condition 1
    if (y == r && x == c)
    {
        cout << ans << '\n';
        return;
    }

    //r,c가 현재 사각형에 존재한다면 -> Base Condition 2
    if (r < y + size && r >= y && c < x + size && c >= x)
    {
        //1번 사각형 탐색
        Z(y, x, size / 2);
        //2번 사각형 탐색
        Z(y, x + size / 2, size / 2);
        //3번 사각형 탐색
        Z(y + size / 2, x, size / 2);
        //4번 사각형 탐색
        Z(y + size / 2, x + size / 2, size / 2);
    }
    else
    {
        //차례대로 1번 2번 3번 4번 사각형을 본 뒤
        //앞 사각형에 (r, c)가 없으므로 앞 사각형만큼 더해준다
        //프로그램이 종료될 때 정답 이상의 ans가 나올 수 있지만 출력은 되지 않음
        ans += size * size;
    }
}
int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    cin >> n >> r >> c;
    Z(0, 0, (1 << n));// 2^n 승을 (0, 0) 부터 전위순회
    return 0;
}
{% endhighlight %}
