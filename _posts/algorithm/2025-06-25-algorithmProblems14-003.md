---
layout: single
title: 백준 13335번 - 트럭
categories: algorithmProblems
tags: [algorithm, simulation]
toc: true
toc_sticky: true
---

## 문제
[백준 13335번 문제](https://www.acmicpc.net/problem/13335) <br>

강을 가로지르는 하나의 차선으로 된 다리가 하나 있다. 이 다리를 n 개의 트럭이 건너가려고 한다. 트럭의 순서는 바꿀 수 없으며, 트럭의 무게는 서로 같지 않을 수 있다. 다리 위에는 단지 w 대의 트럭만 동시에 올라갈 수 있다. 다리의 길이는 w 단위길이(unit distance)이며, 각 트럭들은 하나의 단위시간(unit time)에 하나의 단위길이만큼만 이동할 수 있다고 가정한다. 동시에 다리 위에 올라가 있는 트럭들의 무게의 합은 다리의 최대하중인 L보다 작거나 같아야 한다. 참고로, 다리 위에 완전히 올라가지 못한 트럭의 무게는 다리 위의 트럭들의 무게의 합을 계산할 때 포함하지 않는다고 가정한다.

예를 들어, 다리의 길이 w는 2, 다리의 최대하중 L은 10, 다리를 건너려는 트럭이 트럭의 무게가 [7, 4, 5, 6]인 순서대로 다리를 오른쪽에서 왼쪽으로 건넌다고 하자. 이 경우 모든 트럭이 다리를 건너는 최단시간은 아래의 그림에서 보는 것과 같이 8 이다.

![13335_pro_1.png](/assets/images/algorithm/13335_pro_1.png)
Figure 1. 본문의 예에 대해 트럭들이 다리를 건너는 과정.

다리의 길이와 다리의 최대하중, 그리고 다리를 건너려는 트럭들의 무게가 순서대로 주어졌을 때, 모든 트럭이 다리를 건너는 최단시간을 구하는 프로그램을 작성하라.

## 입력

입력 데이터는 표준입력을 사용한다. 입력은 두 줄로 이루어진다. 입력의 첫 번째 줄에는 세 개의 정수 n (1 ≤ n ≤ 1,000) , w (1 ≤ w ≤ 100) and L (10 ≤ L ≤ 1,000)이 주어지는데, n은 다리를 건너는 트럭의 수, w는 다리의 길이, 그리고 L은 다리의 최대하중을 나타낸다. 입력의 두 번째 줄에는 n개의 정수 a1, a2, ⋯ , an (1 ≤ ai ≤ 10)가 주어지는데, ai는 i번째 트럭의 무게를 나타낸다.

## 출력

출력은 표준출력을 사용한다. 모든 트럭들이 다리를 건너는 최단시간을 출력하라.

## 예제 입력 1 복사

```
4 2 10
7 4 5 6
```

## 예제 출력 1 복사

8

## 예제 입력 2 복사

```
1 100 100
10
```

## 예제 출력 2 복사

101

## 예제 입력 3 복사

```
10 100 100
10 10 10 10 10 10 10 10 10 10
```

## 예제 출력 3 복사

110

## 풀이

<div class="notice--info" markdown="1">
**<u>버스가 들어가는 것과 나가는 것이 동시에 일어나기 때문에 그 부분을 염두해야 함</u>**
</div>

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

#define WEIGHT first
#define DIST second

queue<pair<int, int>> before;
deque<pair<int, int>> bridge;
int n, w, l;
int curWeight;
int ans;

bool Check();
void ExitTruckOnBridge();
void EnterTruckToBridge();
void MoveTruckOnBridge();

//Debug
void PrintBridge();

int main(void) 
{
    ios::sync_with_stdio(0);
    cin.tie(0);

    cin >> n >> w >> l;
    for (int i = 0; i < n; i++)
    {
        int inputW;
        cin >> inputW;
        before.push({ inputW, w });
    }

    curWeight = l;

    while (Check()) //대기하는 트럭이나 다리위의 트럭이 없을 때까지 반복
    {
        ExitTruckOnBridge();    //트럭이 다리를 나가는 경우
        EnterTruckToBridge();   //트럭이 다리로 들어오는 경우
        MoveTruckOnBridge();    //트럭이 다리 위에서 움직임
        //PrintBridge();    //Debug
        ans++;
    }
    
    cout << ans;
}

bool Check()
{
    return !(before.empty() && bridge.empty());
}

void ExitTruckOnBridge()
{
    if (bridge.empty())
        return;

	auto firstTruck = bridge.front();
	if (firstTruck.DIST < 0)    //트럭이 다리 위로 나간 경우
	{
		curWeight += firstTruck.WEIGHT;
		bridge.pop_front();
	}
}

void EnterTruckToBridge()
{
    if (before.empty()) //다리 위로 갈 트럭이 더 이상 없을 때
        return;

    auto curTruck = before.front();
    if (curWeight >= curTruck.WEIGHT)
    {
        curWeight -= curTruck.WEIGHT;   //다리에 올라간 트럭무개 계산
        curTruck.DIST--;    //트럭은 다리에 진입하자마자 dist 를 감소
        before.pop();   //다리에 진입성공
        bridge.push_back(curTruck);
    }
}

void MoveTruckOnBridge()
{
    if (bridge.empty())
        return;

    for (auto& truck : bridge)
        truck.DIST--;   //모든 트럭 진행

    return;
}

//Debug
void PrintBridge()
{
    for (int i = 0; i < bridge.size(); i++)
        cout << bridge[i].WEIGHT << " ";
    cout << "\n";
}
{% endhighlight %}

## 다른풀이
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int n, w, L;
int a[1000]; // i번째 트럭의 무게
int cross[1000]; // cross[i] : i번째 트럭이 다리에 올라간 시간

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	cin >> n >> w >> L;
	for (int i = 0; i < n; i++) 
		cin >> a[i];

	cross[0] = 1;
	int sum = a[0];
	int idx = 0; // 현재 다리에 올라가있는 트럭 중 가장 앞에 있는 트럭의 번호

	for (int i = 1; i < n; i++) 
	{
		//가장 앞의 트럭이 다리를 벗어났거나 || 다리위의 트럭 무개의 총합이 최대값 이상일 때
		while (i - idx + 1 > w || sum + a[i] > L)
			sum -= a[idx++];	//가장 앞의 트럭 제거
		sum += a[i];	//새로운 트럭 들어옴
		if (idx == 0)	//트럭이 다리 밖으로 한대도 나가지 않은경우
			cross[i] = cross[i - 1] + 1;
		else //max(바로 앞트럭이 진입한 시간 +1, 가장 앞트럭이 들어온 시간 + 다리길이)
			cross[i] = max(cross[i - 1] + 1, cross[idx - 1] + w);
	}
	cout << cross[n - 1] + w;
}
{% endhighlight %}