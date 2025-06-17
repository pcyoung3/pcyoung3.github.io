---
layout: single
title: 백준 15650번 - N과 M (2)
categories: algorithmProblems
tags: [algorithm, backtracking]
toc: true
toc_sticky: true
---

## 문제
[백준 15650번 문제](https://www.acmicpc.net/problem/15650)

자연수 N과 M이 주어졌을 때, 아래 조건을 만족하는 길이가 M인 수열을 모두 구하는 프로그램을 작성하시오.

- 1부터 N까지 자연수 중에서 중복 없이 M개를 고른 수열
- 고른 수열은 오름차순이어야 한다.

## 입력

첫째 줄에 자연수 N과 M이 주어진다. (1 ≤ M ≤ N ≤ 8)

## 출력

한 줄에 하나씩 문제의 조건을 만족하는 수열을 출력한다. 중복되는 수열을 여러 번 출력하면 안되며, 각 수열은 공백으로 구분해서 출력해야 한다.

수열은 사전 순으로 증가하는 순서로 출력해야 한다.

## 예제 입력 1 복사

3 1

## 예제 출력 1 복사

1
2
3

## 예제 입력 2 복사

4 2

## 예제 출력 2 복사

1 2
1 3
1 4
2 3
2 4
3 4

## 예제 입력 3 복사

4 4

## 예제 출력 3 복사

1 2 3 4
   
## 풀이
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int n, m;
int arr[10];
bool isUsed[10];

void BackTracking(int length)
{
    if (length == m)    //탈출조건
    {
        for (int i = 0; i < m; i++)
            cout << arr[i] << ' ';
        cout << '\n';
        return;
    }

    //1~n까지 순회
    for (int i = 1; i <= n; ++i)
    {
        if (isUsed[i])  //이미 사용한 숫자는 중복으로 사용하지 않음
            continue;
        if (length > 0 && i < arr[length - 1]) //바로 전 요소를 확인 후 오름차순이 아니면 거름
            continue;

        arr[length] = i;
        isUsed[i] = 1;
        BackTracking(length + 1);
        isUsed[i] = 0;
    }
}

int main() 
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    cin >> n >> m;
    BackTracking(0);
}
{% endhighlight %}
   
## 다른풀이
next_permutation 사용
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int N, M;
vector<int> a;

int main(void) 
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cin >> N >> M;

    // M의 개수만큼 0을 넣어줌
    // N = 4, M = 2 일 경우 {0, 0, 1, 1}
    for (int i = 0; i < N; ++i)
        a.push_back(i < M ? 0 : 1);

    // Vector의 순서가 계속 바뀜
    // {0, 0, 1, 1} -> {0, 1, 0, 1} -> {0, 1, 1, 0} -> {1, 0, 0, 1} -> ...
    // Vector를 순회하면서 0일 때만 출력
    do 
    {
        for (int i = 0; i < N; ++i)
        {
            if (a[i] == 0)
                cout << i + 1 << ' ';
        }
        cout << '\n';
    } while (next_permutation(a.begin(), a.end()));
}
{% endhighlight %}
