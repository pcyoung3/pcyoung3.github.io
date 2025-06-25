---
layout: single
title: 백준 14890번 - 경사로
categories: algorithmProblems
tags: [algorithm, simulation]
toc: true
toc_sticky: true
---

## 문제
[백준 14890번 문제](https://www.acmicpc.net/problem/14890) <br>
크기가 N×N인 지도가 있다. 지도의 각 칸에는 그 곳의 높이가 적혀져 있다.

오늘은 이 지도에서 지나갈 수 있는 길이 몇 개 있는지 알아보려고 한다. 길이란 한 행 또는 한 열 전부를 나타내며, 한쪽 끝에서 다른쪽 끝까지 지나가는 것이다.

길을 지나갈 수 있으려면 길에 속한 모든 칸의 높이가 모두 같아야 한다. 또는, 경사로를 놓아서 지나갈 수 있는 길을 만들 수 있다. 경사로는 높이가 항상 1이며, 길이는 L이다. 또, 개수는 매우 많아 부족할 일이 없다. 경사로는 낮은 칸과 높은 칸을 연결하며, 아래와 같은 조건을 만족해야한다.

- 경사로는 낮은 칸에 놓으며, L개의 연속된 칸에 경사로의 바닥이 모두 접해야 한다.
- 낮은 칸과 높은 칸의 높이 차이는 1이어야 한다.
- 경사로를 놓을 낮은 칸의 높이는 모두 같아야 하고, L개의 칸이 연속되어 있어야 한다.

지도가 주어졌을 때, 지나갈 수 있는 길의 개수를 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 N (2 ≤ N ≤ 100)과 L (1 ≤ L ≤ N)이 주어진다. 둘째 줄부터 N개의 줄에 지도가 주어진다. 각 칸의 높이는 10보다 작거나 같은 자연수이다.

## 출력

첫째 줄에 지나갈 수 있는 길의 개수를 출력한다.

## 예제 입력 1 복사

```
6 2
3 3 3 3 3 3
2 3 3 3 3 3
2 2 2 3 2 3
1 1 1 2 2 2
1 1 1 3 3 1
1 1 2 3 3 2
```

## 예제 출력 1 복사

3

## 풀이

{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int board[102][102];
int n, l;

bool CheckPossiblePath(vector<pair<int, bool>>& path);
bool ConstructUphill(vector<pair<int, bool>>& path, int idx);
bool ConstructDownhill(vector<pair<int, bool>>& path, int idx);

int main(void)
{
	ios::sync_with_stdio(0);
	cin.tie(0);

	cin >> n >> l;
	for (int i = 0; i < n; i++)
		for (int j = 0; j < n; j++)
			cin >> board[i][j];

	int ans = 0;

	//세로줄
	for (int j = 0; j < n; j++)
	{
		vector<pair<int, bool>> path;
		for (int i = 0; i < n; i++)
		{
			path.emplace_back(board[i][j], false);
		}
		if (CheckPossiblePath(path))
			ans++;
	}

	//가로줄
	for (int i = 0; i < n; i++)
	{
		vector<pair<int, bool>> path;
		for (int j = 0; j < n; j++)
		{
			path.emplace_back(board[i][j], false);
		}
		if (CheckPossiblePath(path))
			ans++;
	}

	cout << ans;
}

bool CheckPossiblePath(vector<pair<int, bool>>& path)
{
	int diff;
	for (int i = 0; i < path.size() - 1; i++)
	{
		diff = path[i].first - path[i + 1].first;
		if (diff == -1)	//오르막길
		{
			if (!ConstructUphill(path, i)) 
				return false;
		}
		else if (diff == 1)	//내리막길
		{
			if (!ConstructDownhill(path, i))
				return false;
		}
		else if (diff == 0)	//경사가 없을 경우
			continue;
		else  //경사가 2 이상 차이날 경우
			return false;
	}
	return true;
}

bool ConstructUphill(vector<pair<int, bool>>& path, int idx)
{
	int index = idx;	//인덱스는 감소
	int curL = l;	//경사로 설치에 필요한 길이
	while (index >= 0 && curL > 0) //오르막길이므로 지나온 판에 경사로를 설치
	{
		if (!path[index].second)	//이미 경사로가 설치되어있으면 설치불가
			path[index].second = true;	//경사로 설치
		else
			return false;

		curL--;
		index--;
	}
	if (curL > 0)	//index가 벗어났지만 경사로 설치가 다 안됐을 경우
		return false;
	
	return true;
}

bool ConstructDownhill(vector<pair<int, bool>>& path, int idx)
{
	int index = idx + 1;	//인덱스는 증가 / 현재 내리막길이 시작되므로 경사로는 한칸 앞부터 설치
	int curL = l;	//경사로 설치에 필요한 길이
	while (index < path.size() && curL > 0)	//오르막길이므로 지나갈 판에 경사로를 설치
	{
		if (!path[index].second)	//이미 경사로가 설치되어있으면 설치불가
			path[index].second = true;	//경사로 설치
		else
			return false;

		curL--;
		index++;
	}
	if (curL > 0)	//index가 벗어났지만 경사로 설치가 다 안됐을 경우
		return false;

	return true;
}
{% endhighlight %}

## 다른풀이
{% highlight cpp linenos%}
#include <bits/stdc++.h>
using namespace std;

int n, l;
int board[102][102];

bool check(vector<int>& line) 
{
	int idx = 0;
	int cnt = 1; // 현재 보고 있는, 높이가 같은 구간의 길이

	while (idx < n - 1) 
	{
		// 높이 차이가 1보다 크면 설치 불가
		if (abs(line[idx + 1] - line[idx]) > 1) 
			return 0;
		if (line[idx] == line[idx + 1])		// 같으면 다음칸 체크
		{ 
			cnt++;
			idx++;
		}
		else if (line[idx] < line[idx + 1]) // 다음 칸이 더 높으면
		{ 
			// l보다 작아서 경사로를 놓을 수 없으면 종료
			if (cnt < l) 
				return 0;
			cnt = 1;
			idx++;
		}
		else  // 다음 칸이 더 낮으면
		{  
			// l 길이 만큼 길이 없으면 경사로 설치 불가
			if (idx + l >= n) 
				return 0;
			for (int i = idx + 1; i < idx + l; i++)
				if (line[i] != line[i + 1]) 
					return 0;
			idx = idx + l; // 경사로 설치했으면, 설치한 칸부터 다음칸과 비교
			cnt = 0;
		}
	}
	return 1;
}

int main(void) 
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	cin >> n >> l;
	for (int i = 0; i < n; i++)
		for (int j = 0; j < n; j++)
			cin >> board[i][j];

	int ans = 0;

	// 가로
	for (int i = 0; i < n; i++) 
	{
		vector<int> line;
		for (int j = 0; j < n; j++) 
			line.push_back(board[i][j]);
		ans += check(line);
	}

	// 세로
	for (int i = 0; i < n; i++)
	{
		vector<int> line;
		for (int j = 0; j < n; j++) 
			line.push_back(board[j][i]);
		ans += check(line);
	}
	cout << ans;
}
{% endhighlight %}