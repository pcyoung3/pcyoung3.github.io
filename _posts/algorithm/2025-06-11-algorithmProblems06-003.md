---
layout: single
title: 백준 2493번 - 탑
categories: algorithmProblems
tags: [algorithm, stack]

toc: true
toc_sticky: true
---

## 문제
[백준 2493번 문제](https://www.acmicpc.net/problem/2493)

KOI 통신연구소는 레이저를 이용한 새로운 비밀 통신 시스템 개발을 위한 실험을 하고 있다. 실험을 위하여 일직선 위에 N개의 높이가 서로 다른 탑을 수평 직선의 왼쪽부터 오른쪽 방향으로 차례로 세우고, 각 탑의 꼭대기에 레이저 송신기를 설치하였다. 모든 탑의 레이저 송신기는 레이저 신호를 지표면과 평행하게 수평 직선의 왼쪽 방향으로 발사하고, 탑의 기둥 모두에는 레이저 신호를 수신하는 장치가 설치되어 있다. 하나의 탑에서 발사된 레이저 신호는 가장 먼저 만나는 단 하나의 탑에서만 수신이 가능하다. 

예를 들어 높이가 6, 9, 5, 7, 4인 다섯 개의 탑이 수평 직선에 일렬로 서 있고, 모든 탑에서는 주어진 탑 순서의 반대 방향(왼쪽 방향)으로 동시에 레이저 신호를 발사한다고 하자. 그러면, 높이가 4인 다섯 번째 탑에서 발사한 레이저 신호는 높이가 7인 네 번째 탑이 수신을 하고, 높이가 7인 네 번째 탑의 신호는 높이가 9인 두 번째 탑이, 높이가 5인 세 번째 탑의 신호도 높이가 9인 두 번째 탑이 수신을 한다. 높이가 9인 두 번째 탑과 높이가 6인 첫 번째 탑이 보낸 레이저 신호는 어떤 탑에서도 수신을 하지 못한다.

탑들의 개수 N과 탑들의 높이가 주어질 때, 각각의 탑에서 발사한 레이저 신호를 어느 탑에서 수신하는지를 알아내는 프로그램을 작성하라. 

## 입력

첫째 줄에 탑의 수를 나타내는 정수 N이 주어진다. N은 1 이상 500,000 이하이다. 둘째 줄에는 N개의 탑들의 높이가 직선상에 놓인 순서대로 하나의 빈칸을 사이에 두고 주어진다. 탑들의 높이는 1 이상 100,000,000 이하의 정수이다.

## 출력

첫째 줄에 주어진 탑들의 순서대로 각각의 탑들에서 발사한 레이저 신호를 수신한 탑들의 번호를 하나의 빈칸을 사이에 두고 출력한다. 만약 레이저 신호를 수신하는 탑이 존재하지 않으면 0을 출력한다.

## 예제 입력 1

```
5
6 9 5 7 4
```

## 예제 출력 1

```
0 0 2 2 4
```

## 풀이

{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

const int MX = 500000;
int arr[MX];
int result[MX];

int main(void)
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	int n;
	cin >> n;
	int idx = 0;
	
	while (n--)
	{
		int t;
		cin >> t;
		arr[idx++] = t;
	}

	stack<pair<int, int>> st;	//탑의 높이와 순서 저장
	for (int i = idx - 1; i >= 0; i--)
	{
		int t = arr[i];
		if (st.empty())	//아무것도 없으면 일단 넣고 시작
		{
			st.push({ t, i });
			continue;
		}
		
		while (!st.empty() && st.top().first <= t)	//수신성공
		{
			result[st.top().second] = i + 1;	//결과 배열에 인덱스 남김
			st.pop();	//수신된 탑은 스택에서 뺌
		}

		st.push({ t, i });	//다음번을 위해 현재 탑의 높이와 순서 저장
	}

	for (int i = 0; i < idx; i++)
		cout << result[i] << " ";
}
{% endhighlight %}

### 다른사람 풀이 
역방향으로 진행하지 않고 정방향으로 간단히 풀 수도 있음
{% highlight cpp linenos %}
#include <bits/stdc++.h>
using namespace std;

#define X first
#define Y second

int N;
stack<pair<int, int>> tower;

int main() 
{
    ios_base::sync_with_stdio(0);
    cin.tie(0);

    cin >> N;
    tower.push({ 100000001, 0 });   //더미용 입력
    for (int i = 1; i <= N; i++)
    {
        int height;
        cin >> height;
        
        //현재 들어온 안테나 vs 전 안테나들
        //전 안테나가 더 길면 스택에 남겨두고 짧으면 스택에서 뺀다
        while (tower.top().X < height)
            tower.pop();
        
        //스택에서 현재 길이보다 긴게 있었다면 값이 출력 없었으면 더미용 0이 출력
        cout << tower.top().Y << " ";
        tower.push({ height, i });//현재 안테나 스택에 추가
    }
}
{% endhighlight %}
