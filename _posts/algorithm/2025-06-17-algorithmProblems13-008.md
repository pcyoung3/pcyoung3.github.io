---
layout: single
title: 백준 15655번 - N과 M (6)
categories: algorithmProblems
tags: [algorithm, backtracking]
toc: true
toc_sticky: true
---

## 문제
[백준 15655번 문제](https://www.acmicpc.net/problem/15655)

N개의 자연수와 자연수 M이 주어졌을 때, 아래 조건을 만족하는 길이가 M인 수열을 모두 구하는 프로그램을 작성하시오. N개의 자연수는 모두 다른 수이다.

- N개의 자연수 중에서 M개를 고른 수열
- 고른 수열은 오름차순이어야 한다.

## 입력

첫째 줄에 N과 M이 주어진다. (1 ≤ M ≤ N ≤ 8)

둘째 줄에 N개의 수가 주어진다. 입력으로 주어지는 수는 10,000보다 작거나 같은 자연수이다.

## 출력

한 줄에 하나씩 문제의 조건을 만족하는 수열을 출력한다. 중복되는 수열을 여러 번 출력하면 안되며, 각 수열은 공백으로 구분해서 출력해야 한다.

수열은 사전 순으로 증가하는 순서로 출력해야 한다.

## 예제 입력 1 복사

3 1
4 5 2

## 예제 출력 1 복사
```
2
4
5
```

## 예제 입력 2 복사

4 2
9 8 7 1

## 예제 출력 2 복사
```
1 7
1 8
1 9
7 8
7 9
8 9
```

## 예제 입력 3 복사

4 4
1231 1232 1233 1234

## 예제 출력 3 복사
```
1231 1232 1233 1234
```

## 풀이
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int n, m;
int arr[10];
int val[10];
bool isUsed[10];

//오름차순을 위해서 현재 진행중인 index를 변수로 받음
void BackTracking(int length, int idx)
{
    if (length == m)
    {
        for (int i = 0; i < m; i++)
            cout << arr[i] << " ";
        cout << "\n";
        return;
    }

    for (int i = idx; i < n; i++)
    {
        if (isUsed[i])
            continue;

        arr[length] = val[i];
        isUsed[i] = 1;
        BackTracking(length + 1, i + 1); //현재 진행중인 index+1을 넘김
        isUsed[i] = 0;
    }
}

int main(void) 
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    cin >> n >> m;
    for (int i = 0; i < n; i++)
        cin >> val[i];

    sort(val, val + n, [](int a, int b)
        {
            return a < b;
        });

    BackTracking(0, 0);

}
{% endhighlight %}
   
## 다른풀이
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int n, m;
int arr[10];
bool isused[10];
int num[10];

void func(int k) // 현재 k개까지 수를 택했음.
{ 
    if (k == m) // m개를 모두 택했으면
    { 
        for (int i = 0; i < m; i++)
            cout << num[arr[i]] << ' '; // arr에 기록해둔 인덱스에 대응되는 수를 출력
        cout << '\n';
        return;
    }

    int st = 0; // 시작지점, k = 0일 때에는 st = 0
    if (k != 0) 
        st = arr[k - 1] + 1; // k != 0일 경우 st = arr[k-1]+1
    //arr[k]가 안되는 이유는 아래에서 arr[k]값을 채우기 때문

    for (int i = st; i < n; i++) 
    {
        if (!isused[i]) // 아직 i가 사용되지 않았으면
        { 
            arr[k] = i; // k번째 인덱스를 i로 정함
            isused[i] = 1; // i를 사용되었다고 표시
            func(k + 1); // 다음 인덱스를 정하러 한 단계 더 들어감
            isused[i] = 0; // k번째 인덱스를 i로 정한 모든 경우에 대해 다 확인했으니 i를 이제 사용되지않았다고 명시함.
        }
    }
}

int main(void) {
    ios::sync_with_stdio(0);
    cin.tie(0);
    cin >> n >> m;
    for (int i = 0; i < n; i++) 
        cin >> num[i];
    sort(num, num + n); // 수 정렬
    func(0);
}
{% endhighlight %}
