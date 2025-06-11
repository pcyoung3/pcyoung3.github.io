---
layout: single
title: 백준 1158번 - 요세푸스 문제
categories: algorithmProblems
tags: [algorithm, linked_list]

toc: true
toc_sticky: true
---

## 문제
[백준 1158번 문제](https://www.acmicpc.net/problem/1158)

요세푸스 문제는 다음과 같다.

1번부터 N번까지 N명의 사람이 원을 이루면서 앉아있고, 양의 정수 K(≤ N)가 주어진다. 이제 순서대로 K번째 사람을 제거한다. 한 사람이 제거되면 남은 사람들로 이루어진 원을 따라 이 과정을 계속해 나간다. 이 과정은 N명의 사람이 모두 제거될 때까지 계속된다. 원에서 사람들이 제거되는 순서를 (N, K)-요세푸스 순열이라고 한다. 예를 들어 (7, 3)-요세푸스 순열은 <3, 6, 2, 7, 5, 1, 4>이다.

N과 K가 주어지면 (N, K)-요세푸스 순열을 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 N과 K가 빈 칸을 사이에 두고 순서대로 주어진다. (1 ≤ K ≤ N ≤ 5,000)

## 출력

예제와 같이 요세푸스 순열을 출력한다.

## 예제 입력 1

```
7 3
```

## 예제 출력 1

```
<3, 6, 2, 7, 5, 1, 4>
```

---

## 풀이
원형연결리스트 문제인데 연결리스트나 큐를 통해서 풀 수 있다.


1.stl 연결리스트 사용

{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

int main(void)
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int n, k;

	list<int> order;

	cin >> n >> k;
	for (int i = 0; i < n; i++)
		order.push_back(i + 1);

	auto cursor = order.begin();
	cout << '<';
	while (order.size() > 0)
	{
		int result;
		int cur = k;
		for (int i = 0; i < cur; i++)
		{
			if(i != 0)
				cursor++;

			if (cursor == order.end())
				cursor = order.begin();
		}
		if (cursor == order.end())
			cursor = order.begin();

		cout << *cursor;
		if (order.size() != 1)
			cout << ", ";
		cursor = order.erase(cursor);
		if (cursor == order.end())
			cursor = order.begin();
	}
	cout << '>';
}
{% endhighlight %}

2.stl queue 사용

{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

int main(void) 
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    
    int n, k;
    cin >> n >> k;
    
    queue<int> Q;
    for (int i = 1; i <= n; i++) 
	    Q.push(i);
	
    cout << '<';
    while (!Q.empty()) 
    {
        for (int i = 0; i < k - 1; i++) 
        {
            Q.push(Q.front());
            Q.pop();
        }
        cout << Q.front();
        Q.pop();
        
        if (Q.size()) 
	        cout << ", ";
    }
    cout << '>';
}
{% endhighlight %}