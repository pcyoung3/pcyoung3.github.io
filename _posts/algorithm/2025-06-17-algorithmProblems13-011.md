---
layout: single
title: 백준 15663번 - N과 M (9)
categories: algorithmProblems
tags: [algorithm, backtracking]
toc: true
toc_sticky: true
---

## 문제
[백준 15663번 문제](https://www.acmicpc.net/problem/15663)

N개의 자연수와 자연수 M이 주어졌을 때, 아래 조건을 만족하는 길이가 M인 수열을 모두 구하는 프로그램을 작성하시오.

- N개의 자연수 중에서 M개를 고른 수열

## 입력

첫째 줄에 N과 M이 주어진다. (1 ≤ M ≤ N ≤ 8)

둘째 줄에 N개의 수가 주어진다. 입력으로 주어지는 수는 10,000보다 작거나 같은 자연수이다.

## 출력

한 줄에 하나씩 문제의 조건을 만족하는 수열을 출력한다. 중복되는 수열을 여러 번 출력하면 안되며, 각 수열은 공백으로 구분해서 출력해야 한다.

수열은 사전 순으로 증가하는 순서로 출력해야 한다.

## 예제 입력 1 복사

3 1
4 4 2

## 예제 출력 1 복사
```
2
4
```

## 예제 입력 2 복사

4 2
9 7 9 1

## 예제 출력 2 복사
```
1 7
1 9
7 1
7 9
9 1
9 7
9 9
```

## 예제 입력 3 복사

4 4
1 1 1 1

## 예제 출력 3 복사
```
1 1 1 1
```
   
## 풀이
{% highlight cpp linenos%}
#include <bits/stdc++.h>
#include <unordered_set>

using namespace std;

int val[10];
int arr[10];
bool isUsed[10];
int n, m;
unordered_set<string> compareResult;

void BackTracking(int length)
{
	if (length == m)
	{
        //수열을 문자열로 치환
		string resultString = "";
        for (int i = 0; i < m; i++)
		    resultString += (char)arr[i];
        //Hash Set에 수열을 찾아서 있는지를 확인(중복수열확인)
        if (compareResult.find(resultString) != compareResult.end())
            return;
        //없다면 수열 삽입
        compareResult.insert(resultString);

		for (auto ch : resultString)
			cout << (int)ch << " ";
        
        cout << "\n";
        return;
    }

    for (int i = 0; i < n; i++)
    {
        if (isUsed[i])
            continue;
        arr[length] = val[i];
        isUsed[i] = 1;
        BackTracking(length + 1);
        isUsed[i] = 0;
    }
}

int main(void) {
    ios::sync_with_stdio(0);
    cin.tie(0);

    cin >> n >> m;
    for (int i = 0; i < n; i++)
        cin >> val[i];
    sort(val, val + n);

    BackTracking(0);
}
{% endhighlight %}
   
## 다른풀이

![Pasted image 20230715210426.png](/assets/images/algorithm/Pasted image 20230715210426.png)

중복수열의 생략은 같은 숫자가 반복될 때만 발생

![KakaoTalk_20230715_210220008.jpg](/assets/images/algorithm/KakaoTalk_20230715_210220008.jpg)

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int n, m;
int arr[10];
int num[10];
bool isused[10];

void func(int k) { // 현재 k개까지 수를 택했음.
  if (k == m) {
    for (int i = 0; i < m; ++i) {
      cout << arr[i] << ' ';
    }
    cout << '\n';
    return;
  }
  int tmp = 0;  // 중복 수열인지 확인 하기 위해 필요한 임시 변수
  for (int i = 0; i < n; ++i) {
    if (!isused[i] && tmp != num[i]){ // 이전 수열의 마지막 항과 새로운 수열의 마지막 항이 같으면 중복 수열
      isused[i] = true;
      arr[k] = num[i];
      tmp = arr[k];
      func(k + 1);
      isused[i] = false;
    }
  }
}

int main(void) {
  ios::sync_with_stdio(0);
  cin.tie(0);
  cin >> n >> m;
  for (int i = 0; i < n; ++i)
    cin >> num[i];
  sort(num, num + n);
  func(0);
}
{% endhighlight %}
