---
layout: single
title: 백준 1021번 - 회전하는 큐
categories: algorithmProblems
tags: [algorithm, deque]
toc: true
toc_sticky: true
---

## 문제
[백준 1021번 문제](https://www.acmicpc.net/problem/1021)
   
지민이는 N개의 원소를 포함하고 있는 양방향 순환 큐를 가지고 있다. 지민이는 이 큐에서 몇 개의 원소를 뽑아내려고 한다.

지민이는 이 큐에서 다음과 같은 3가지 연산을 수행할 수 있다.

1.  첫 번째 원소를 뽑아낸다. 이 연산을 수행하면, 원래 큐의 원소가 a1, ..., ak이었던 것이 a2, ..., ak와 같이 된다.
2.  왼쪽으로 한 칸 이동시킨다. 이 연산을 수행하면, a1, ..., ak가 a2, ..., ak, a1이 된다.
3.  오른쪽으로 한 칸 이동시킨다. 이 연산을 수행하면, a1, ..., ak가 ak, a1, ..., ak-1이 된다.

큐에 처음에 포함되어 있던 수 N이 주어진다. 그리고 지민이가 뽑아내려고 하는 원소의 위치가 주어진다. (이 위치는 가장 처음 큐에서의 위치이다.) 이때, 그 원소를 주어진 순서대로 뽑아내는데 드는 2번, 3번 연산의 최솟값을 출력하는 프로그램을 작성하시오.

## 입력

첫째 줄에 큐의 크기 N과 뽑아내려고 하는 수의 개수 M이 주어진다. N은 50보다 작거나 같은 자연수이고, M은 N보다 작거나 같은 자연수이다. 둘째 줄에는 지민이가 뽑아내려고 하는 수의 위치가 순서대로 주어진다. 위치는 1보다 크거나 같고, N보다 작거나 같은 자연수이다.

## 출력

첫째 줄에 문제의 정답을 출력한다.

## 예제 입력 1 복사

```
10 3
1 2 3
```

## 예제 출력 1 복사

```
0
```

## 예제 입력 2 복사

```
10 3
2 9 5
```

## 예제 출력 2 복사

```
8
```

## 풀이
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int n, m;
	cin >> n;

	deque<int> deq;
	for (int i = 1; i <= n; i++)	//1~n까지의 덱 생성
		deq.push_back(i);

	cin >> m;
	list<int> inputList;
	for (int i = 0; i < m; i++)		//뽑으려고 하는 수의 리스트
	{
		int inputNum;
		cin >> inputNum;
		inputList.push_back(inputNum);
	}

	int result = 0;

	while(inputList.size() > 0)
	{
		int leftNumber = 1;		//오른쪽(back)에 있는 원소를 왼쪽(front)으로 보내려면 찾은거리 +1 을 해줘야됨
		int rightNumber = 0;
		int idx = 0;
		while (*inputList.begin() != deq[idx])		//rightNumber : 찾는 원소와 front의 거리
		{
			idx++;
			rightNumber++;
		}

		idx = deq.size() - 1;
		while (*inputList.begin() != deq[idx])		//leftNumber : 찾는 원소와 back의 거리
		{
			idx--;
			leftNumber++;
		}

		if (rightNumber <= leftNumber)			//right와 left를 비교 후 가까운 쪽으로 계산
		{
			for (int i = 0; i < rightNumber; i++)	//right : 위에서 계산한 거리만큼 왼쪽원소를 오른쪽으로 보냄
			{
				deq.push_back(deq.front());
				deq.pop_front();
				result++;
			}
			deq.pop_front();
		}
		else
		{
			for (int i = 0; i < leftNumber; i++)	//Left : 위에서 계산한 거리만큼 오른쪽 원소를 왼쪽으로 보냄
			{
				deq.push_front(deq.back());
				deq.pop_back();
				result++;
			}
			deq.pop_front();
		}

		inputList.pop_front();
	}

	cout << result <<'\n';
}
{% endhighlight %}

## 다른사람 풀이
{% highlight cpp linenos %}
// Authored by : haneulkimdev
// Co-authored by : BaaaaaaaaaaarkingDog
// http://boj.kr/9571e70535a34702812d2a03510ac182
#include <bits/stdc++.h>
using namespace std;

int main(void) 
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    int n, m;
    cin >> n >> m;
    deque<int> DQ;

    for (int i = 1; i <= n; i++)
        DQ.push_back(i);

    int ans = 0;
    while (m--)
    {
        int t;
        cin >> t;

        int idx = find(DQ.begin(), DQ.end(), t) - DQ.begin(); // idx : t가 있는 위치
        
        while (DQ.front() != t) 
        {
            if (idx < DQ.size() - idx) 
            {
                DQ.push_back(DQ.front());
                DQ.pop_front();
            }
            else 
            {
                DQ.push_front(DQ.back());
                DQ.pop_back();
            }
            ans++;
        }
        DQ.pop_front();
    }
    cout << ans;
}
/*
20번째 줄에서, 지금은 idx가 항상 DQ.size()보다 작아서 문제가 없지만
DQ.size()는 unsigned int(or unsigned long long)이기
때문에 만약 idx가 DQ.size()보다 컸다면 overflow가 발생해
의도한대로 동작하지 않을 수 있음을 인지해야 함. 그래서 size()로
받아온 값에 대해서는 안전하게 (int)DQ.size() 로 항상 형변환을
하는 것도 괜찮음.
*/
{% endhighlight %}
